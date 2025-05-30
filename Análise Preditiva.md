# Análise Preditiva de Rotatividade de Clientes  

#### TelecomNext S.A. 

<hr>

## Introdução

O objetivo deste projeto é identificar padrões de cancelamento de clientes na **TelecomNext S.A.**, uma operadora de telecomunicações. Usamos técnicas de análise de dados, machine learning e visualização interativa para prever quais clientes estão em risco de churn e auxiliando no suporte à decisão.

<hr>

## Dataset Utilizado

- **Fonte:** `TlecomNextS.A.csv`  
- **Total de registros:** 7.043 clientes  
- **Variáveis:** 20 colunas relacionadas a perfil demográfico, serviços contratados, forma de pagamento e status de churn

#### Estrutura das Variáveis

| Coluna | Tipo | Descrição |
|--------|------|-----------|
| `customerID` | Texto | Identificador único do cliente |
| `gender` | Categórica | Gênero do cliente (`Male`, `Female`) |
| `SeniorCitizen` | Binária | Indicador se o cliente é idoso (`0`, `1`) |
| `Partner` | Binária | Cliente tem parceiro(a)? (`Yes`, `No`) |
| `Dependents` | Binária | Cliente tem dependentes? (`Yes`, `No`) |
| `tenure` | Numérica | Tempo como cliente em meses |
| `PhoneService` | Binária | Tem serviço telefônico? (`Yes`, `No`) |
| `MultipleLines` | Categórica | Tem múltiplas linhas? (`Yes`, `No`, `No phone service`) |
| `InternetService` | Categórica | Tipo de serviço de internet (`DSL`, `Fiber optic`, `No`) |
| `OnlineSecurity` | Categórica | Tem segurança online? (`Yes`, `No`, `No internet service`) |
| `OnlineBackup` | Categórica | Tem backup online? (`Yes`, `No`, `No internet service`) |
| `DeviceProtection` | Categórica | Tem proteção de dispositivo? (`Yes`, `No`, `No internet service`) |
| `TechSupport` | Categórica | Tem suporte técnico? (`Yes`, `No`, `No internet service`) |
| `StreamingTV` | Categórica | Usa TV via streaming? (`Yes`, `No`, `No internet service`) |
| `StreamingMovies` | Categórica | Assiste filmes via streaming? (`Yes`, `No`, `No internet service`) |
| `Contract` | Categórica | Tipo de contrato (`Month-to-month`, `One year`, `Two year`) |
| `PaperlessBilling` | Binária | Recebe fatura digitalmente? (`Yes`, `No`) |
| `PaymentMethod` | Categórica | Método de pagamento (`Electronic check`, `Mailed check`, `Bank transfer (automatic)`, `Credit card (automatic)`) |
| `MonthlyCharges` | Numérica | Valor mensal pago pelo cliente |
| `TotalCharges` | Numérica | Total acumulado pago até o momento |
| `Churn` | Binária | Cliente cancelou o serviço? (`Yes`, `No`) |

<hr>

## Limpeza e Transformação de Dados

####  Tratamento de Valores Ausentes
A coluna `TotalCharges` tinha valores ausentes que foram substituídos por `0` (clientes novos).

```python
df['TotalCharges'] = pd.to_numeric(df['TotalCharges'], errors='coerce')
df['TotalCharges'].fillna(0, inplace=True)
```

#### Padronização de Categorias
Substituir valores inconsistentes:

```python
cols_to_replace = ['PhoneService', 'MultipleLines', 'OnlineSecurity', 'OnlineBackup',
                   'DeviceProtection', 'TechSupport', 'StreamingTV', 'StreamingMovies']
for col in cols_to_replace:
    df[col] = df[col].replace({'No phone service': 'No', 'No internet service': 'No'})
```

#### Conversão de Tipos

```python
df['SeniorCitizen'] = df['SeniorCitizen'].map({0: 'No', 1: 'Yes'})
df['MonthlyCharges'] = pd.to_numeric(df['MonthlyCharges'])
```

#### One-Hot Encoding

```python
df_encoded = pd.get_dummies(df.drop(['customerID', 'Churn'], axis=1), drop_first=True)
```

<hr>

## Análise Exploratória de Dados 

📊 Taxa média de churn: 26%

- Clientes com contrato mês a mês têm taxa de churn muito maior.
- Tempo como cliente (tenure ) é o fator mais importante na retenção.
- Cobrança mensal acima de R$ 80 está associada a maior rotatividade.
- Ausência de serviços extras aumenta risco de cancelamento.

<hr>

## Modelagem Preditiva

#### Foram testados três modelos:

<table>
  <thead>
    <tr>
      <th>Modelo</th>
      <th>Acurácia</th>
      <th>Recall</th>
      <th>F1-SCcore</th>
      <th>ROC-AUC</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Regressão Logística</td>
      <td>80%</td>
      <td>56%</td>
      <td>63%</td>
      <td>0.84</td>
    </tr>
    <tr>
      <td>Random Forest</td>
      <td>81%</td>
      <td>61%</td>
      <td>67%</td>
      <td>0.86</td>
    </tr>
     <tr>
      <td>XGBoost</td>
      <td>82%</td>
      <td>63%</td>
      <td>69%</td>
      <td>0.87</td>
    </tr>
  </tbody>
</table>



#### 🔍 Importância das Features

As variáveis mais impactantes no modelo final (XGBoost):

<table>
  <thead>
    <tr>
      <th>Feature</th>
      <th>Impacto</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Tenure</td>
      <td>Tempo como cliente – quanto maior, menor o risco</td>
    </tr>
    <tr>
      <td>MonthlyCharges</td>
      <td>Cobrança mensal – quanto maior, maior o risco</td>
    </tr>
     <tr>
      <td>Contract_Month-to-month</td>
      <td>Contrato mês a mês – principal driver de churn</td>
    </tr>
    <tr>
      <td>OnlineSecurity_No</td>
      <td>Falta de segurança online aumenta risco</td>
    </tr>
    <tr>
      <td>DeviceProtection_No</td>
      <td>Ausência de proteção de dispositivo aumenta risco</td>
    </tr>
    <tr>
      <td>PaymentMethod_Electronic check</td>
      <td>Pagamento por boleto aumenta risco</td>
    </tr>
  </tbody>
</table>

<hr>

## Previsão Futura de Churn

#### Simulamos a taxa de churn nos próximos 6 meses com base nas probabilidades previstas:

<table>
  <thead>
    <tr>
      <th>Mês</th>
      <th>Cancelamentos Previstos</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>120 +</td>
    </tr>
    <tr>
      <td>2</td>
      <td>140 +</td>
    </tr>
     <tr>
      <td>3</td>
      <td>160 +</td>
    </tr>
    <tr>
      <td>4</td>
      <td>170 +</td>
    </tr>
    <tr>
      <td>5</td>
      <td>180 +</td>
    </tr>
    <tr>
      <td>6</td>
      <td>190 +</td>
    </tr>
  </tbody>
</table>

- Curva crescente indica aumento gradual do risco ao longo do tempo.

## Recomendações Que eu Sugiro

#### Drivers Mais Relevantes de Churn
- Contratos mês a mês
- Tempo como cliente baixo (tenure maior que 12 meses)
- Falta de serviços extras (segurança, backup, proteção de dispositivos)
- Cobrança mensal alta (menor que R$ 80)
- Método de pagamento “boleto bancário” (Electronic check)

#### Recomendações

<table>
  <thead>
    <tr>
      <th>Estratégia</th>
      <th>Descrição</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Ofertas de contratos anuais</td>
      <td>Promover segurança online, backup e proteção de dispositivo</td>
    </tr>
    <tr>
      <td>Programas de Fidelidade</td>
      <td>Recompensar clientes com mais de 12 meses</td>
    </tr>
     <tr>
      <td>Campanhas de upsell</td>
      <td>Promover segurança online, backup e proteção de dispositivo</td>
    </tr>
    <tr>
      <td>Monitoramento contínuo</td>
      <td>Sistema de detecção automática de clientes em risco</td>
    </tr>
    <tr>
      <td>Personalização de ofertas</td>
      <td>Baseado na probabilidade individual de churn</td>
    </tr>
  </tbody>
</table>

<hr>

## Conclusão

A TelecomNext S.A. agora possui uma ferramenta robusta e prática para:

- Antecipar cancelamentos
- Priorizar ações de CRM
- Melhorar a experiência do cliente
- Reduzir perda de receita
- Este projeto é ideal tanto para apresentação corporativa quanto para portfólio de análise de dados.




