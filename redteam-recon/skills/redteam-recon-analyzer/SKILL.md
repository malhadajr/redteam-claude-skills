---
name: redteam-recon-analyzer
description: Use quando o usuário enviar saídas de nmap, netstat ou inventário de rede para triagem de superfície de ataque e correlação de CVEs em engajamento de red team autorizado.
---

# Triagem de reconhecimento (red team autorizado)

Esta skill **analisa dados já coletados** (nmap `-oX`/`-oG`, `netstat -tulnp`, inventários). Ela não executa varredura, não gera exploração e não valida vulnerabilidades de forma ativa.

## Regras obrigatórias (valem em todas as etapas)

1. **Sem exploração.** Não gerar payloads, exploits, nem instruções de uso de exploração. Não recomendar qual exploit usar contra um alvo. Se o usuário pedir, recusar e oferecer mitigação.
2. **Sem credenciais padrão.** Não incluir teste de credenciais padrão ou de fábrica em nenhuma etapa. No Wi-Fi e em serviços, apenas recomendar a *troca* de credenciais.
3. **CVEs: rigor.** Para cada CVE citada, informar: ID, link NVD, severidade CVSS (com versão e fonte), boletim ou patch, mitigação e explicação conceitual curta. Confirmar o ID antes de citar (NVD ou [references/common-cve-cheatsheet.md](references/common-cve-cheatsheet.md)). Se não for possível confirmar, marcar **"não verificado"**. Nunca inventar identificadores, scores ou versões corrigidas.
4. **Validação apenas passiva.** Correlacionar por versão, banner e configuração presentes nos dados fornecidos. Nada que cause efeito no alvo. Um banner indica versão *candidata*, não vulnerabilidade confirmada; registrar o grau de confiança e o estado do achado (ver "Confiança do achado").
5. **Anonimização.** Em qualquer arquivo versionado, usar placeholders: IPs de documentação (RFC 5737: `192.0.2.0/24`, `198.51.100.0/24`, `203.0.113.0/24`), hostnames como `host-01` e cliente como `CLIENTE-A`. Dados reais permanecem fora do repositório; ao salvar relatório, gravar fora de qualquer diretório versionado ou anonimizado.

## Confiança do achado (calibração)

Todo achado tem um **estado**:
- **Confirmado:** a versão vem de fonte confiável e não contraditória (inventário de pacotes, saída de versão do próprio serviço, banner exato coerente com o SO), está na faixa afetada e não há indício de backport.
- **Candidato a confirmar:** a versão é heurística, contraditória ou sujeita a backport.

Regras:
1. **Versão heurística.** A versão que o `nmap -sV` informa para alguns serviços é uma estimativa, não uma leitura exata. Casos típicos: Samba (`Samba smbd 4.x` deduzido do protocolo), PostgreSQL ("9.6.0 or later"), serviços com banner genérico ou ocultado. Tratar como candidata.
2. **Cruzamento com o SO.** Antes de correlacionar CVE, comparar a versão com o SO e a distribuição inferidos de outros banners (ex.: sufixo `Ubuntu` no OpenSSH, banner do BIND ou do Apache). Se a versão é incompatível com a distribuição (ex.: Samba 4.6.2 num host Ubuntu 24.04), registrar **"banner inconsistente com o SO"** e tratar como candidato, nunca como confirmado.
3. **Backports.** Banner com sufixo de distribuição (ex.: `3ubuntu13.20`) indica possível correção por backport: estado candidato, confiança baixa. Quando houver rede, conferir o aviso de segurança da distribuição (USN, DSA, RHSA) antes de manter ou rebaixar o achado.
4. **Teto de severidade.** Candidato a confirmar **nunca** recebe Crítico nem Alto. Registrar como "Candidato (CVSS X.X se confirmado)". CVSS e KEV descrevem a CVE, não o host.
5. **Resumo executivo e postura geral** são calculados **somente sobre achados confirmados**. Candidatos ficam em bloco separado "A confirmar", fora da contagem de críticos e altos e fora da frase de abertura.
6. **Fontes que se contradizem** (nmap, netstat, banner de outro serviço): registrar em "limitações" e tratar o achado como candidato.

## Consulta ao vivo de CVEs (camada de atualização)

O [cheatsheet](references/common-cve-cheatsheet.md) é uma base **clássica e estável**, com data de última verificação no topo. Ele não cobre CVEs recentes. Em toda análise, complementar com fontes públicas atuais:

1. **Frescor do cheatsheet.** Ler a linha `Última verificação:` do arquivo. Se tiver mais de 90 dias, ou estiver ausente, registrar o aviso no relatório.
2. **Fontes (somente leitura, dados públicos):**
   - Catálogo KEV da CISA (exploração confirmada): `https://www.cisa.gov/sites/default/files/feeds/known_exploited_vulnerabilities.json`. O arquivo é grande (~1,8 MB): baixar e filtrar localmente por `vendorProject`/`product` e `dateAdded`, em vez de ler inteiro no contexto. Campos úteis: `cveID`, `dateAdded`, `knownRansomwareCampaignUse`, `requiredAction`; registrar o `catalogVersion`.
   - NVD API 2.0: `https://services.nvd.nist.gov/rest/json/cves/2.0?cveId=CVE-AAAA-NNNNN` para confirmar ID, CVSS e referências; `...?keywordSearch=<produto>` para descobrir CVEs de um produto/versão. Sem chave, o limite é de poucas requisições por janela de 30 s; espaçar as chamadas.
3. **Cobertura por produto.** Para cada serviço identificado (produto e versão), consultar a NVD com `keywordSearch=<produto e versão>` e descartar as CVEs cuja faixa afetada não inclui a versão; priorizar as que estão no KEV. Use `resultsPerPage` pequeno e refine a busca quando o total for grande. Registrar no relatório **quais produtos foram pesquisados e quais não** (limite de requisições, falta de rede): produto não pesquisado é "não verificado".
4. **Confidencialidade.** Enviar a essas fontes **somente** IDs de CVE e nomes/versões de produto (ex.: "OpenSSH 9.6"). **Nunca** IPs, hostnames, nomes de cliente, nem trechos da saída do nmap/netstat.
5. **Precedência.** O dado da consulta ao vivo prevalece sobre o cheatsheet. Se houver divergência de score ou de versão corrigida, reportar as duas fontes e a data da consulta.
6. **Uso do KEV.** "Presente no KEV" significa exploração observada: usar como fator de **priorização** (citar "no KEV desde AAAA-MM-DD" e o indicador de ransomware). Não usar para decidir como explorar nada; a regra 1 continua valendo.
7. **Sem rede ou falha na consulta.** Usar o cheatsheet e marcar como **"não verificado (consulta ao vivo indisponível)"** toda CVE que não estiver nele. Não preencher de memória.
8. **Registro obrigatório no relatório:** fontes consultadas, produtos pesquisados e não pesquisados, data/hora da consulta, `catalogVersion` do KEV e data de última verificação do cheatsheet.

## Fluxo

### 1. Escopo (portão obrigatório)
Antes de qualquer análise, exigir:
- faixas de IP e alvos em escopo;
- janela de testes;
- autorização formal por escrito (quem assinou, data, referência).

Se faltar qualquer item, **parar e pedir**. Descartar da análise qualquer host fora das faixas informadas e registrar o descarte.

### 2. Descoberta
Mapear hosts ativos a partir da saída fornecida (estado `up`, portas abertas, MAC/fabricante quando houver).

### 3. Topologia
Gerar o diagrama com nós, sub-redes e gateways. **Ler [templates/network-topology.md](templates/network-topology.md)** e preencher com dados anonimizados. Inferir gateways apenas de evidência (rota, traceroute, saída de `netstat -r`); marcar o que for suposição.

### 4. Fingerprinting
Extrair SO, portas, serviços e versões de nmap (`-oX` ou `-oG`) e de `netstat -tulnp` (processo, porta, interface de escuta). Registrar a confiança do SO (percentual do nmap) e discrepâncias entre fontes.

### 5. Segregação
Classificar cada ativo em: **servidor de infraestrutura**, **servidor de aplicação** ou **estação de usuário**. Justificar pela combinação de SO, serviços e portas; marcar "incerto" quando a evidência for fraca.

### 6. Estações
Listar SOs fora de suporte e serviços expostos (SSH, RDP, FTP, SMB). Para cada achado: CVEs associadas (ver regra 3), estado (Confirmado ou Candidato a confirmar), severidade e correção. Consultar [references/common-cve-cheatsheet.md](references/common-cve-cheatsheet.md) e a consulta ao vivo para correlacionar versão ↔ CVE.

### 7. Servidores
Mapear serviços críticos: web, banco de dados, DNS, e-mail, AD/LDAP. Associar versões a CVEs com CVSS (regra 3), priorizando exposição, privilégio do serviço e criticidade do ativo. Aplicar "Confiança do achado" a cada versão antes de atribuir severidade.

### 8. Wi-Fi (somente defensivo)
Checklist, sem qualquer ataque a rede sem fio:
- WPA3 (ou WPA2-AES/CCMP no mínimo); sem WEP/WPA-TKIP;
- WPS desativado;
- credenciais de fábrica do AP/roteador trocadas (recomendar a troca; não testar);
- SSID de gestão separado, segmentação de convidados, firmware atualizado;
- administração do AP restrita à rede de gestão.

### 9. Relatório
**Ler [templates/redteam-recon-report.md](templates/redteam-recon-report.md)** e preencher: resumo executivo (só achados confirmados), tabela de achados com IDs F-NN, matriz de risco, tabela de ativos, CVEs prioritárias e remediação. Fechar com a lista de itens "não verificado" e das limitações da análise.

## Quando ler cada arquivo

| Arquivo | Ler quando |
|---|---|
| [templates/network-topology.md](templates/network-topology.md) | Etapa 3, ao montar o diagrama Mermaid |
| [templates/redteam-recon-report.md](templates/redteam-recon-report.md) | Etapa 9, ao redigir o relatório final |
| [references/common-cve-cheatsheet.md](references/common-cve-cheatsheet.md) | Etapas 6 e 7, ao correlacionar versão ↔ CVE. É a base clássica, não a lista completa: sempre complementar com a seção "Consulta ao vivo de CVEs"; sem confirmação, marcar "não verificado" |

## Postura de saída

Linguagem direta e técnica. Distinguir sempre **fato observado** (dado fornecido), **inferência** (justificada) e **suposição** (a confirmar). Não suavizar achados confirmados, e não inflar candidatos: o que não foi confirmado não vira Crítico.
