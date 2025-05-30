# Relatório de Análise de Cancelamentos

<hr>

## Contexto

A rotatividade de clientes (churn) é um dos maiores desafios enfrentados pelas empresas de telecomunicações. Este relatório apresenta uma análise detalhada do dataset de clientes da empresa, com objetivo de identificar padrões de cancelamento, prever futuros cancelamentos e recomendar ações de retenção.

<hr>

### Principais Descobertas:

- Taxa de churn média: 26%
- Clientes com contrato mês a mês têm maior probabilidade de cancelar
- Tempo como cliente (tenure) é um fator crítico
- Valor pago mensalmente tem forte correlação com o churn
- Serviços extras (segurança online, backup, suporte técnico) ajudam na retenção

<hr>

## Introdução

Este relatório foi desenvolvido com base em um dataset contendo informações de 7.043 clientes de uma operadora de telecomunicações. As variáveis incluem dados demográficos, tipos de serviço, forma de pagamento e status de churn.

#### Variáveis Selecionadas:

- <b>`gender`:</b> Gênero do cliente
- <b>`SeniorCitizen`:</b> Indicador se é idoso
- <b>`Partner` / `Dependents`:</b> Possui parceiro(a) ou dependentes
- <b>`tenure`:</b> Tempo como cliente em meses
- <b>`MonthlyCharges` / `TotalCharges`:</b> Valores pagos
- <b>`Contract`:</b> Tipo de contrato
- <b>`PaymentMethod`:</b> Método de pagamento
- <b>`Churn`:</b> Indicador de cancelamento (`Yes` ou `No`)

<hr>

## Análise Exploratória de Dados 

#### Estatísticas Descritivas

| Variável | Média | Mediana | Mínimo | Máximo |
|---------|-------|---------|--------|--------|
| Tenure | 32.37 meses | 29 meses | 0 | 72 |
| MonthlyCharges | R$ 64,76 | R$ 70,35 | R$ 18,25 | R$ 118,75 |
| TotalCharges | R$ 2.283,30 | R$ 1.397,48 | 0 | R$ 8.684,80 |

#### Taxa Global de Churn

- <b>Clientes ativos:</b> 5.174 (73.5%)
- <b>Clientes que cancelaram:</b> 1.869 (26.5%)

<hr>

## Visualizações

### Churn por Contrato

Clientes com contrato **mês a mês** têm taxa de churn muito superior aos de contratos anuais/trienais.

![image](https://github.com/user-attachments/assets/5b7803f7-df98-4f1e-a8a7-0915163b0a03)

### Churn por Tempo como Cliente (Tenure)

Clientes novos (menos de 12 meses) têm alta taxa de churn.

![image](https://github.com/user-attachments/assets/fa40fedf-41bd-4e72-8083-52ae90818b89)

### Churn por Cobrança Mensal

Valores acima de R$ 80 estão altamente associados ao cancelamento.

![image](https://github.com/user-attachments/assets/3c1be403-946b-4b93-9dae-c00ad2cbed8c)

<hr>

## Modelagem Preditiva

Foram testados três modelos preditivos:

| Modelo | Acurácia | Recall | F1-Score | ROC-AUC |
|--------|----------|--------|----------|---------|
| Regressão Logística | 0.80 | 0.56 | 0.66 | 0.84 |
| Random Forest | 0.81 | 0.61 | 0.69 | 0.86 |
| XGBoost | 0.82 | 0.63 | 0.71 | 0.87 |

### Features Mais Importantes

As variáveis mais impactantes na decisão de churn foram (na ordem):

- Tempo como cliente (tenure)
- Valor mensal pago (MonthlyCharges)
- Tipo de contrato
- Presença de serviço de segurança online
- Método de pagamento (boleto bancário aumenta churn)

<hr>

## Insights e Recomendações

#### Drivers de Churn

- Clientes com contrato mês a mês
- Baixo tempo como cliente
- Não possuem serviços extras (backup, segurança)
- Pagam via boleto bancário

#### Recomendações Que eu Sugiro

- Oferecer descontos para contratos anuais
- Programas de fidelidade para clientes novos
- Incentivar adesão a serviços extras
- Melhorar experiência de pagamento
