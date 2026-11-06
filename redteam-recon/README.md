# redteam-recon

Plugin do Claude Code com a skill `redteam-recon-analyzer`: triagem **passiva** de saídas de nmap (`-oX`/`-oG`) e `netstat -tulnp` em engajamentos de red team **autorizados**. Gera topologia em Mermaid, inventário, achados com estado (Confirmado ou Candidato a confirmar), fichas de CVE (cheatsheet + NVD API 2.0 + KEV da CISA) e relatório técnico e executivo.

Não varre, não explora, não testa credenciais.

## Instalação

No Claude Code 2.1.275 ou posterior, num passo:

```text
/plugin install redteam-recon --marketplace malhadajr/redteam-claude-skills
```

Outras formas (marketplace em dois passos, `--plugin-dir`): ver o README do repositório.

## Uso

```text
/redteam-recon:redteam-recon-analyzer
```

Ou descreva a tarefa ("triagem desta saída de nmap"). A skill exige escopo, janela de testes e autorização por escrito antes de começar.

## Conteúdo

| Arquivo | Papel |
|---|---|
| [`skills/redteam-recon-analyzer/SKILL.md`](skills/redteam-recon-analyzer/SKILL.md) | Regras, calibração de confiança e fluxo de 9 etapas |
| [`templates/network-topology.md`](skills/redteam-recon-analyzer/templates/network-topology.md) | Modelo do diagrama de topologia |
| [`templates/redteam-recon-report.md`](skills/redteam-recon-analyzer/templates/redteam-recon-report.md) | Modelo do relatório |
| [`references/common-cve-cheatsheet.md`](skills/redteam-recon-analyzer/references/common-cve-cheatsheet.md) | Base clássica de CVEs, com data de verificação |
| [`CHANGELOG.md`](CHANGELOG.md) | Histórico de versões |

Instalação, exemplo real, status e uso responsável: [README do repositório](https://github.com/malhadajr/redteam-claude-skills#readme).

Licença MIT. Autor: LogosPayload.
