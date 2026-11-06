# redteam-claude-skills

**PT-BR** · [English below](#english)

Coleção de skills do Claude Code para ferramentas de red team em avaliações de segurança **autorizadas**.

## Plugins

| Plugin | Versão | O que faz |
|---|---|---|
| [`redteam-recon`](redteam-recon/) | 0.2.1 | Triagem de reconhecimento: analisa saídas de nmap/netstat, mapeia a superfície de ataque, correlaciona CVEs e gera relatório técnico e executivo. |

## redteam-recon

A skill `redteam-recon-analyzer` trabalha **sobre dados já coletados**. Ela não executa varredura, não explora nada e não valida vulnerabilidades de forma ativa.

### Fluxo
1. **Escopo (portão obrigatório):** exige faixas de IP, alvos, janela de testes e autorização formal por escrito. Sem isso, para e pede.
2. Descoberta de hosts ativos.
3. Topologia em diagrama Mermaid.
4. Fingerprinting (SO, portas, serviços, versões) a partir de nmap `-oX`/`-oG` e `netstat -tulnp`.
5. Segregação: servidor (infraestrutura ou aplicação) ou estação de usuário.
6. Estações: SOs fora de suporte e serviços expostos (SSH, RDP, FTP, SMB).
7. Servidores: serviços críticos (web, banco, DNS, e-mail, AD/LDAP) associados a CVEs com CVSS.
8. Wi-Fi: checklist somente defensivo.
9. Relatório: resumo executivo, matriz de risco, inventário, CVEs prioritárias e remediação.

### Regras
- **Sem exploração:** não gera payloads, exploits nem instruções de uso, e não recomenda qual exploit usar.
- **Sem teste de credenciais padrão** em nenhuma etapa.
- **CVEs com rigor:** cada CVE traz ID, link NVD, CVSS (com versão e fonte), boletim ou patch, mitigação e explicação conceitual. O que não for confirmado é marcado como **"não verificado"**. Nunca inventa identificadores.
- **Validação apenas passiva:** versão, banner e configuração.
- **Anonimização:** nada de IPs, hostnames ou nomes de cliente reais em arquivos versionados.

### Consulta ao vivo de CVEs
O arquivo `common-cve-cheatsheet.md` é uma base clássica (2011–2024) com data de última verificação. Durante a análise, a skill complementa essa base com a **NVD API 2.0** e o **catálogo KEV da CISA**. Só IDs de CVE e produto/versão são enviados a essas fontes; nunca IPs, hostnames ou trechos da saída do nmap. Sem rede, as CVEs fora do cheatsheet ficam como "não verificado".

### Instalação (Claude Code)
```bash
git clone https://github.com/malhadajr/redteam-claude-skills
claude --plugin-dir ./redteam-claude-skills/redteam-recon
```
Depois, invoque `/redteam-recon:redteam-recon-analyzer` ou descreva a tarefa (por exemplo, "triagem desta saída de nmap").

### Exemplo (ilustrativo, dados anonimizados)
Entrada (nmap `-oG`):
```
Host: 192.0.2.10 (host-01)  Ports: 80/open/tcp//http//Apache httpd 2.4.49/
```
Trecho da saída:

| Host | Serviço (fonte) | CVE | CVSS | Correção |
|---|---|---|---|---|
| host-01 | Apache 2.4.49 (banner) | CVE-2021-41773 / CVE-2021-42013 | 9.8 / 9.8 (NVD) | Atualizar para 2.4.51 ou superior |

A correlação por banner é **candidata**, não confirmação; o relatório registra a confiança.

### Status
Versão inicial (0.x). Passou no `claude plugin validate`, mas ainda não foi validada de ponta a ponta em engajamentos reais. Os itens marcados com † no cheatsheet ainda não foram confirmados em fonte primária.

### Uso responsável
Use **apenas** com autorização formal por escrito do dono dos sistemas. Os autores não se responsabilizam por uso indevido.

## Licença
MIT. Autor: LogosPayload

---

<a id="english"></a>

# redteam-claude-skills (English)

Claude Code skills for red team tooling in **authorized** security assessments.

## Plugins

| Plugin | Version | What it does |
|---|---|---|
| [`redteam-recon`](redteam-recon/) | 0.2.1 | Reconnaissance triage: analyzes nmap/netstat output, maps the attack surface, correlates CVEs, and produces a technical and executive report. |

## redteam-recon

The `redteam-recon-analyzer` skill works **on data you have already collected**. It does not scan, exploit, or actively validate vulnerabilities.

### Workflow
1. **Scope gate (mandatory):** requires IP ranges, targets, testing window, and formal written authorization. Without them it stops and asks.
2. Live host discovery.
3. Network topology as a Mermaid diagram.
4. Fingerprinting (OS, ports, services, versions) from nmap `-oX`/`-oG` and `netstat -tulnp`.
5. Segregation: server (infrastructure or application) or user workstation.
6. Workstations: unsupported OSes and exposed services (SSH, RDP, FTP, SMB).
7. Servers: critical services (web, database, DNS, mail, AD/LDAP) mapped to CVEs with CVSS.
8. Wi-Fi: defensive-only checklist.
9. Report: executive summary, risk matrix, asset inventory, priority CVEs, and remediation.

### Rules
- **No exploitation:** no payloads, exploits, or usage instructions, and no advice on which exploit to use.
- **No default-credential testing** at any step.
- **Rigorous CVE handling:** each CVE includes ID, NVD link, CVSS (with version and source), advisory or patch, mitigation, and a conceptual explanation. Anything unconfirmed is marked **"not verified"**. Identifiers are never invented.
- **Passive validation only:** version, banner, and configuration.
- **Anonymization:** no real IPs, hostnames, or client names in versioned files.

### Live CVE lookup
`common-cve-cheatsheet.md` is a classic baseline (2011–2024) with a last-verified date. During analysis the skill supplements it with the **NVD API 2.0** and the **CISA KEV catalog**. Only CVE IDs and product/version strings are sent to those sources, never IPs, hostnames, or nmap output excerpts. Offline, CVEs outside the cheatsheet are marked "not verified".

### Installation (Claude Code)
```bash
git clone https://github.com/malhadajr/redteam-claude-skills
claude --plugin-dir ./redteam-claude-skills/redteam-recon
```
Then invoke `/redteam-recon:redteam-recon-analyzer` or describe the task (for example, "triage this nmap output").

### Example (illustrative, anonymized data)
Input (nmap `-oG`):
```
Host: 192.0.2.10 (host-01)  Ports: 80/open/tcp//http//Apache httpd 2.4.49/
```
Output excerpt:

| Host | Service (source) | CVE | CVSS | Fix |
|---|---|---|---|---|
| host-01 | Apache 2.4.49 (banner) | CVE-2021-41773 / CVE-2021-42013 | 9.8 / 9.8 (NVD) | Upgrade to 2.4.51 or later |

A banner-based match is a **candidate**, not a confirmation; the report records the confidence level.

### Status
Early release (0.x). It passes `claude plugin validate` but has not yet been validated end to end on real engagements. Items marked † in the cheatsheet are not yet confirmed against a primary source.

### Responsible use
Use **only** with formal written authorization from the system owner. The authors are not responsible for misuse.

## License
MIT. Author: [LogosPayload]
