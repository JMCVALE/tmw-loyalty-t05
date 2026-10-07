# Projeto de Fidelidade Téo Me Why
#  Predição de Churn

**Autora:** Júlia Maria de Carvalho Vale

## Objetivo

Aplicar técnicas de Machine Learning para identificar clientes com maior probabilidade de churn nos próximos 28 dias.

Neste projeto, o churn foi definido a partir da não ativação do cliente no período de 28 dias após a data de referência.

O objetivo final é gerar um ranking dos clientes com maior probabilidade de churn, permitindo a priorização de ações de retenção.

## Pipeline do projeto

**Dados de origem → Features → ABT → Treinamento → Avaliação → MLflow → Predição → Ranking de Churn**

### 1. Dados de origem

Os dados utilizados no projeto são provenientes das bases do Téo Me Why (https://github.com/ASN-ROCKS/tmw-loyalty-t05).

### 2. Features

Foi criada a Feature Store `fs_pontos`, responsável por transformar o histórico de transações em variáveis de comportamento dos clientes.

Entre as principais features estão:

- `qtFrequencia`
- `qtPontos`
- `qtPontosPositivos`
- `recencia`
- `QtdeTransacoes`
- `saldoDia`
- `diasPrimeiraTransacao`
- `freqVida`
- `zScore`
- `DiasUltimoStreak`
- `flStreak`
- `qtdeProdutoDistintos`

As features consideram diferentes períodos do histórico do cliente, incluindo uma janela de 28 dias e o histórico completo anterior à data de referência.

### 3. Construção da ABT

Foi criada a tabela:

`workspace.analytics.abt_ativacao`

A ABT reúne as features transacionais e o indicador utilizado como variável target do modelo.

### 4. Definição do target

O target utilizado foi:

`flAtivacao`

A variável indica se o cliente realizou uma ativação nos 28 dias seguintes à data de referência.

- `flAtivacao = 1`: cliente ativou nos próximos 28 dias
- `flAtivacao = 0`: cliente não ativou nos próximos 28 dias

No contexto deste projeto, a classe `0` representa o maior risco de churn.

### 5. Separação temporal

Foi utilizada uma abordagem temporal para separar os dados históricos do período mais recente.

Os dados históricos foram utilizados para treinamento e teste, enquanto o período mais recente foi utilizado como OOT (Out of Time).

Essa abordagem permite avaliar o comportamento do modelo em um período posterior ao utilizado no treinamento.

### 6. Tratamento dos dados

Foram realizados tratamentos de valores ausentes utilizando a biblioteca `feature_engine`.

Foram aplicadas estratégias de imputação de acordo com as características das variáveis.

### 7. Treinamento do modelo

Foi utilizado o algoritmo:

`RandomForestClassifier`

Configuração principal:

```python
RandomForestClassifier(
    min_samples_leaf=10,
    n_estimators=500,
    random_state=42
)
```
### 8. Avaliação
O modelo foi avaliado utilizando os conjuntos Train, Test e OOT, considerando as métricas Accuracy e AUC.

### 9. MLflow
O modelo treinado foi registrado no MLflow para ser utilizado posteriormente na etapa de predição.

### 10. Predição
O modelo foi aplicado à base mais recente, composta por 233 clientes e 51 variáveis.
Foi calculada a probabilidade de churn para cada cliente.

### 11. Top 50
Os clientes foram ordenados pela maior probabilidade de churn e foram selecionados os 50 primeiros.