# Arquitetura do Projeto

O projeto **Logistics Operations Intelligence** foi desenvolvido no Databricks utilizando arquitetura Medalhão, com separação dos dados em camadas Bronze, Silver e Gold, além de uma camada específica de Governança de Dados.

## Visão Geral

Fluxo principal:

Fonte de Dados
    ↓
Bronze
    ↓
Silver
    ↓
Gold
    ↓
## Consumo Analítico


A governança atua de forma transversal sobre as camadas, apoiando catálogo, qualidade, linhagem e documentação dos dados.

## Camada Bronze

A camada Bronze é responsável pela ingestão e preservação dos dados em seu formato mais próximo da origem.

Principais tabelas:

trips
loads
routes
customers
drivers
trucks
delivery_events
fuel_purchases

Nesta camada, o objetivo principal é manter rastreabilidade e preservar os dados brutos para processamento posterior.

## Camada Silver

A camada Silver concentra as etapas de limpeza, padronização, validação e enriquecimento.

Principais processos aplicados:

padronização de campos textuais;
tratamento e monitoramento de valores nulos;
validação de chaves primárias;
validação de integridade referencial;
criação de atributos temporais;
criação de métricas derivadas;
criação de indicadores de qualidade;
preservação de inconsistências relevantes para análise posterior.

Exemplos de métricas criadas:

MPG calculado;
velocidade média;
percentual de tempo ocioso;
receita total;
receita por peça;
receita por libra;
atraso e antecipação de eventos;
custo recalculado de combustível.

## Camada Gold

A camada Gold foi modelada para consumo analítico e possui granularidade orientada ao negócio.

Foram criadas três tabelas principais:

gold_trip_operations

## Visão consolidada das viagens, integrando:

viagens;
cargas;
rotas;
motoristas;
caminhões.

Inclui métricas operacionais e financeiras, como desvio de distância, receita por milha e margem operacional estimada.

## gold_delivery_performance

Visão de desempenho de coleta e entrega, contendo:

horários programados e realizados;
atrasos;
antecipações;
tempo de detenção;
tempo de ciclo da entrega;
indicadores de pontualidade.
gold_fuel_performance

## Visão consolidada de combustível por viagem, contendo:

quantidade de abastecimentos;
total de galões;
custo total;
preço médio por galão;
custo por milha;
indicadores de completude e consistência.
Governança de Dados

## Foi criado o schema:

workspace.governance_logistics

com os seguintes artefatos:

data_catalog
data_dictionary
data_quality_rules
data_quality_results
data_lineage

Também foi utilizado o Unity Catalog para organização dos objetos e rastreabilidade automática das dependências entre tabelas.

## Organização no Databricks

A arquitetura final foi organizada da seguinte forma:

workspace
├── bronze_logistics
├── silver_logistics
├── gold_logistics
└── governance_logistics

## Essa separação permite distinguir claramente:

dados brutos;
dados tratados;
dados analíticos;
metadados e controles de governança.
Tecnologias Utilizadas
Databricks
PySpark
Spark SQL
Delta Lake
Unity Catalog
Git
GitHub

Esse arquivo complementa bem o README porque entra mais no **como a arquitetura foi estruturada**, sem ficar repetindo os achados de qualidade.


