# 📊 SQL — Análise de Engajamento de Clientes

Pipeline SQL de construção de ABT (Analytical Base Table) para análise comportamental de clientes, com múltiplas janelas temporais e engenharia de features direto na camada de dados.

---

## 🎯 Objetivo

Construir uma base analítica consolidada que capture o **comportamento de engajamento de cada cliente ao longo do tempo**, permitindo identificar padrões de consumo, recência de interação e perfil de produto — insumo direto para modelos de churn e segmentação.

---

## 🧠 Destaques Técnicos

- **CTEs encadeadas** — 10 subconsultas modulares, cada uma com responsabilidade única
- **Janelas temporais múltiplas** — métricas calculadas para 7, 14, 28 e 56 dias + vida toda do cliente
- **Window Functions** — `ROW_NUMBER() OVER (PARTITION BY ...)` para identificar produto e período favorito por cliente
- **Feature Engineering em SQL** — engajamento relativo (`qtdeTransacoes28 / qtdeTransacoesVida`), separação de pontos positivos e negativos, período do dia (manhã/tarde/noite/madrugada)
- **Template parametrizável** — query aceita `{date}` como parâmetro, permitindo geração da ABT para qualquer data de referência (padrão usado em projetos de ML)

---

## 🗂️ Estrutura do Projeto

```
sql-analise-engajamento-clientes/
│
├── DADOS/
│   ├── ETL_PROJETO.sql        # Query principal de construção da ABT
│   ├── clientes.csv           # Cadastro de clientes com data de criação
│   ├── produtos.csv           # Catálogo de produtos e categorias
│   ├── transacoes.csv         # Histórico de transações com pontos
│   ├── transacao_produto.csv  # Tabela de relacionamento transação-produto
│   └── banco de dados.db      # Banco SQLite local para execução
│
├── etl.py                     # Pipeline Python que executa o SQL e exporta resultado
└── README.md
```

---

## 🔄 Arquitetura da Query

A query é construída em camadas progressivas via CTEs:

```
transacoes (raw)
    └── tb_transacoes          → parse de datas, cálculo de diffDate e hora
            ├── tb_sumario_transacoes   → contagens e saldos por janela temporal
            ├── tb_transacao_produto    → join com produtos e categorias
            │       └── tb_cliente_produto      → agregação por cliente/produto
            │               └── tb_cliente_produto_rn  → produto favorito (ROW_NUMBER)
            ├── tb_cliente_dia          → dia da semana com mais transações
            │       └── tb_cliente_dia_rn       → dia favorito (ROW_NUMBER)
            └── tb_cliente_periodo      → período do dia preferido
                    └── tb_cliente_periodo_rn  → período favorito (ROW_NUMBER)

clientes (raw)
    └── tb_cliente             → idade do cliente na data de referência

tb_join → SELECT final com todas as features consolidadas
```

---

## 📐 Features Geradas

| Feature | Descrição |
|---|---|
| `qtdeTransacoesVida` / `28` / `14` / `7` | Frequência de compras em múltiplas janelas |
| `saldoPontos` | Saldo acumulado de pontos na vida |
| `qtdePontosPos*` / `qtdePontosNeg*` | Pontos positivos e negativos por janela |
| `diasUltimaInteracao` | Recência — dias desde a última transação |
| `idadeBase` | Tempo de vida do cliente na data de referência |
| `produtoVida` / `produto28` / `produto7` | Produto favorito por janela temporal |
| `dtDia` | Dia da semana com maior frequência de compras (últimos 28 dias) |
| `periodoMaisTransacao28` | Período do dia preferido (últimos 28 dias) |
| `engajamento28Vida` | Proporção de transações recentes vs. total — proxy de engajamento |

---

## ▶️ Como Executar

**Pré-requisitos:** Python 3.8+, SQLite (nativo no Python)

```bash
# Clone o repositório
git clone https://github.com/edudatalytics/sql-analise-engajamento-clientes.git
cd sql-analise-engajamento-clientes

# Execute o pipeline ETL para uma data de referência
python etl.py --date 2024-01-01
```

O script `etl.py` lê o `ETL_PROJETO.sql`, substitui o parâmetro `{date}`, executa no banco SQLite e exporta a ABT resultante em CSV.

---

## 🛠️ Stack

![SQLite](https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white)

---

## 👤 Autor

**Eduardo Matos** — Cientista de Dados Júnior  
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Eduardo_Matos-blue?style=flat&logo=linkedin)](https://linkedin.com/in/matos-eduardo)
[![GitHub](https://img.shields.io/badge/GitHub-edudatalytics-black?style=flat&logo=github)](https://github.com/edudatalytics)
