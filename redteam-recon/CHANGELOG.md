# Changelog

Formato baseado em [Keep a Changelog](https://keepachangelog.com/pt-BR/1.1.0/). A cada atualização de conteúdo, aumentar `version` em `.claude-plugin/plugin.json`: com `version` definido, os usuários permanecem na versão instalada até ela mudar.

## [0.2.0] - 2026-11-06
### Adicionado
- Seção "Consulta ao vivo de CVEs" no `SKILL.md`: NVD API 2.0 e catálogo KEV da CISA, com regras de confidencialidade, precedência e fallback sem rede.
- Carimbo `Última verificação` e validade de 90 dias no cheatsheet.
- Campos de rastreabilidade (fontes, data da consulta, `catalogVersion`, KEV) no template de relatório.
### Alterado
- Cheatsheet: itens confirmados em fontes oficiais; `LoginGraceTime 0` reclassificado como não confirmado.

## [0.1.0] - 2026-11-06
### Adicionado
- Primeira versão: skill `redteam-recon-analyzer`, templates de topologia e relatório, cheatsheet de 15 CVEs.
