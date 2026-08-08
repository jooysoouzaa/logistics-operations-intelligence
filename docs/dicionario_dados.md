# Dicionário de Dados

Este documento apresenta as principais colunas utilizadas e criadas nas camadas Silver e Gold do projeto **Logistics Operations Intelligence**.

O dicionário técnico completo também foi armazenado no Databricks na tabela:

 
## Silver — trips

workspace.governance_logistics.data_dictionary

| Coluna                             | Tipo    | Descrição                                                  | Regra / Observação                              |
| ---------------------------------- | ------- | ---------------------------------------------------------- | ----------------------------------------------- |
| `trip_id`                          | string  | Identificador único da viagem.                             | Chave primária.                                 |
| `actual_distance_miles`            | int     | Distância efetivamente percorrida na viagem.               | Proveniente da fonte.                           |
| `actual_duration_hours`            | double  | Duração real da viagem em horas.                           | Proveniente da fonte.                           |
| `fuel_gallons_used`                | double  | Quantidade de combustível consumida na viagem.             | Proveniente da fonte.                           |
| `calculated_mpg`                   | double  | Eficiência calculada em milhas por galão.                  | `actual_distance_miles / fuel_gallons_used`     |
| `average_speed_mph`                | double  | Velocidade média calculada da viagem.                      | `actual_distance_miles / actual_duration_hours` |
| `idle_time_percentage`             | double  | Percentual do tempo da viagem em marcha lenta.             | Nulo quando o tempo ocioso é inconsistente.     |
| `idle_time_inconsistent`           | boolean | Indica inconsistência entre tempo ocioso e duração total.  | Usado como flag de qualidade.                   |
| `has_complete_resource_assignment` | boolean | Indica se motorista, caminhão e trailer estão preenchidos. | Indicador de completude.                        |

## Silver — loads

| Coluna                             | Tipo    | Descrição                                                  | Regra / Observação                              |
| ---------------------------------- | ------- | ---------------------------------------------------------- | ----------------------------------------------- |
| `trip_id`                          | string  | Identificador único da viagem.                             | Chave primária.                                 |
| `actual_distance_miles`            | int     | Distância efetivamente percorrida na viagem.               | Proveniente da fonte.                           |
| `actual_duration_hours`            | double  | Duração real da viagem em horas.                           | Proveniente da fonte.                           |
| `fuel_gallons_used`                | double  | Quantidade de combustível consumida na viagem.             | Proveniente da fonte.                           |
| `calculated_mpg`                   | double  | Eficiência calculada em milhas por galão.                  | `actual_distance_miles / fuel_gallons_used`     |
| `average_speed_mph`                | double  | Velocidade média calculada da viagem.                      | `actual_distance_miles / actual_duration_hours` |
| `idle_time_percentage`             | double  | Percentual do tempo da viagem em marcha lenta.             | Nulo quando o tempo ocioso é inconsistente.     |
| `idle_time_inconsistent`           | boolean | Indica inconsistência entre tempo ocioso e duração total.  | Usado como flag de qualidade.                   |
| `has_complete_resource_assignment` | boolean | Indica se motorista, caminhão e trailer estão preenchidos. | Indicador de completude.                        |

## Silver — routes

| Coluna                      | Tipo    | Descrição                                  | Regra / Observação                               |
| --------------------------- | ------- | ------------------------------------------ | ------------------------------------------------ |
| `load_id`                   | string  | Identificador único da carga.              | Chave primária.                                  |
| `customer_id`               | string  | Identificador do cliente.                  | FK para `customers`.                             |
| `route_id`                  | string  | Identificador da rota.                     | FK para `routes`.                                |
| `total_revenue`             | double  | Receita total associada à carga.           | `revenue + fuel_surcharge + accessorial_charges` |
| `revenue_per_piece`         | double  | Receita média por peça transportada.       | `revenue / pieces`                               |
| `revenue_per_lb`            | double  | Receita média por libra transportada.      | `revenue / weight_lbs`                           |
| `has_complete_relationship` | boolean | Indica se cliente e rota estão associados. | Indicador de integridade.                        |

## Silver — delivery_events

| Coluna                          | Tipo    | Descrição                                            | Regra / Observação                                |
| ------------------------------- | ------- | ---------------------------------------------------- | ------------------------------------------------- |
| `route_id`                      | string  | Identificador único da rota.                         | Chave primária.                                   |
| `origin_location`               | string  | Origem consolidada da rota.                          | Cidade + estado.                                  |
| `destination_location`          | string  | Destino consolidado da rota.                         | Cidade + estado.                                  |
| `route_description`             | string  | Descrição textual da rota.                           | Origem → destino.                                 |
| `estimated_rate_per_mile`       | double  | Tarifa estimada por milha.                           | Considera tarifa base e sobretaxa de combustível. |
| `estimated_route_cost`          | double  | Valor estimado da rota.                              | Distância típica × tarifa ajustada.               |
| `average_miles_per_transit_day` | double  | Média de milhas por dia de trânsito.                 | `typical_distance_miles / typical_transit_days`   |
| `has_complete_route`            | boolean | Indica se os principais dados da rota estão válidos. | Indicador de qualidade.                           |

## Silver — fuel_purchases

| Coluna                      | Tipo    | Descrição                                             | Regra / Observação                         |
| --------------------------- | ------- | ----------------------------------------------------- | ------------------------------------------ |
| `event_id`                  | string  | Identificador único do evento.                        | Chave primária.                            |
| `event_type`                | string  | Tipo do evento logístico.                             | `Pickup` ou `Delivery`.                    |
| `event_variance_minutes`    | double  | Diferença entre horário real e programado.            | Positivo = atraso; negativo = antecipação. |
| `delay_minutes`             | double  | Quantidade de minutos de atraso.                      | Zero quando não há atraso.                 |
| `early_minutes`             | double  | Quantidade de minutos de antecipação.                 | Zero quando não há antecipação.            |
| `is_on_time_calculated`     | boolean | Indicador recalculado de pontualidade.                | `actual_datetime <= scheduled_datetime`    |
| `on_time_flag_consistent`   | boolean | Compara o flag original com o recalculado.            | Indicador de consistência.                 |
| `has_detention`             | boolean | Indica ocorrência de tempo de detenção.               | `detention_minutes > 0`                    |
| `has_complete_relationship` | boolean | Indica se carga, viagem e facility estão preenchidos. | Indicador de integridade.                  |

## Gold — gold_trip_operations

| Coluna                             | Tipo    | Descrição                                                | Regra / Observação                                 |
| ---------------------------------- | ------- | -------------------------------------------------------- | -------------------------------------------------- |
| `fuel_purchase_id`                 | string  | Identificador único da compra de combustível.            | Chave primária.                                    |
| `trip_id`                          | string  | Viagem associada à compra.                               | FK para `trips`.                                   |
| `truck_id`                         | string  | Caminhão associado à compra.                             | Pode ser nulo.                                     |
| `driver_id`                        | string  | Motorista associado à compra.                            | Pode ser nulo.                                     |
| `calculated_total_cost`            | double  | Custo da compra recalculado.                             | `gallons × price_per_gallon`                       |
| `total_cost_difference`            | double  | Diferença entre custo informado e recalculado.           | Usado na validação financeira.                     |
| `has_consistent_total_cost`        | boolean | Indica se o custo está dentro da tolerância definida.    | Tolerância de até 0,02.                            |
| `has_truck`                        | boolean | Indica se a compra possui caminhão associado.            | Indicador de completude.                           |
| `has_driver`                       | boolean | Indica se a compra possui motorista associado.           | Indicador de completude.                           |
| `has_complete_resource_assignment` | boolean | Indica se caminhão e motorista estão preenchidos.        | Indicador de completude.                           |
| `truck_matches_trip`               | boolean | Compara o caminhão da compra com o caminhão da viagem.   | Pode ser nulo quando não há informação suficiente. |
| `driver_matches_trip`              | boolean | Compara o motorista da compra com o motorista da viagem. | Pode ser nulo quando não há informação suficiente. |


## Gold — gold_delivery_performance

| Coluna                          | Tipo    | Descrição                                              | Regra / Observação             |
| ------------------------------- | ------- | ------------------------------------------------------ | ------------------------------ |
| `trip_id`                       | string  | Identificador da viagem.                               | Chave da visão analítica.      |
| `pickup_delay_minutes`          | double  | Atraso do evento de coleta.                            | Derivado de `delivery_events`. |
| `delivery_delay_minutes`        | double  | Atraso do evento de entrega.                           | Derivado de `delivery_events`. |
| `total_detention_minutes`       | double  | Soma da detenção na coleta e entrega.                  | Pickup + Delivery.             |
| `has_any_delay`                 | boolean | Indica se houve atraso em algum evento.                | Pickup ou Delivery atrasado.   |
| `both_events_on_time`           | boolean | Indica se coleta e entrega ocorreram no prazo.         | Ambos os eventos pontuais.     |
| `delivery_cycle_hours`          | double  | Tempo entre a coleta real e a entrega real.            | Calculado em horas.            |
| `has_source_flag_inconsistency` | boolean | Indica divergência nos flags de pontualidade da fonte. | Indicador de qualidade.        |


## Gold — gold_fuel_performance

| Coluna                                 | Tipo    | Descrição                                                    | Regra / Observação                        |
| -------------------------------------- | ------- | ------------------------------------------------------------ | ----------------------------------------- |
| `trip_id`                              | string  | Identificador da viagem.                                     | Chave da visão analítica.                 |
| `fuel_purchase_count`                  | long    | Quantidade de abastecimentos associados à viagem.            | Agregação de `fuel_purchases`.            |
| `total_gallons_purchased`              | double  | Total de galões adquiridos.                                  | Soma por `trip_id`.                       |
| `total_fuel_cost`                      | double  | Custo total de combustível da viagem.                        | Soma por `trip_id`.                       |
| `average_price_per_gallon`             | double  | Preço médio por galão.                                       | Média das compras.                        |
| `fuel_cost_per_mile`                   | double  | Custo de combustível por milha.                              | `total_fuel_cost / actual_distance_miles` |
| `purchased_vs_used_gallons_difference` | double  | Diferença entre combustível comprado e utilizado.            | Comprado - consumido.                     |
| `has_fuel_purchase`                    | boolean | Indica se há abastecimento associado à viagem.               | Indicador de cobertura.                   |
| `has_complete_fuel_data`               | boolean | Indica se os dados agregados de combustível estão completos. | Indicador de completude.                  |

