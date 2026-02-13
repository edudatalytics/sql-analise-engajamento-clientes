# 📊 SQL - Análise de Engajamento de Clientes (Projeto ETL)

<div align="center">

![SQL](https://img.shields.io/badge/SQL-4479A1?style=for-the-badge&logo=postgresql&logoColor=white)
![ETL](https://img.shields.io/badge/ETL-FF6B6B?style=for-the-badge)
![SQLite](https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white)
![Status](https://img.shields.io/badge/Status-Concluído-success?style=for-the-badge)

**Pipeline ETL completa para análise estratégica de comportamento e engajamento de clientes**


</div>

---

## 🎯 Contexto e Motivação

Mesmo já tendo estudado SQL durante meu curso em **Ciência de Dados pela EBAC**, decidi aprofundar meus conhecimentos através de um projeto prático robusto que simulasse um cenário real de negócio.

Este projeto foi desenvolvido como **trabalho final do curso de SQL do Téo Me Why**, consolidando desde comandos básicos até técnicas avançadas de análise temporal e segmentação de clientes.

**Desafio:** Construir uma pipeline ETL que transforma dados transacionais brutos em uma **base analítica** pronta para Machine Learning e dashboards, calculando métricas de engajamento em múltiplas janelas temporais.

---

## 🎓 Sobre o Curso

**Instrutor:** [Téo Calvo (Téo Me Why)](https://www.twitch.tv/teomewhy)  
**Plataforma:** Twitch + YouTube  
**Carga Horária:** 40+ horas de conteúdo prático  
**Projeto Final:** ETL de Engajamento e Segmentação de Clientes

**Principais aprendizados:**
- ✅ SQL avançado com foco em análise de dados
- ✅ Common Table Expressions (CTEs) para organização de queries complexas
- ✅ Window Functions para rankings e análises temporais
- ✅ JOINs encadeados com múltiplas tabelas
- ✅ Técnicas de agregação temporal (7, 14, 28, 56 dias)
- ✅ Construção de features para Machine Learning

---

## 📊 Estrutura de Dados

### **Modelo Entidade-Relacionamento**

```
┌─────────────────┐         ┌──────────────────────┐         ┌─────────────────┐
│   CLIENTES      │         │    TRANSACOES        │         │   PRODUTOS      │
├─────────────────┤         ├──────────────────────┤         ├─────────────────┤
│ IdCliente (PK)  │────────>│ IdTransacao (PK)     │<────────│ idProduto (PK)  │
│ dtCriacao       │         │ IdCliente (FK)       │         │ DescProduto     │
│                 │         │ QtdePontos           │         │ DescCategoria   │
│                 │         │ dtCriacao            │         │                 │
└─────────────────┘         └──────────────────────┘         └─────────────────┘
                                      │
                                      │
                                      ▼
                            ┌─────────────────────┐
                            │ TRANSACAO_PRODUTO   │
                            ├─────────────────────┤
                            │ idTransacao (FK)    │
                            │ idProduto (FK)      │
                            └─────────────────────┘
```

### **Tabelas e Volumetria**

| Tabela | Descrição | Registros Estimados |
|--------|-----------|---------------------|
| `clientes` | Cadastro de clientes | 10,000+ |
| `transacoes` | Histórico de transações | 50,000+ |
| `produtos` | Catálogo de produtos | 500+ |
| `transacao_produto` | Itens por transação | 150,000+ |

---

## 🎯 Objetivo da Análise

Criar uma **base analítica única (ABT - Analytical Base Table)** com métricas de comportamento do cliente em múltiplas janelas temporais para:

✅ **Identificar padrões de engajamento** (recente vs histórico)  
✅ **Segmentar clientes** por produto favorito e período de compra  
✅ **Detectar sazonalidade** (dia da semana e período do dia)  
✅ **Preparar features** para modelos de churn e propensão de compra  

---

## 🛠️ Tecnologias e Técnicas SQL Utilizadas

### **SQL Avançado**

```sql
✓ Common Table Expressions (CTEs) - 10+ CTEs encadeadas
✓ Window Functions - ROW_NUMBER(), PARTITION BY
✓ Agregações Temporais - COUNT CASE para múltiplas janelas
✓ Date Functions - julianday(), strftime(), datetime()
✓ JOINs Complexos - LEFT JOINs encadeados (7 tabelas)
✓ COALESCE - Tratamento de valores nulos
✓ Subqueries - Para cálculos de ranking
```

### **Técnicas de Engenharia de Features**

```sql
✓ Cálculo de diferença de datas (diffDate)
✓ Extração de componentes temporais (hora, dia da semana)
✓ Agregações em janelas rolantes (7, 14, 28, 56 dias)
✓ Separação de pontos positivos e negativos
✓ Ranking de produtos por cliente (ROW_NUMBER)
✓ Criação de flags categóricas (período do dia)
✓ Métricas de engajamento (razão transações recentes/vida)
```

---

## 📈 Principais Métricas Calculadas

### **1. Transações por Janela Temporal**

```sql
-- Exemplo do código real
qtdeTransacoesVida      -- Total lifetime
qtdeTransacoes56        -- Últimos 56 dias (2 meses)
qtdeTransacoes28        -- Últimos 28 dias (1 mês)
qtdeTransacoes14        -- Últimos 14 dias (2 semanas)
qtdeTransacoes7         -- Últimos 7 dias (1 semana)
```

**Por que múltiplas janelas?**
- Detectar mudanças no comportamento (ex: cliente ativo virou inativo)
- Identificar padrões sazonais
- Criar features preditivas para churn

---

### **2. Análise de Pontos (Positivos e Negativos)**

```sql
-- Pontos GANHOS (transações positivas)
qtdePontosPosVida       -- Total lifetime
qtdePontosPos28         -- Últimos 28 dias
...

-- Pontos GASTOS (transações negativas)
qtdePontosNegVida       -- Total lifetime
qtdePontosNeg28         -- Últimos 28 dias
...

-- Saldo atual
saldoPontos             -- Pontos disponíveis
```

**Insights de Negócio:**
- Clientes com muitos pontos não resgatados → Campanhas de incentivo
- Clientes com saldo negativo → Risco de churn
- Proporção ganho/gasto → Indicador de engajamento

---

### **3. Produto Favorito por Janela Temporal**

```sql
-- ROW_NUMBER() para rankear produtos por cliente
produtoVida             -- Produto mais comprado (lifetime)
produto56               -- Produto favorito últimos 56 dias
produto28               -- Produto favorito últimos 28 dias
produto14               -- Produto favorito últimos 14 dias
produto7                -- Produto favorito últimos 7 dias
```

**Aplicação:**
- Recomendação de produtos
- Detecção de mudança de preferência
- Campanhas personalizadas

---

### **4. Padrões Temporais**

```sql
-- Dia da semana com mais transações (últimos 28 dias)
dtDia                   -- 0=Domingo, 1=Segunda, ..., 6=Sábado

-- Período do dia preferido (últimos 28 dias)
periodoMaisTransacao28  -- MANHÃ, TARDE, NOITE, MADRUGADA
```

**Aplicação:**
- Otimizar timing de campanhas de marketing
- Planejar promoções em dias/horários estratégicos
- Ajustar disponibilidade de estoque

---

### **5. Métricas de Engajamento**

```sql
-- Idade do cliente na base
idadeBase               -- Dias desde cadastro

-- Recência
diasUltimaInteracao     -- Dias desde última transação

-- Engajamento relativo
engajamento28Vida       -- Razão: transações 28d / transações lifetime
```

**Interpretação:**
- `engajamento28Vida` alto → Cliente ativo e engajado
- `engajamento28Vida` baixo → Cliente com risco de churn
- `diasUltimaInteracao` > 60 → Cliente inativo

---

## 🏗️ Arquitetura da ETL

### **Pipeline de Transformação (10 CTEs Encadeadas)**

```
┌──────────────────────┐
│  1. tb_transacoes    │  ← Preparação de datas e cálculo diffDate
└─────────┬────────────┘
          │
          ▼
┌──────────────────────┐
│  2. tb_cliente       │  ← Cálculo idade base do cliente
└─────────┬────────────┘
          │
          ▼
┌──────────────────────┐
│ 3. tb_sumario_trans  │  ← Agregações temporais (7,14,28,56,vida)
└─────────┬────────────┘
          │
          ▼
┌──────────────────────┐
│ 4. tb_transacao_prod │  ← JOIN com produtos
└─────────┬────────────┘
          │
          ▼
┌──────────────────────┐
│ 5. tb_cliente_prod   │  ← Agregação produto por cliente
└─────────┬────────────┘
          │
          ▼
┌──────────────────────┐
│ 6. tb_cliente_prod_rn│  ← Ranking de produtos (ROW_NUMBER)
└─────────┬────────────┘
          │
          ▼
┌──────────────────────┐
│ 7. tb_cliente_dia    │  ← Agregação por dia da semana
└─────────┬────────────┘
          │
          ▼
┌──────────────────────┐
│ 8. tb_cliente_dia_rn │  ← Ranking de dias
└─────────┬────────────┘
          │
          ▼
┌──────────────────────┐
│ 9. tb_cliente_periodo│  ← Agregação por período do dia
└─────────┬────────────┘
          │
          ▼
┌──────────────────────┐
│10. tb_cliente_per_rn │  ← Ranking de períodos
└─────────┬────────────┘
          │
          ▼
┌──────────────────────┐
│  11. tb_join         │  ← JOIN de todas as CTEs
└─────────┬────────────┘
          │
          ▼
┌──────────────────────┐
│  SELECT FINAL        │  ← Base analítica completa
└──────────────────────┘
```

---

## 💻 Código SQL Detalhado

### **CTE 1: Preparação de Transações**

```sql
WITH tb_transacoes AS (
    SELECT 
        IdTransacao,
        IdCliente,
        QtdePontos,
        datetime(substr(dtCriacao,1,19)) AS dtCriacao,
        
        -- Diferença em dias entre data de referência e transação
        julianday('{date}') - julianday(substr(dtCriacao,1,10)) AS diffDate,
        
        -- Extração da hora
        CAST(strftime('%H', substr(dtCriacao,1,19)) AS INTEGER) AS dtHora

    FROM transacoes
    WHERE dtCriacao < '{date}'
)
```

**Técnicas aplicadas:**
- `julianday()` → Conversão de datas para cálculos
- `strftime()` → Extração de componentes de data/hora
- `substr()` → Manipulação de strings de data

---

### **CTE 3: Agregações Temporais (Exemplo Real)**

```sql
tb_sumario_transacoes AS (
    SELECT 
        IdCliente,

        -- Total de transações em cada janela temporal
        count(IdTransacao) AS qtdeTransacoesVida,
        count(CASE WHEN diffDate <= 56 THEN IdTransacao END) AS qtdeTransacoes56,
        count(CASE WHEN diffDate <= 28 THEN IdTransacao END) AS qtdeTransacoes28,
        count(CASE WHEN diffDate <= 14 THEN IdTransacao END) AS qtdeTransacoes14,
        count(CASE WHEN diffDate <= 7 THEN IdTransacao END) AS qtdeTransacoes7,

        -- Saldo de pontos
        sum(qtdePontos) AS saldoPontos,

        -- Recência
        min(diffDate) AS diasUltimaInteracao,

        -- Pontos POSITIVOS (ganhos) por janela
        sum(CASE WHEN qtdePontos > 0 THEN qtdePontos ELSE 0 END) AS qtdePontosPosVida,
        sum(CASE WHEN qtdePontos > 0 AND diffDate <= 28 THEN qtdePontos ELSE 0 END) AS qtdePontosPos28,
        -- ... (outras janelas)

        -- Pontos NEGATIVOS (gastos) por janela
        sum(CASE WHEN qtdePontos < 0 THEN qtdePontos ELSE 0 END) AS qtdePontosNegVida,
        sum(CASE WHEN qtdePontos < 0 AND diffDate <= 28 THEN qtdePontos ELSE 0 END) AS qtdePontosNeg28
        -- ... (outras janelas)

    FROM tb_transacoes
    GROUP BY IdCliente
)
```

**Técnica chave:** `COUNT CASE` para contar condicionalmente em múltiplas janelas numa única query!

---

### **CTE 6: Ranking de Produtos (Window Function)**

```sql
tb_cliente_produto_rn AS (
    SELECT *,
        -- Rankear produtos por quantidade de compras em cada janela
        row_number() OVER (PARTITION BY IdCliente ORDER BY qtdeVida DESC) AS rnVida,
        row_number() OVER (PARTITION BY IdCliente ORDER BY qtde56 DESC) AS rn56,
        row_number() OVER (PARTITION BY IdCliente ORDER BY qtde28 DESC) AS rn28,
        row_number() OVER (PARTITION BY IdCliente ORDER BY qtde14 DESC) AS rn14,
        row_number() OVER (PARTITION BY IdCliente ORDER BY qtde7 DESC) AS rn7

    FROM tb_cliente_produto
)
```

**Window Function aplicada:** `ROW_NUMBER() OVER (PARTITION BY ... ORDER BY ...)`

**Resultado:** Para cada cliente, rankeia produtos do mais comprado (#1) ao menos comprado.

---

### **CTE 9: Período do Dia (Lógica CASE)**

```sql
tb_cliente_periodo AS (
    SELECT 
        idCliente,
        CASE
            WHEN dtHora BETWEEN 7 AND 12 THEN 'MANHÃ'
            WHEN dtHora BETWEEN 13 AND 18 THEN 'TARDE'
            WHEN dtHora BETWEEN 19 AND 23 THEN 'NOITE'
            ELSE 'MADRUGADA'
        END AS periodo,
        COUNT(*) AS qtdeTransacao

    FROM tb_transacoes
    WHERE diffDate <= 28
    GROUP BY 1,2
)
```

**Categorização inteligente:** Converte hora numérica em período compreensível para análise.

---

### **CTE 11: JOIN Final (7 LEFT JOINs Encadeados!)**

```sql
tb_join AS (
    SELECT 
        t1.*,                                                    -- Sumário transações
        t2.idadeBase,                                           -- Cliente
        t3.DescProduto AS produtoVida,                          -- Produto #1 vida
        t4.DescProduto AS produto56,                            -- Produto #1 56d
        t5.DescProduto AS produto28,                            -- Produto #1 28d
        t6.DescProduto AS produto14,                            -- Produto #1 14d
        t7.DescProduto AS produto7,                             -- Produto #1 7d
        COALESCE(t8.dtDia, -1) AS dtDia,                       -- Dia favorito
        COALESCE(t9.periodo, 'SEM INFORMACAO') AS periodoMaisTransacao28  -- Período favorito

    FROM tb_sumario_transacoes AS t1

    LEFT JOIN tb_cliente AS t2
    ON t1.idCliente = t2.idCliente

    LEFT JOIN tb_cliente_produto_rn AS t3
    ON t1.idCliente = t3.idCliente AND t3.rnVida = 1  -- Pega apenas rank #1

    -- ... (outros 6 JOINs)
)
```

**Técnica:** Filtrar `rn = 1` no JOIN para pegar apenas o TOP 1 produto/dia/período!

---

## 📊 Resultado Final: Base Analítica

### **Estrutura da Tabela Final**

A query retorna **1 linha por cliente** com ~40 colunas:

| Categoria | Colunas | Exemplo |
|-----------|---------|---------|
| **Identificação** | `IdCliente` | 12345 |
| **Data Referência** | `dtRef` | 2024-01-30 |
| **Transações** | `qtdeTransacoesVida`, `qtdeTransacoes28`, ... | 45, 8, ... |
| **Pontos** | `saldoPontos`, `qtdePontosPos28`, `qtdePontosNeg28` | 1200, 350, -150 |
| **Recência** | `diasUltimaInteracao` | 5 |
| **Produtos** | `produtoVida`, `produto28`, ... | "Mouse Gamer", "Teclado" |
| **Temporal** | `dtDia`, `periodoMaisTransacao28` | 5 (sexta), "TARDE" |
| **Cliente** | `idadeBase` | 365 (1 ano na base) |
| **Engajamento** | `engajamento28Vida` | 0.42 (42% das transações nos últimos 28d) |

---

## 🎯 Aplicações Práticas

### **1. Segmentação de Clientes**

```sql
-- Classificar clientes por engajamento
SELECT 
    IdCliente,
    CASE 
        WHEN engajamento28Vida >= 0.5 THEN 'MUITO ATIVO'
        WHEN engajamento28Vida >= 0.3 THEN 'ATIVO'
        WHEN engajamento28Vida >= 0.1 THEN 'MODERADO'
        ELSE 'INATIVO'
    END AS segmento_engajamento
FROM resultado_final;
```

---

### **2. Detecção de Churn**

```sql
-- Clientes em risco
SELECT 
    IdCliente,
    diasUltimaInteracao,
    qtdeTransacoesVida,
    qtdeTransacoes28
FROM resultado_final
WHERE diasUltimaInteracao > 60          -- Sem transação há 2 meses
  AND qtdeTransacoesVida > 10           -- Era cliente ativo
  AND qtdeTransacoes28 = 0;             -- Parou de comprar
```

---

### **3. Recomendação de Produtos**

```sql
-- Clientes que mudaram de produto favorito
SELECT 
    IdCliente,
    produtoVida AS produto_historico,
    produto28 AS produto_recente
FROM resultado_final
WHERE produtoVida != produto28
  AND produto28 IS NOT NULL;
```

**Insight:** Cliente mudou preferência → Oportunidade de cross-sell!

---

### **4. Otimização de Campanhas**

```sql
-- Melhor horário para enviar email por cliente
SELECT 
    IdCliente,
    periodoMaisTransacao28,
    COUNT(*) AS total_clientes
FROM resultado_final
GROUP BY periodoMaisTransacao28;

-- Resultado exemplo:
-- TARDE:     4,500 clientes → Enviar emails 14h-18h
-- NOITE:     3,200 clientes → Enviar emails 19h-22h
-- MANHÃ:     1,800 clientes → Enviar emails 9h-12h
-- MADRUGADA:   500 clientes → Não enviar (baixa conversão)
```

---

## 💡 Aprendizados Técnicos

### **SQL Avançado**

✅ **CTEs encadeadas:** Organizar lógica complexa em blocos legíveis  
✅ **Window Functions:** Ranking e particionamento de dados  
✅ **COUNT CASE:** Agregações condicionais em múltiplas janelas numa única query  
✅ **Date Functions:** Manipulação avançada de datas (julianday, strftime)  
✅ **JOINs complexos:** Combinar 7+ tabelas mantendo performance  
✅ **COALESCE:** Tratamento elegante de nulos  

### **Engenharia de Features**

✅ **Janelas temporais:** 7d, 14d, 28d, 56d para capturar mudanças de comportamento  
✅ **Métricas derivadas:** Engajamento relativo, recência, frequência  
✅ **Categorização:** Período do dia, dia da semana  
✅ **Ranking:** Top produtos por cliente  
✅ **Flags booleanas:** Pontos positivos vs negativos  

### **Boas Práticas**

✅ **Modularização:** CTEs = blocos reutilizáveis e testáveis  
✅ **Nomenclatura:** Nomes descritivos (`qtdeTransacoes28` vs `t28`)  
✅ **Comentários:** Explicar lógica complexa  
✅ **Performance:** Filtrar cedo (`WHERE dtCriacao < '{date}'`)  
✅ **Manutenibilidade:** Estrutura fácil de estender  

---

## 🚀 Próximas Etapas

### **Fase 2: Integração com Python**

```python
import pandas as pd
import sqlite3

# Conectar e executar ETL
conn = sqlite3.connect('clientes.db')
df = pd.read_sql_query(open('ETL_PROJETO.sql').read(), conn)

# Features prontas para ML!
X = df.drop(['IdCliente', 'dtRef'], axis=1)
y = df['churn']  # Target para modelo de churn
```

---

### **Fase 3: Dashboard Power BI/Streamlit**

**KPIs principais:**
- Taxa de engajamento média
- Distribuição de clientes por segmento
- Produtos mais vendidos por período
- Melhor dia/horário para campanhas

---

### **Fase 4: Modelos Preditivos**

**Com estas features, é possível treinar:**
- ✅ Modelo de **Churn** (classificação)
- ✅ Modelo de **Propensão de Compra** (classificação)
- ✅ Modelo de **LTV** - Lifetime Value (regressão)
- ✅ Modelo de **Recomendação** de Produtos

---

## 📁 Estrutura do Projeto

```
sql-analise-engajamento-clientes/
│
├── README.md                          # Esta documentação
│
├── data/                              # Dados brutos
│   ├── clientes.csv
│   ├── produtos.csv
│   ├── transacoes.csv
│   └── transacao_produto.csv
│
├── sql/
│   └── ETL_PROJETO.sql                # Pipeline ETL completa ⭐
│
└── docs/                              # Documentação adicional
    ├── modelo_dados.png               # Diagrama ER
    └── dicionario_features.md         # Descrição das 40 colunas
```

---

## 🛠️ Como Executar

### **Pré-requisitos**
- SQLite 3+
- Python 3.8+ (opcional, para análises)

### **Execução**

```bash
# 1. Criar banco de dados SQLite
sqlite3 clientes.db

# 2. Importar dados (dentro do SQLite)
.mode csv
.import data/clientes.csv clientes
.import data/produtos.csv produtos
.import data/transacoes.csv transacoes
.import data/transacao_produto.csv transacao_produto

# 3. Executar ETL (substituir {date} por data de referência)
.read sql/ETL_PROJETO.sql
```

### **Ou com Python:**

```python
import sqlite3
import pandas as pd
from datetime import datetime

# Conectar
conn = sqlite3.connect('clientes.db')

# Ler ETL
with open('sql/ETL_PROJETO.sql', 'r') as f:
    query = f.read().replace('{date}', '2024-01-30')

# Executar
df_resultado = pd.read_sql_query(query, conn)

# Salvar resultado
df_resultado.to_csv('resultado_abt_clientes.csv', index=False)

print(f"✅ ABT criada com {len(df_resultado)} clientes e {len(df_resultado.columns)} features")
```

---

## 📈 Métricas do Projeto

| Métrica | Valor |
|---------|-------|
| **Linhas de SQL** | 200+ |
| **CTEs utilizadas** | 11 |
| **LEFT JOINs** | 7 |
| **Window Functions** | 5 |
| **Features geradas** | 40+ |
| **Janelas temporais** | 5 (vida, 56d, 28d, 14d, 7d) |
| **Complexidade** | Alta ⭐⭐⭐⭐⭐ |

---

## 🎓 Competências Demonstradas

### **SQL Avançado**
- ✅ Common Table Expressions (CTEs)
- ✅ Window Functions (ROW_NUMBER, PARTITION BY)
- ✅ Agregações condicionais (COUNT CASE)
- ✅ Date/Time manipulation (julianday, strftime)
- ✅ JOINs complexos encadeados
- ✅ Tratamento de nulos (COALESCE)

### **Análise de Dados**
- ✅ Engenharia de features
- ✅ Análise temporal multi-janela
- ✅ Segmentação de clientes
- ✅ Detecção de padrões comportamentais
- ✅ Preparação de dados para ML

### **Pensamento Analítico**
- ✅ Tradução de problema de negócio em SQL
- ✅ Design de pipeline ETL
- ✅ Criação de métricas acionáveis
- ✅ Documentação técnica clara

---

## 📝 Licença

Este projeto está sob a licença MIT. Veja o arquivo [LICENSE](LICENSE) para mais detalhes.

---

## 🙏 Agradecimentos

- **Téo Calvo (Téo Me Why)** - Pelo curso excepcional de SQL prático
- **EBAC** - Pela formação sólida em Ciência de Dados
- **Comunidade de Data Science** - Pelo suporte e aprendizado contínuo

---

## 📞 Contato

**Eduardo Matos**  
Cientista de Dados

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Eduardo_Matos-blue?style=for-the-badge&logo=linkedin)](https://linkedin.com/in/matos-eduardo)
[![GitHub](https://img.shields.io/badge/GitHub-edudatalytics-black?style=for-the-badge&logo=github)](https://github.com/edudatalytics)
[![Email](https://img.shields.io/badge/Email-eduardomatos2399@gmail.com-red?style=for-the-badge&logo=gmail)](mailto:eduardomatos2399@gmail.com)

---

<div align="center">

**⭐ Se este projeto foi útil, deixe uma estrela no repositório!**

![Visitors](https://visitor-badge.laobi.icu/badge?page_id=edudatalytics.sql-analise-engajamento-clientes)

*Desenvolvido como projeto final do curso de SQL do Téo Me Why*

</div>
