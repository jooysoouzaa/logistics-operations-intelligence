# Governança de Dados

A governança de dados do projeto **Logistics Operations Intelligence** foi implementada para aumentar a rastreabilidade, padronização, qualidade e compreensão dos dados ao longo das camadas Bronze, Silver e Gold.

Foi criado o schema:

```text
workspace.governance_logistics

## Data Catalog

A tabela data_catalog documenta as principais tabelas do projeto.

Para cada tabela, foram registrados metadados como:

camada;
descrição de negócio;
granularidade;
chave primária;
chaves estrangeiras;
domínio de dados.

O objetivo é facilitar a descoberta e o entendimento das tabelas.

## Data Dictionary

A tabela data_dictionary documenta as principais colunas criadas ou utilizadas nas camadas Silver e Gold.

Foram registrados atributos como:

nome da tabela;
nome da coluna;
tipo de dado;
descrição de negócio;
regra de cálculo;
possibilidade de valor nulo;
observações de governança.

O dicionário de dados complementa o catálogo ao detalhar o significado dos campos.

## Data Quality Rules

A tabela data_quality_rules reúne as regras de qualidade definidas para o projeto.

As regras foram organizadas por dimensões como:

unicidade;
completude;
integridade;
consistência;
validade.

Cada regra também possui uma classificação de severidade.

Exemplos:

trip_id deve ser único;
trip_id não pode ser nulo;
customer_id deve existir em customers;
route_id deve existir em routes;
cada viagem deve possuir um evento de Pickup e um evento de Delivery;
o custo total de combustível deve ser consistente com gallons × price_per_gallon.

## Data Quality Results

A tabela data_quality_results registra os resultados das validações realizadas.

Os resultados podem assumir estados como:

PASS;
WARNING.

Também são registrados:

número de registros afetados;
observação sobre o resultado da regra.

Essa abordagem permite separar a definição da regra de seu resultado real.

## Data Lineage

A tabela data_lineage documenta as principais dependências entre tabelas Silver e Gold.

Exemplo:

silver.trips
silver.loads
silver.routes
silver.drivers
silver.trucks
        ↓
gold.gold_trip_operations

Também foram registrados os tipos de transformação aplicados, como:

joins;
enriquecimento;
agregações;
consolidação por viagem.

## Unity Catalog

Além da documentação manual, o projeto utiliza o Unity Catalog do Databricks.

O Unity Catalog permite:

organização de catálogos, schemas e tabelas;
rastreabilidade automática;
lineage entre tabelas;
visualização das dependências de dados;
gerenciamento centralizado dos objetos.

No projeto, o lineage automático foi validado principalmente nas tabelas Gold.

## Abordagem adotada

A governança foi tratada de forma complementar à transformação dos dados.

Os dados originais não foram alterados arbitrariamente quando uma regra de negócio não estava clara.

Em vez disso, foram criados indicadores de qualidade e consistência para preservar:

rastreabilidade;
transparência;
capacidade de investigação posterior.

Essa abordagem foi utilizada, por exemplo, nas divergências encontradas no indicador on_time_flag e nos registros de combustível sem motorista ou caminhão associado.
