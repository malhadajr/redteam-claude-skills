# Template: topologia de rede (Mermaid)

> Use somente dados **anonimizados** (IPs RFC 5737, `host-NN`, `CLIENTE-A`). Marque inferências com linha tracejada (`-.->`) e `?` no rótulo.

## Convenções

- Um `subgraph` por sub-rede; o rótulo traz o CIDR.
- Forma do nó: `([ ])` gateway/roteador/firewall, `[( )]` banco de dados, `[ ]` servidor, `[/ /]` estação, `{{ }}` ponto de acesso Wi-Fi.
- Rótulo do nó: `nome<br/>IP<br/>SO · portas-chave`.
- Classes: `crit` (achado CVSS ≥ 9.0), `high` (7.0–8.9), `eol` (SO fora de suporte), `unk` (não classificado).

## Diagrama

```mermaid
flowchart TB
    INET((Internet / fora do escopo))

    subgraph DMZ["DMZ · 198.51.100.0/24"]
        GW1([gw-01<br/>198.51.100.1<br/>firewall/gateway])
        WEB1[host-01<br/>198.51.100.10<br/>Linux · 80/443]
        DNS1[host-02<br/>198.51.100.11<br/>Linux · 53]
    end

    subgraph SRV["Servidores · 192.0.2.0/24"]
        GW2([gw-02<br/>192.0.2.1])
        AD1[host-10<br/>192.0.2.10<br/>Windows Server · 88/389/445]
        DB1[(host-11<br/>192.0.2.20<br/>Linux · 5432)]
    end

    subgraph USR["Estações · 203.0.113.0/24"]
        GW3([gw-03<br/>203.0.113.1])
        WS1[/host-20<br/>203.0.113.50<br/>Windows · 445 · 3389/]
        WS2[/host-21<br/>203.0.113.51<br/>Linux · 22/]
        AP1{{ap-01<br/>203.0.113.2<br/>Wi-Fi}}
    end

    INET --- GW1
    GW1 --- WEB1
    GW1 --- DNS1
    GW1 --- GW2
    GW2 --- AD1
    GW2 --- DB1
    GW2 --- GW3
    GW3 --- WS1
    GW3 --- WS2
    GW3 --- AP1
    WEB1 -.->|"acesso a banco ?"| DB1

    classDef crit fill:#fdd,stroke:#c00,color:#000;
    classDef high fill:#ffe4c4,stroke:#d80,color:#000;
    classDef eol fill:#eee,stroke:#666,stroke-dasharray:4 3,color:#000;
    classDef unk fill:#fff,stroke:#999,stroke-dasharray:2 2,color:#000;
    class WS1 eol;
    class AD1 high;
```

## Preenchimento

1. Uma linha por host ativo; agrupar por sub-rede inferida do CIDR informado em escopo.
2. Aplicar `class <id> <classe>` conforme a pior severidade verificada do host.
3. Ligações sem evidência direta (rota, ARP, traceroute, conexão em `netstat`) usam `-.->` e rótulo com `?`.
4. Adicionar abaixo do diagrama uma **legenda de evidências**: de onde veio cada gateway e cada ligação.

| Elemento | Evidência | Confiança |
|---|---|---|
| gw-01 | _ex.: saída de traceroute / tabela de rotas_ | alta / média / baixa |
