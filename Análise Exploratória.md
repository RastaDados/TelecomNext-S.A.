### Limpeza e Pré-Processamento dos Dados

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import warnings
warnings.filterwarnings('ignore')

#Carregando os dados
df = pd.read_csv('dados/WA_Fn-UseC_-Telco-Customer-Churn.csv')

#Verificando as primeiras linhas
print(df.head())

#Verificando os valores ausentes
print(df.isnull().sum())

#Substituição de 'No phone service' e 'No internet service' por 'No'
cols_to_replace = ['PhoneService', 'MultipleLines', 'OnlineSecurity', 'OnlineBackup',
                   'DeviceProtection', 'TechSupport', 'StreamingTV', 'StreamingMovies']
for col in cols_to_replace:
    df[col] = df[col].replace({'No phone service': 'No', 'No internet service': 'No'})

#Convertendo SeniorCitizen para categórico
df['SeniorCitizen'] = df['SeniorCitizen'].map({0: 'No', 1: 'Yes'})

#Tratando o TotalCharges (convertndo para numérico)
df['TotalCharges'] = pd.to_numeric(df['TotalCharges'], errors='coerce')
df['TotalCharges'] = df['TotalCharges'].fillna(0)
```

![image](https://github.com/user-attachments/assets/f46cf6c4-72e9-444d-9e05-53f8b779b4e4)

<hr>

### Análise Exploratória dos Dados

#### Estatísticas Descritivas

```python
#Variáveis numéricas
print(df.describe())

#Variáveis categóricas
categorical_cols = df.select_dtypes(include=['object']).columns
for col in categorical_cols:
    print(f"\n{col}:\n", df[col].value_counts())
```

![image](https://github.com/user-attachments/assets/fb86f682-12b7-43a5-b5ce-694f72c11672)

#### Taxa Global de Churn

```python
churn_rate = df['Churn'].value_counts(normalize=True)
print("Taxa de Churn:")
print(churn_rate)

sns.countplot(x='Churn', data=df)
plt.title('Distribuição de Churn')
plt.show()
```

![image](https://github.com/user-attachments/assets/3de80962-1ca3-428f-9e8e-eaaf2f421c19)

#### Distribuição de Churn por Categoria

```python
features = ['gender', 'SeniorCitizen', 'Partner', 'Dependents', 'Contract', 'PaymentMethod']

for feature in features:
    sns.countplot(x=feature, hue='Churn', data=df)
    plt.title(f'Distribuição de Churn por {feature}')
    plt.xticks(rotation=45)
    plt.legend(title='Churn')
    plt.tight_layout()
    plt.show()
```

![image](https://github.com/user-attachments/assets/d9d6c0b9-f5f1-4a89-b76e-14a9dd0a1cf6)

![image](https://github.com/user-attachments/assets/a1f1634c-0e4c-49af-b9ca-c01830979200)

![image](https://github.com/user-attachments/assets/3c6ff7a0-c90e-4444-97c2-777d618f0f80)

![image](https://github.com/user-attachments/assets/6a2d6f88-4f30-490b-a5da-b8b4a463d7e5)

![image](https://github.com/user-attachments/assets/c59482f7-1a8a-4bfa-a96d-ead9bef61237)

![image](https://github.com/user-attachments/assets/d91ecbac-d76f-4d74-839e-7ec58a2b7fbd)


### Análise de Tenure (Tempo como Cliente)

```python
#Histograma
sns.histplot(data=df, x='tenure', bins=30, kde=True)
plt.title('Distribuição de Tempo como Cliente (Tenure)')
plt.show()

#Boxplot por Churn
sns.boxplot(x='Churn', y='tenure', data=df)
plt.title('Tenure por Status de Churn')
plt.show()

#Taxa de churn por faixa de tenure
df['tenure_group'] = pd.cut(df['tenure'], bins=[0, 12, 24, 36, 48, 60, 72], labels=[
    '0-12', '13-24', '25-36', '37-48', '49-60', '61-72'])
churn_by_tenure = df.groupby('tenure_group')['Churn'].value_counts(normalize=True).unstack()
churn_by_tenure.plot(kind='bar', stacked=True)
plt.title('Taxa de Churn por Faixa de Tenure')
plt.xlabel('Faixa de Tempo como Cliente')
plt.ylabel('Proporção')
plt.legend(title='Churn')
plt.show()
```

![image](https://github.com/user-attachments/assets/4794874f-4d5a-46c5-a0e8-00738f4dd0b2)

![image](https://github.com/user-attachments/assets/5f39ee10-f2f2-4c4f-adec-80c4a5601a59)

![image](https://github.com/user-attachments/assets/72afec56-7a51-47fc-848a-99dc1de07953)

### Análise Financeira

```python
#Distribuição de MonthlyCharges
sns.histplot(data=df, x='MonthlyCharges', hue='Churn', bins=30, kde=True)
plt.title('Distribuição de Cobrança Mensal')
plt.show()

#Média por status de churn
print(df.groupby('Churn')[['MonthlyCharges', 'TotalCharges']].mean())

#Scatter plot entre cobrança mensal e tempo como cliente
sns.scatterplot(x='MonthlyCharges', y='tenure', hue='Churn', data=df, alpha=0.6)
plt.title('Relação entre Cobrança Mensal e Tempo como Cliente')
plt.show()
```

![image](https://github.com/user-attachments/assets/964407d3-97f4-4253-957c-513b6d442340)

![image](https://github.com/user-attachments/assets/57ddcd30-39cf-4dec-a91c-584a5b6de673)











