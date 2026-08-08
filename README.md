# Logistics Operations Intelligence

Projeto end-to-end de Engenharia e Analytics de Dados desenvolvido no Databricks, utilizando PySpark, Delta Lake, arquitetura Medalhão e práticas de Governança de Dados.

## Objetivo

Construir uma arquitetura de dados logística capaz de:

- ingerir dados brutos;
- aplicar tratamento e regras de qualidade;
- organizar os dados nas camadas Bronze, Silver e Gold;
- criar tabelas analíticas orientadas ao negócio;
- implementar catálogo, dicionário, regras de qualidade e linhagem;
- preparar os dados para consumo analítico futuro em Power BI.

## Arquitetura

Fonte de dados
↓
Bronze
↓
Silver
↓
Gold
↓
Consumo analítico

Governança transversal:
- Data Catalog
- Data Dictionary
- Data Quality Rules
- Data Quality Results
- Data Lineage
- Unity Catalog Lineage

## Tecnologias

- Databricks
- PySpark
- Delta Lake
- Unity Catalog
- Git
- GitHub

## Camada Bronze

Responsável pela ingestão e preservação dos dados de origem.

Principais tabelas:

- trips
- loads
- routes
- customers
- drivers
- trucks
- delivery_events
- fuel_purchases

## Camada Silver

Responsável pela padronização, validação e enriquecimento dos dados.

Foram aplicadas regras de:

- unicidade;
- completude;
- integridade referencial;
- consistência;
- validade;
- padronização textual;
- criação de métricas derivadas;
- criação de flags de qualidade.

## Camada Gold

Foram construídas três tabelas analíticas:

### gold_trip_operations

Visão consolidada das viagens, integrando informações operacionais, financeiras, de rota, motorista e caminhão.

### gold_delivery_performance

Visão de desempenho de coleta e entrega, incluindo atrasos, antecipações, detenção e pontualidade.

### gold_fuel_performance

Visão consolidada de consumo e custos de combustível por viagem.

## Governança de Dados

Foi criada uma camada específica de governança contendo:

- `data_catalog`
- `data_dictionary`
- `data_quality_rules`
- `data_quality_results`
- `data_lineage`

Também foi utilizado o Unity Catalog para organização dos objetos e rastreabilidade automática de linhagem entre tabelas Silver e Gold.

## Principais achados de qualidade

Durante as validações foram identificados:

- 56.866 divergências entre `on_time_flag` e o indicador de pontualidade recalculado;
- 3.880 compras de combustível sem `truck_id`;
- 3.988 compras de combustível sem `driver_id`;
- nenhum relacionamento órfão entre fuel_purchases e trips;
- nenhum custo de combustível inconsistente;
- preservação da granularidade das tabelas Gold.

## Linhagem de Dados com Unity Catalog

O Unity Catalog foi utilizado para rastrear automaticamente as dependências entre as tabelas Silver e a tabela analítica `gold_trip_operations`.

<img width="1401" height="805" alt="Unity Catalog Lineage" src="https://github.com/user-attachments/assets/7e4c7f4a-b37a-423e-a2d5-a309b4cc75f6" />

**Estrutura do catálogo no Databricks.**  
Organização das camadas `bronze`, `silver`, `gold` e da camada de `governance`, com destaque para as tabelas analíticas e artefatos de governança criados no projeto.

<img width="470" height="380" alt="Estrutura do Catalog" src="https://github.com/user-attachments/assets/5f6a487a-96a1-4331-b7fd-894f05e7148f" />


**Amostra da tabela analítica `gold_trip_operations`.**  
Visão consolidada por viagem, reunindo métricas operacionais como distância percorrida, duração, consumo de combustível, eficiência média, tempo ocioso e status da operação.

<img width="1264" height="693" alt="gold_trip_operations" src="https://github.com/user-attachments/assets/c3b324b5-2cbc-4186-bf38-040e9e004659" />


## Estrutura do repositório

```
logistics-operations-intelligence/
├── notebooks/
│   ├── 01_ingestao_bronze.ipynb
│   ├── 02_transformacao_silver.ipynb
│   ├── 03_transformacao_gold.ipynb
│   └── 04_governanca_dados.ipynb
│
├── docs/
├── images/
├── .gitignore
├── LICENSE
└── README.md
