# Relatório de triagem de reconhecimento — CLIENTE-A

> **Anonimizado.** IPs RFC 5737, hostnames `host-NN`, cliente `CLIENTE-A`. Dados reais ficam fora do repositório.
> Classificação: _[ex.: confidencial — uso restrito ao engajamento]_

| Campo | Valor |
|---|---|
| Engajamento | _[referência]_ |
| Autorização formal (escrita) | _[emitente · data · referência]_ |
| Escopo (faixas/alvos) | _[ex.: 192.0.2.0/24, 198.51.100.0/24]_ |
| Janela de testes | _[início – fim, fuso]_ |
| Fontes analisadas | _[nmap -oX, netstat -tulnp, inventário; data da coleta]_ |
| Fontes de CVE e data da consulta | _[NVD API 2.0, KEV `catalogVersion` ____; data/hora; ou "consulta ao vivo indisponível"]_ |
| Última verificação do cheatsheet | _[AAAA-MM-DD; avisar se > 90 dias]_ |
| Natureza da análise | Triagem passiva sobre dados fornecidos; sem exploração |
| Analista / revisão | _[analista responsável]_ |

---

# Parte I — Resumo executivo

**Postura geral:** _[Crítica / Alta / Média / Baixa]_ — uma frase com a razão principal. Calculada **somente sobre achados confirmados**.

- **Ativos analisados:** _N_ (servidores: _n_, estações: _n_, rede/outros: _n_).
- **Achados confirmados:** _n_ críticos, _n_ altos, _n_ médios, _n_ baixos.
- **A confirmar (fora da contagem acima):** _n_ candidatos, em ordem de verificação. Não abrem o resumo nem definem a postura.
- **Principais riscos (até 3), em linguagem de negócio:**
  1. _[ex.: sistemas sem suporte do fabricante expostos na rede de usuários]_
  2. _[ex.: serviço de administração remota acessível a partir de rede ampla]_
  3. _[ex.: controlador de domínio sem correções de segurança críticas]_
- **Ação imediata recomendada:** _[1–2 frases]_
- **Limitações:** _[ex.: dados de um único ponto de vista; versões inferidas por banner]_

---

# Parte II — Relatório técnico

## 1. Escopo e metodologia
Descrever a verificação de escopo (hosts descartados por estarem fora das faixas) e o fluxo seguido. Validação **somente passiva** (versão, banner, configuração).

## 2. Topologia
_[Inserir diagrama conforme `templates/network-topology.md` + legenda de evidências]_

## 3. Achados

Um ID por achado (F-NN). Esses IDs são os usados no resumo, na matriz de risco e nas tabelas seguintes. **Estado:** Confirmado ou Candidato a confirmar (ver "Confiança do achado" no `SKILL.md`). Candidato nunca recebe Crítico nem Alto.

| ID | Ativo | Achado | Evidência (fonte) | Estado | Severidade | CVE | Ação |
|---|---|---|---|---|---|---|---|
| F-01 | A-01 | _[ex.: serviço X versão Y exposto]_ | _[ex.: banner nmap; netstat]_ | _Confirmado / Candidato (motivo)_ | _Média; ou "Candidato (CVSS 9.8 se confirmado)"_ | _CVE-AAAA-NNNNN / —_ | _[correção ou verificação]_ |

## 4. Matriz de risco

Probabilidade × impacto (preencher com IDs de achado F-NN; candidatos marcados com `?`).

| Impacto \ Probabilidade | Baixa | Média | Alta |
|---|---|---|---|
| **Alto** | | | |
| **Médio** | | | |
| **Baixo** | | | |

Critério: probabilidade considera exposição e pré-requisitos; impacto considera criticidade do ativo e privilégio do serviço. CVSS é insumo, não o veredito.

## 5. Inventário de ativos

| ID | Host | IP | Função | SO (confiança) | Portas/serviços (versão) | Classe | Pior achado (estado) |
|---|---|---|---|---|---|---|---|
| A-01 | host-01 | 198.51.100.10 | Servidor de aplicação | _Linux (95%)_ | 80/tcp _httpd x.y_, 443/tcp | Servidor-App | _—_ |
| A-02 | host-20 | 203.0.113.50 | Estação | _Windows (90%)_ | 445/tcp, 3389/tcp | Estação | _—_ |

Classe: Servidor-Infra · Servidor-App · Estação · Rede · Incerto.

## 6. Estações de usuário
### 6.1 SOs fora de suporte
| Host | SO | Fim de suporte | Observação |
|---|---|---|---|

### 6.2 Serviços expostos (SSH, RDP, FTP, SMB)
| Host | Serviço/porta | Versão (fonte) | Risco | CVEs relacionadas | Correção |
|---|---|---|---|---|---|

## 7. Servidores
Agrupar por função: web · banco de dados · DNS · e-mail · AD/LDAP.

| Host | Função | Serviço/versão (fonte) | Exposição | CVEs relacionadas | Correção |
|---|---|---|---|---|---|

## 8. Wi-Fi (checklist defensivo)
- [ ] WPA3, ou WPA2-AES/CCMP no mínimo; sem WEP/TKIP
- [ ] WPS desativado
- [ ] Credenciais de fábrica do AP/roteador trocadas
- [ ] Rede de convidados segregada
- [ ] Gestão do AP restrita à rede de administração
- [ ] Firmware atualizado

## 9. CVEs prioritárias

Uma ficha por CVE. **Todos os campos obrigatórios**; o que não for confirmado recebe "não verificado".

### CVE-AAAA-NNNNN — _título curto_
| Campo | Conteúdo |
|---|---|
| ID | CVE-AAAA-NNNNN |
| NVD | https://nvd.nist.gov/vuln/detail/CVE-AAAA-NNNNN |
| CVSS | _score · severidade · versão (3.1) · fonte (NVD primária / CNA)_ |
| Boletim / patch | _[link ou "não verificado"]_ |
| Mitigação | _[configuração/segmentação/desativação]_ |
| Explicação conceitual | _[2–3 frases sobre a classe de falha, sem passos de exploração]_ |
| Ativos afetados | _[IDs A-NN]_ |
| Base da correlação | _versão / banner / configuração — confiança alta/média/baixa_ |
| KEV (CISA) | _sim, desde AAAA-MM-DD, ransomware: sim/não/desconhecido · não · consulta indisponível_ |
| Estado do achado | _Confirmado / Candidato a confirmar (motivo: versão heurística, banner inconsistente com o SO, possível backport)_ |
| Status da CVE | _verificado em [data] / não verificado_ |

## 10. Remediação priorizada

| Prioridade | Ação | Ativos | Esforço | Prazo sugerido |
|---|---|---|---|---|
| P1 (imediato) | | | | |
| P2 (curto prazo) | | | | |
| P3 (planejado) | | | | |

## 11. Itens não verificados e limitações
Lista explícita de CVEs "não verificado", **produtos não pesquisados na NVD**, versões inferidas apenas por banner, fontes que se contradisseram e hosts cuja classificação ficou "incerto".

## Anexos
- A. Hosts descartados por fora de escopo (anonimizados).
- B. Referências em ABNT.
