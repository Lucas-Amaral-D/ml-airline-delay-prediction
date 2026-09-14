# Predição de Atrasos de Voos utilizando Aprendizado de Máquina

Projeto desenvolvido para o seminário de Machine Learning, com o objetivo de investigar se é possível prever se um voo sofrerá atraso utilizando características disponíveis sobre o voo antes de sua realização.

## 1. Problema e por que ele importa

Atrasos de voos afetam passageiros, companhias aéreas e a operação aeroportuária. Antecipar a possibilidade de atraso pode auxiliar na identificação de situações de maior risco e servir como apoio para triagem e planejamento operacional.

Neste projeto, o problema foi formulado como uma **classificação binária**:

- `0` — voo sem atraso;
- `1` — voo atrasado.

A pergunta investigada foi:

> É possível prever se um voo sofrerá atraso utilizando características disponíveis sobre o voo antes de sua realização?

O objetivo não é afirmar que um voo necessariamente atrasará, mas avaliar se os dados disponíveis permitem identificar padrões associados à ocorrência de atrasos.

---

## 2. Fonte dos dados e quantidade

Foi utilizado o dataset público **Airlines Dataset to predict a delay**, disponibilizado no Kaggle:

https://www.kaggle.com/datasets/jimschacko/airlines-dataset-to-predict-a-delay

O arquivo utilizado no projeto é `Airlines.csv`.

O conjunto original possui:

- **539.383 registros**
- **9 variáveis**

As variáveis são:

| Variável | Descrição |
|---|---|
| `id` | Identificador do registro |
| `Airline` | Companhia aérea |
| `Flight` | Código do voo |
| `AirportFrom` | Aeroporto de origem |
| `AirportTo` | Aeroporto de destino |
| `DayOfWeek` | Dia da semana |
| `Time` | Horário codificado em minutos |
| `Length` | Duração do voo |
| `Delay` | Variável-alvo |

A variável `Delay` apresentou aproximadamente:

- **55,46%** de voos sem atraso;
- **44,54%** de voos atrasados.

Após a inspeção dos dados, foram encontrados quatro registros com `Length = 0`. Esses registros foram removidos por representarem uma duração incompatível com um voo. O conjunto utilizado na modelagem passou a ter **539.379 registros**.

Não foram encontrados valores ausentes nem linhas duplicadas no conjunto original.

---

## 3. O que foi feito antes da modelagem

A preparação foi realizada antes dos experimentos de modelagem.

Primeiramente, foi feita uma análise exploratória das variáveis, incluindo distribuição da variável-alvo, quantidade de categorias, taxas de atraso por companhia aérea e dia da semana e comportamento do horário e duração dos voos.

A coluna `id` foi retirada dos modelos por ser apenas um identificador sequencial.

As variáveis categóricas `Airline`, `AirportFrom` e `AirportTo` foram codificadas com `OneHotEncoder`.

As variáveis numéricas foram padronizadas com `StandardScaler`.

A variável `Flight` foi inicialmente excluída dos primeiros experimentos. Depois foi realizado um experimento específico tratando `Flight` como variável categórica. Como existem **6.585 códigos diferentes**, foi utilizado `min_frequency=10` no `OneHotEncoder`, evitando representar individualmente categorias muito pouco frequentes.

Também foi investigada a representação do horário. No experimento inicial, `Time` foi tratado como variável numérica. Posteriormente, ele foi transformado em duas variáveis cíclicas:

- `Time_sin`
- `Time_cos`

Essa transformação representa a natureza circular do horário ao longo de um dia.

Os dados foram separados de forma estratificada em treinamento e teste, utilizando 80% para treinamento e 20% para teste. Na etapa de escolha do threshold, uma parte do conjunto de treinamento foi separada para validação, mantendo o teste reservado para a avaliação final.

---

## 4. Modelos testados

Foram avaliadas as seguintes abordagens:

### Baseline

Foi utilizado `DummyClassifier` com estratégia `most_frequent`, que sempre prevê a classe majoritária.

Resultado:

- Accuracy: **55,46%**

O baseline serve como referência para verificar se os modelos de aprendizado de máquina apresentam ganho em relação a uma previsão trivial.

### Regressão Logística

Primeiro foi testada uma Regressão Logística utilizando as variáveis categóricas e numéricas da configuração inicial.

Resultado:

- Accuracy: **64,64%**
- Precision: **63,48%**
- Recall: **48,53%**
- F1-score: **55,01%**
- ROC-AUC: **0,6923**

### Árvore de Decisão

Foi testada uma árvore com `max_depth=12` e `min_samples_leaf=50`.

Resultado:

- Accuracy: **65,10%**
- Precision: **67,83%**
- Recall: **41,17%**
- F1-score: **51,24%**
- ROC-AUC: **0,6906**

Embora tenha apresentado Accuracy ligeiramente maior que a primeira Regressão Logística, seu Recall para a classe `Atrasado` foi inferior.

### Random Forest

Foi testada uma Random Forest com 100 árvores, `max_depth=12` e `min_samples_leaf=50`.

Resultado:

- Accuracy: **63,44%**
- Precision: **73,67%**
- Recall: **27,88%**
- F1-score: **40,45%**
- ROC-AUC: **0,6867**

A Random Forest apresentou a maior Precision entre os modelos testados, mas seu Recall foi muito baixo para a classe `Atrasado`.

### Regressão Logística com Flight

Foi realizado um novo experimento incluindo `Flight` como variável categórica.

Resultado:

- Accuracy: **65,17%**
- Precision: **63,40%**
- Recall: **51,61%**
- F1-score: **56,90%**
- ROC-AUC: **0,6985**

### Regressão Logística com Flight e Time cíclico

A última configuração antes da escolha do threshold utilizou `Flight` e a representação cíclica de `Time`.

Resultado:

- Accuracy: **65,44%**
- Precision: **63,76%**
- Recall: **51,93%**
- F1-score: **57,24%**
- ROC-AUC: **0,7020**

Essa configuração foi escolhida como base para o modelo final.

---

## 5. Resultado, métrica adequada e comparação com o baseline

A comparação dos experimentos foi:

| Modelo | Accuracy | Precision | Recall | F1-score | ROC-AUC |
|---|---:|---:|---:|---:|---:|
| Baseline | 0,5546 | 0,0000 | 0,0000 | 0,0000 | — |
| Logistic Regression | 0,6464 | 0,6348 | 0,4853 | 0,5501 | 0,6923 |
| Decision Tree | 0,6510 | 0,6783 | 0,4117 | 0,5124 | 0,6906 |
| Random Forest | 0,6344 | 0,7367 | 0,2788 | 0,4045 | 0,6867 |
| Logistic + Flight | 0,6517 | 0,6340 | 0,5161 | 0,5690 | 0,6985 |
| Logistic + Flight + Time cíclico | **0,6544** | 0,6376 | 0,5193 | 0,5724 | **0,7020** |
| Modelo final + threshold 0,30 | 0,5745 | 0,5133 | **0,8651** | **0,6443** | **0,7020** |

A métrica mais importante para a decisão final foi o **Recall da classe `Atrasado`**, complementado pelo F1-score.

Isso ocorre porque, para o objetivo definido, deixar passar um voo que realmente atrasará é relevante. Portanto, foi realizado um experimento específico com diferentes thresholds de classificação.

O threshold padrão de 0,50 foi comparado com valores entre 0,30 e 0,60. A escolha foi feita utilizando o conjunto de validação, e não o conjunto de teste.

O threshold **0,30** apresentou o maior F1-score na validação, com aproximadamente **64,30%**, e Recall de aproximadamente **86,16%**.

Depois dessa escolha, o modelo foi treinado novamente utilizando todo o conjunto de treinamento e avaliado uma única vez no conjunto de teste.

### Resultado final no teste

Com threshold `0,30`:

- **Accuracy:** 57,45%
- **Precision:** 51,33%
- **Recall:** 86,51%
- **F1-score:** 64,43%
- **ROC-AUC:** 0,7020

O principal ganho da mudança de threshold foi o aumento do Recall para a classe `Atrasado`, que passou de 51,93% com threshold 0,50 para 86,51% com threshold 0,30.

Esse ganho ocorre com uma redução de Accuracy e Precision, mostrando explicitamente o trade-off entre identificar mais atrasos e gerar mais falsos positivos.

---

## 6. O que não funcionou

A tentativa com Random Forest não trouxe uma melhoria adequada para o objetivo definido.

Apesar de apresentar Precision de **73,67%**, seu Recall foi de apenas **27,88%**. Isso significa que uma parcela grande dos voos que realmente atrasaram não foi identificada pelo modelo.

Além disso, sua ROC-AUC de **0,6867** ficou abaixo da configuração final da Regressão Logística, que alcançou **0,7020**.

O experimento foi mantido no projeto porque demonstra uma tentativa real que apresentou resultado inferior para o objetivo escolhido.

Outro ponto observado foi que simplesmente aumentar a complexidade do modelo não garantiu melhor desempenho. A escolha final foi baseada no comportamento das métricas e na finalidade da previsão, e não apenas na complexidade do algoritmo.

---

## 7. Uma limitação honesta

A principal limitação da solução final é o número elevado de falsos positivos provocado pela escolha do threshold 0,30.

No conjunto de teste, a matriz de confusão apresentou:

| | Predito: Sem atraso | Predito: Atrasado |
|---|---:|---:|
| **Real: Sem atraso** | 20.398 | 39.425 |
| **Real: Atrasado** | 6.480 | 41.573 |

O modelo identificou **41.573 dos 48.053 voos atrasados**, resultando em Recall de **86,51%**.

Por outro lado, classificou **39.425 voos sem atraso como atrasados**. Por isso, a Precision ficou em **51,33%**.

Consequentemente, o modelo deve ser interpretado como uma ferramenta de **triagem e identificação de possíveis atrasos**, e não como uma confirmação de que determinado voo necessariamente atrasará.

Além disso, o desempenho observado depende das características presentes no dataset. Uma aplicação real poderia exigir outras informações operacionais, temporais e contextuais que não estão disponíveis neste projeto.

---

# Reprodutibilidade

## Executando o projeto

O projeto pode ser executado utilizando Google Colab ou um ambiente Python com as dependências listadas em `requirements.txt`.

### 1. Clonar o repositório

```bash
git clone https://github.com/Lucas-Amaral-D/ml-airline-delay-prediction.git
cd ml-airline-delay-prediction
```

### 2. Instalar as dependências

```bash
pip install -r requirements.txt
```

### 3. Abrir o notebook

O notebook principal está em:

```text
notebooks/01_analise_exploratoria_airline_delay.ipynb
```

No Google Colab, abra o notebook e execute as células na ordem utilizando **Ambiente de execução → Executar tudo**.

O notebook realiza o download do dataset público pelo `kagglehub`, executa a preparação dos dados, treina os modelos, avalia os thresholds e gera os resultados.

### 4. Resultados gerados

A execução gera:

```text
resultados/
├── comparacao_modelos.csv
├── matriz_confusao_modelo_final.png
├── resultado_final.csv
├── threshold_validation.csv
└── threshold_validation.png
```

---

# Estrutura do projeto

```text
ml-airline-delay-prediction/
│
├── dados/
│
├── notebooks/
│   └── 01_analise_exploratoria_airline_delay.ipynb
│
├── resultados/
│   ├── comparacao_modelos.csv
│   ├── matriz_confusao_modelo_final.png
│   ├── resultado_final.csv
│   ├── threshold_validation.csv
│   └── threshold_validation.png
│
├── src/
│
├── .gitignore
├── DIARIO.md
├── README.md
└── requirements.txt
```

## Tecnologias utilizadas

- Python
- Google Colab
- Pandas
- NumPy
- Matplotlib
- Scikit-learn
- Kaggle / KaggleHub
- Jupyter Notebook

## Autoria

Projeto acadêmico desenvolvido para o seminário de Machine Learning.
