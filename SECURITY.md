# Política de segurança

## Escopo de uso

Este repositório contém instruções para o Claude Code analisar dados de reconhecimento **já coletados**, em avaliações de segurança **com autorização formal por escrito** do dono dos sistemas. Não contém nem aceita exploits, payloads ou instruções de exploração.

## Como reportar uma falha

Reporte em privado, pela aba **Security → Report a vulnerability** deste repositório. Não abra issue pública com detalhes.

Exemplos do que reportar:

- a skill gera conteúdo de exploração, testa credenciais ou sugere ação ativa contra um alvo;
- a skill envia a fontes externas (NVD, CISA) algo além de IDs de CVE e nomes/versões de produto, como IPs, hostnames ou trechos da coleta;
- o portão de escopo pode ser contornado de forma não documentada;
- algum arquivo versionado contém dado real (IP, hostname, nome de cliente).

Falsos positivos e falsos negativos de CVE não são falhas de segurança: use o modelo de issue correspondente, com dados anonimizados.

## Versões atendidas

Somente a versão mais recente publicada na branch `main`.
