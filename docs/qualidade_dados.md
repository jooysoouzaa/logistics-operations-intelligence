# Qualidade de Dados

A qualidade de dados foi tratada ao longo das camadas Silver e Gold por meio de validações de unicidade, completude, integridade, consistência e validade.

As regras foram documentadas no schema:


workspace.governance_logistics

por meio das tabelas:

data_quality_rules
data_quality_results

## Dimensões de qualidade utilizadas

As principais dimensões aplicadas no projeto foram:

Unicidade: validação de chaves primárias sem duplicidade;
Completude: identificação de campos obrigatórios ausentes;
Integridade: validação de relacionamentos entre tabelas;
Consistência: comparação entre campos relacionados e métricas recalculadas;
Validade: verificação de valores dentro de regras esperadas.


## Principais regras aplicadas

## Trips
trip_id deve ser único;
trip_id não pode ser nulo;
load_id deve existir em loads;
motorista e caminhão devem ser monitorados quando ausentes;
métricas como MPG e velocidade média devem respeitar regras válidas de cálculo.

## Loads
load_id deve ser único;
customer_id deve existir em customers;
route_id deve existir em routes;
valores financeiros devem ser positivos e consistentes.

## Delivery Events
event_id deve ser único;
trip_id deve existir em trips;
load_id deve existir em loads;
cada viagem deve possuir um evento Pickup e um evento Delivery;
datas programadas e realizadas devem estar preenchidas;
tempos de detenção não podem ser negativos;
o indicador on_time_flag deve ser comparado com o indicador recalculado.

## Fuel Purchases
fuel_purchase_id deve ser único;
trip_id deve existir em trips;
truck_id, quando preenchido, deve existir em trucks;
driver_id, quando preenchido, deve existir em drivers;
total_cost deve ser consistente com gallons × price_per_gallon;
galões, preço e custo devem ser maiores que zero.

## Principais resultados

As validações identificaram os seguintes resultados relevantes:

## Integridade
nenhum trip_id inválido em fuel_purchases;
nenhum truck_id preenchido e inexistente;
nenhum driver_id preenchido e inexistente;
nenhum relacionamento órfão entre delivery_events, loads e trips.

## Unicidade

As principais chaves primárias permaneceram únicas após as transformações:

trip_id;
load_id;
route_id;
driver_id;
truck_id;
event_id;
fuel_purchase_id.

## Consistência financeira

Foram analisadas 196.442 compras de combustível.

Todas foram consideradas consistentes dentro da tolerância definida para arredondamento.

custos consistentes: 196.442
custos inconsistentes: 0
maior diferença encontrada: 0,01

## Achados de qualidade
### Divergência de pontualidade

Foram identificadas:

56.866 divergências

entre o campo on_time_flag recebido da fonte e o indicador recalculado a partir de:

actual_datetime <= scheduled_datetime

Como a regra original da fonte não estava documentada, o valor original não foi sobrescrito.

### Foram preservados:

on_time_flag;
is_on_time_calculated;
on_time_flag_consistent.

Essa abordagem mantém a rastreabilidade e permite investigação posterior da regra de negócio utilizada na origem.

### Compras de combustível sem caminhão

Foram identificados:

3.880 registros

sem truck_id.

Os registros foram preservados e sinalizados por meio do indicador:

has_truck

### Compras de combustível sem motorista

Foram identificados:

3.988 registros

sem driver_id.

Os registros também foram preservados e sinalizados por meio do indicador:

has_driver

### Recursos completos

Das 196.442 compras de combustível, 188.668 possuem simultaneamente caminhão e motorista identificados.

Esse controle é registrado pelo indicador:

has_complete_resource_assignment

### Estratégia de tratamento

O projeto adotou o princípio de não corrigir dados sem uma regra de negócio conhecida.

Quando uma inconsistência foi identificada, foram priorizadas as seguintes ações:

preservar o valor original;
criar métricas recalculadas separadamente;
criar flags de qualidade;
documentar a ocorrência;
manter o registro disponível para investigação posterior.

Essa estratégia evita alterações arbitrárias e aumenta a rastreabilidade das transformações.

## Qualidade na camada Gold

As tabelas Gold também foram validadas para garantir a preservação da granularidade.

As três principais tabelas mantiveram:

85.410 registros
85.410 trip_ids únicos
0 trip_ids nulos

nas respectivas visões analíticas por viagem.

As validações garantem que os joins e agregações realizados na camada Gold não introduziram duplicidades ou perdas inesperadas de registros.
