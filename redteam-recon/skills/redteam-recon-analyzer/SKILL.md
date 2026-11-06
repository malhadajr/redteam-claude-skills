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
4. **Validação apenas passiva.** Correlacionar por versão, banner e configuração presentes nos dados fornecidos. Nada que cause efeito no alvo. Um banner indica versão *candidata*, não vulnerabilidade confirmada; registrar o grau de confiança.
5. **Anonimização.** Em qualquer arquivo versionado, usar placeholders: IPs de documentação (RFC 5737: `192.0.2.0/24`, `198.51.100.0/24`, `203.0.113.0/24`), hostnames como `host-01` e cliente como `CLIENTE-A`. Dados reais permanecem fora do repositório; ao salvar relatório, gravar fora de qualquer diretório versionado ou anonimizado.

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
Listar SOs fora de suporte e serviços expostos (SSH, RDP, FTP, SMB). Para cada achado: CVEs associadas (ver regra 3), severidade e correção. Consultar [references/common-cve-cheatsheet.md](references/common-cve-cheatsheet.md) para correlacionar versão ↔ CVE.

### 7. Servidores
Mapear serviços críticos: web, banco de dados, DNS, e-mail, AD/LDAP. Associar versões a CVEs com CVSS (regra 3), priorizando exposição, privilégio do serviço e criticidade do ativo.

### 8. Wi-Fi (somente defensivo)
Checklist, sem qualquer ataque a rede sem fio:
- WPA3 (ou WPA2-AES/CCMP no mínimo); sem WEP/WPA-TKIP;
- WPS desativado;
- credenciais de fábrica do AP/roteador trocadas (recomendar a troca; não testar);
- SSID de gestão separado, segmentação de convidados, firmware atualizado;
- administração do AP restrita à rede de gestão.

### 9. Relatório
**Ler [templates/redteam-recon-report.md](templates/redteam-recon-report.md)** e preencher: resumo executivo, matriz de risco, tabela de ativos, CVEs prioritárias e remediação. Fechar com a lista de itens "não verificado" e das limitações da análise.

## Quando ler cada arquivo

| Arquivo | Ler quando |
|---|---|
| [templates/network-topology.md](templates/network-topology.md) | Etapa 3, ao montar o diagrama Mermaid |
| [templates/redteam-recon-report.md](templates/redteam-recon-report.md) | Etapa 9, ao redigir o relatório final |
| [references/common-cve-cheatsheet.md](references/common-cve-cheatsheet.md) | Etapas 6 e 7, ao correlacionar versão ↔ CVE. Não é lista exaustiva; para qualquer CVE fora dela, consultar a NVD e, sem confirmação, marcar "não verificado" |

## Postura de saída

Linguagem direta e técnica. Distinguir sempre **fato observado** (dado fornecido), **inferência** (justificada) e **suposição** (a confirmar). Não suavizar achados críticos.
