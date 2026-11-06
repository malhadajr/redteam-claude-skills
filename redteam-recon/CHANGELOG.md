# Changelog

Formato baseado em [Keep a Changelog](https://keepachangelog.com/pt-BR/1.1.0/). A cada atualização de conteúdo, aumentar `version` em `.claude-plugin/plugin.json`: com `version` definido, os usuários permanecem na versão instalada até ela mudar.

## [0.2.1] - 2026-11-06
### Corrigido
- Falso positivo crítico em Samba: a versão que o `nmap -sV` informa para Samba e PostgreSQL é heurística. Achado cuja versão contradiz o SO (ex.: Samba 4.6.2 em Ubuntu 24.04) ou que pode ter backport vira "Candidato a confirmar" e nunca recebe Crítico nem Alto.
- O resumo executivo e a postura geral passam a considerar somente achados confirmados; candidatos ficam num bloco "A confirmar".
- Os IDs F-NN do template eram citados sem definição. O template ganhou a seção "Achados".
### Adicionado
- Seção "Confiança do achado (calibração)" no `SKILL.md`.
- Cobertura por produto: busca na NVD por `keywordSearch` para cada serviço identificado, com registro dos produtos pesquisados e não pesquisados.
- Classe `cand` no diagrama de topologia.
### Alterado
- Template: campo "Autor / revisão" virou "Analista / revisão", para não ser confundido com o autor do plugin.

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
