# DIÁRIO DO PROJETO

## Projeto

**Título:** Predição de atrasos de voos utilizando aprendizado de máquina

**Repositório:** `ml-airline-delay-prediction`

**Problema:** Investigar se é possível prever, utilizando aprendizado de máquina, se um voo sofrerá atraso a partir de características disponíveis antes de sua realização.

---

## 14/09/2026 — Definição do problema, criação do repositório e exploração inicial

Nesta etapa foi definido o problema do projeto: utilizar aprendizado de máquina para prever a ocorrência de atrasos em voos. A escolha foi motivada pela existência de um problema real relacionado ao transporte aéreo e pela possibilidade de utilizar dados históricos para identificar padrões associados à ocorrência de atrasos.

Foi definido que o problema seria tratado como uma tarefa de classificação binária, tendo como variável-alvo `Delay`, em que `0` representa um voo sem atraso e `1` representa um voo atrasado.

Também foi criado o repositório público do projeto no GitHub, com a estrutura inicial contendo os diretórios `dados`, `notebooks`, `src` e `resultados`, além dos arquivos `README.md`, `DIARIO.md`, `requirements.txt` e `.gitignore`.

O dataset escolhido foi o **Airlines Dataset to predict a delay**, disponibilizado no Kaggle. O conjunto possui originalmente 539.383 registros e 9 variáveis: `id`, `Airline`, `Flight`, `AirportFrom`, `AirportTo`, `DayOfWeek`, `Time`, `Length` e `Delay`.

Foi realizada uma análise inicial da estrutura dos dados. Não foram encontrados valores ausentes nem linhas duplicadas. A variável `Delay` apresentou 299.119 registros da classe `0` e 240.264 da classe `1`, correspondendo aproximadamente a 55,46% de voos sem atraso e 44,54% de voos atrasados.

Durante a análise foi observado que a coluna `id` possui valores únicos e sequenciais, funcionando apenas como identificador dos registros. Por esse motivo, foi decidido não utilizá-la como variável preditora, evitando introduzir uma informação sem significado para a previsão.

Também foram analisadas as variáveis categóricas, os aeroportos de origem e destino, o dia da semana, o horário e a duração dos voos. A variável `Time` foi interpretada como o horário de partida representado em minutos após a meia-noite.

---

## 14/09/2026 — Limpeza dos dados e preparação inicial

Durante a preparação dos dados foram investigados valores inconsistentes na variável `Length`, que representa a duração do voo.

Foram encontrados quatro registros com duração igual a zero. Como uma duração de voo igual a zero é inconsistente com o significado da variável e poderia prejudicar a modelagem, esses quatro registros foram removidos.

Após essa limpeza, o conjunto passou de 539.383 para 539.379 registros.

Também foram realizadas análises exploratórias da taxa de atraso por companhia aérea, aeroporto de origem, aeroporto de destino, dia da semana, horário e duração do voo. Essas análises mostraram que a ocorrência de atrasos varia entre diferentes companhias, aeroportos e períodos do dia.

Inicialmente, a variável `Flight` não foi utilizada diretamente no primeiro modelo. A decisão permitiu estabelecer uma primeira referência de desempenho antes de testar se a identificação do voo poderia fornecer informação adicional.

Foi realizada a separação dos dados em treinamento e teste utilizando 80% dos dados para treinamento e 20% para teste. Foi utilizado `random_state=42` e estratificação pela variável-alvo, buscando manter proporções semelhantes das classes nos dois conjuntos.

---

## 14/09/2026 — Criação do baseline

Antes da utilização de modelos mais complexos foi criado um baseline utilizando `DummyClassifier` com a estratégia `most_frequent`.

O objetivo foi estabelecer uma referência simples para verificar se os modelos de aprendizado de máquina realmente conseguiam superar uma estratégia que simplesmente escolhe a classe mais frequente.

O baseline apresentou:

- Accuracy: 0,5546
- Recall da classe de atraso: 0,0000
- F1-score da classe de atraso: 0,0000

O modelo sempre classificou os registros como pertencentes à classe `0`, pois essa era a classe majoritária.

Foi decidido manter esse baseline como referência para as comparações posteriores.

---

## 14/09/2026 — Primeiro modelo: Regressão Logística

Foi implementado o primeiro modelo de aprendizado de máquina utilizando Regressão Logística.

Para as variáveis categóricas `Airline`, `AirportFrom` e `AirportTo` foi utilizada codificação One-Hot. As variáveis numéricas `DayOfWeek`, `Time` e `Length` foram padronizadas utilizando `StandardScaler`.

O modelo apresentou os seguintes resultados no conjunto de teste:

- Accuracy: 0,6464
- Precision da classe 1: 0,6348
- Recall da classe 1: 0,4853
- F1-score da classe 1: 0,5501
- ROC-AUC: 0,6923

A Regressão Logística superou o baseline em Accuracy, passando de 55,46% para 64,64%.

Apesar da melhora, o Recall da classe de atraso ainda foi inferior a 50%, indicando que uma parcela significativa dos voos atrasados não estava sendo identificada.

Esse resultado serviu como primeira referência para os próximos experimentos.

---

## 14/09/2026 — Segundo modelo: Árvore de Decisão

Foi testado um modelo de Árvore de Decisão.

Foram utilizados os parâmetros `max_depth=12`, `min_samples_leaf=50` e `random_state=42`.

O modelo apresentou:

- Accuracy: 0,6510
- Precision da classe 1: 0,6783
- Recall da classe 1: 0,4117
- F1-score da classe 1: 0,5124
- ROC-AUC: 0,6906

A Árvore de Decisão apresentou Accuracy ligeiramente superior à Regressão Logística inicial. Entretanto, o Recall da classe de atraso foi menor, com 41,17%.

Como o objetivo do projeto envolve identificar voos que podem sofrer atraso, apenas observar a Accuracy não seria suficiente para definir o melhor modelo. Por esse motivo, o Recall e o F1-score da classe `1` passaram a ter maior importância na análise.

---

## 14/09/2026 — Terceiro modelo: Random Forest

Foi testado um modelo Random Forest com 100 árvores, `max_depth=12`, `min_samples_leaf=50`, `random_state=42` e processamento paralelo com `n_jobs=-1`.

Os resultados foram:

- Accuracy: 0,6344
- Precision da classe 1: 0,7367
- Recall da classe 1: 0,2788
- F1-score da classe 1: 0,4045
- ROC-AUC: 0,6867

O Random Forest apresentou a maior Precision entre os modelos iniciais para a classe de atraso, mas apresentou Recall muito inferior aos demais.

Isso significa que, embora as previsões classificadas como atraso tivessem uma proporção relativamente alta de acertos, o modelo deixou de identificar grande parte dos voos que realmente sofreram atraso.

Esse experimento foi importante para mostrar que um modelo não deve ser escolhido apenas pela Accuracy ou Precision. Neste problema, um modelo que identifica poucos atrasos corretamente pode não atender ao objetivo definido.

O Random Forest foi mantido como um dos exemplos de abordagem que não apresentou o comportamento desejado para o objetivo do projeto.

---

## 14/09/2026 — Comparação inicial dos modelos

Após os três primeiros experimentos foi realizada uma comparação entre o baseline, a Regressão Logística, a Árvore de Decisão e o Random Forest.

Os resultados foram:

| Modelo | Accuracy | Precision classe 1 | Recall classe 1 | F1 classe 1 | ROC-AUC |
|---|---:|---:|---:|---:|---:|
| Baseline | 0,5546 | 0,0000 | 0,0000 | 0,0000 | - |
| Logistic Regression | 0,6464 | 0,6348 | 0,4853 | 0,5501 | 0,6923 |
| Decision Tree | 0,6510 | 0,6783 | 0,4117 | 0,5124 | 0,6906 |
| Random Forest | 0,6344 | 0,7367 | 0,2788 | 0,4045 | 0,6867 |

A comparação mostrou que os modelos conseguiram superar o baseline em Accuracy. Entretanto, também ficou evidente que existe um compromisso entre Precision e Recall.

A partir dessa análise, decidiu-se investigar se algumas das variáveis inicialmente não utilizadas e diferentes formas de representação dos dados poderiam melhorar a capacidade de identificação dos atrasos.

---

## 14/09/2026 — Investigação da variável Flight

Foi realizada uma investigação específica sobre a variável `Flight`.

O dataset possui 6.585 identificadores diferentes de voo. Foi analisada a taxa média de atraso associada a cada voo para verificar se essa informação poderia contribuir para a previsão.

Após essa análise, a variável `Flight` passou a ser tratada como categórica e incorporada à Regressão Logística por meio de One-Hot Encoding.

O resultado foi:

- Accuracy: 0,6517
- Precision da classe 1: 0,6340
- Recall da classe 1: 0,5161
- F1-score da classe 1: 0,5690
- ROC-AUC: 0,6985

Comparando com a Regressão Logística inicial, houve melhora no Recall, no F1-score e no ROC-AUC.

Esse experimento indicou que a identificação do voo carregava informação útil para o problema, apesar de aumentar a dimensionalidade do conjunto após a codificação categórica.

---

## 14/09/2026 — Transformação cíclica da variável Time

Foi investigada uma nova forma de representar a variável `Time`.

Como `Time` representa minutos após a meia-noite, tratá-la apenas como uma variável numérica pode não representar adequadamente a natureza cíclica do horário. Por exemplo, horários próximos à meia-noite estão numericamente distantes, apesar de serem temporalmente próximos.

Foi criada uma representação cíclica utilizando as funções seno e cosseno:

- `Time_sin`
- `Time_cos`

A Regressão Logística foi então treinada utilizando `Airline`, `Flight`, `AirportFrom` e `AirportTo` como variáveis categóricas e `DayOfWeek`, `Length`, `Time_sin` e `Time_cos` como variáveis numéricas.

Os resultados foram:

- Accuracy: 0,6544
- Precision da classe 1: 0,6376
- Recall da classe 1: 0,5193
- F1-score da classe 1: 0,5724
- ROC-AUC: 0,7020

A representação cíclica do horário apresentou uma melhora em relação ao modelo anterior, principalmente no ROC-AUC, que passou de 0,6985 para 0,7020.

Essa configuração foi adotada como base para a etapa seguinte.

---

## 14/09/2026 — Investigação do threshold de classificação

Após os experimentos anteriores foi analisada a influência do limiar de decisão da Regressão Logística.

Inicialmente, foram observados diferentes thresholds para entender a relação entre Precision e Recall. Foi possível verificar que reduzir o threshold aumentava significativamente o Recall da classe de atraso, enquanto reduzia a Precision e a Accuracy.

Durante essa análise foi percebido que utilizar diretamente o conjunto de teste para escolher o threshold não seria adequado para uma avaliação final imparcial. Por esse motivo, a estratégia foi corrigida.

Foi criada uma divisão de validação a partir do conjunto de treinamento. O threshold passou a ser escolhido utilizando exclusivamente os dados de validação, mantendo o conjunto de teste reservado para a avaliação final.

Foram avaliados os thresholds entre 0,30 e 0,60.

O threshold `0,30` apresentou o maior F1-score na validação:

- Accuracy: 0,5738
- Precision: 0,5129
- Recall: 0,8616
- F1-score: 0,6430

Dessa forma, o threshold `0,30` foi escolhido antes da avaliação final no conjunto de teste.

Essa mudança foi importante para evitar que o conjunto de teste influenciasse a escolha do modelo final.

---

## 14/09/2026 — Avaliação final do modelo

O modelo final foi definido como uma Regressão Logística utilizando:

- `Airline`
- `Flight`
- `AirportFrom`
- `AirportTo`
- `DayOfWeek`
- `Length`
- `Time_sin`
- `Time_cos`

As variáveis categóricas foram transformadas utilizando One-Hot Encoding com tratamento para categorias desconhecidas. As variáveis numéricas foram padronizadas.

O threshold definido utilizando o conjunto de validação foi `0,30`.

O conjunto de teste foi utilizado somente nessa etapa final.

Os resultados finais foram:

- Accuracy: 0,5745
- Precision da classe 1: 0,5133
- Recall da classe 1: 0,8651
- F1-score da classe 1: 0,6443
- ROC-AUC: 0,7020

A matriz de confusão apresentou:

- Verdadeiros negativos: 20.398
- Falsos positivos: 39.425
- Falsos negativos: 6.480
- Verdadeiros positivos: 41.573

O principal objetivo da escolha do threshold foi aumentar a capacidade de identificar voos atrasados. O Recall de 86,51% indica que o modelo identificou a maior parte dos voos que realmente apresentaram atraso.

Por outro lado, o número elevado de falsos positivos mostra que essa estratégia também classifica muitos voos sem atraso como atrasados.

---

## 14/09/2026 — Análise final e limitações

Após os experimentos foi realizada uma comparação consolidada dos modelos.

| Modelo | Accuracy | Precision classe 1 | Recall classe 1 | F1 classe 1 | ROC-AUC |
|---|---:|---:|---:|---:|---:|
| Baseline | 0,5546 | 0,0000 | 0,0000 | 0,0000 | - |
| Logistic Regression | 0,6464 | 0,6348 | 0,4853 | 0,5501 | 0,6923 |
| Decision Tree | 0,6510 | 0,6783 | 0,4117 | 0,5124 | 0,6906 |
| Random Forest | 0,6344 | 0,7367 | 0,2788 | 0,4045 | 0,6867 |
| Logistic + Flight | 0,6517 | 0,6340 | 0,5161 | 0,5690 | 0,6985 |
| Logistic + Flight + Time cíclico | 0,6544 | 0,6376 | 0,5193 | 0,5724 | 0,7020 |
| Modelo final + threshold 0,30 | 0,5745 | 0,5133 | 0,8651 | 0,6443 | 0,7020 |

A análise mostrou que o melhor desempenho depende da métrica utilizada.

O modelo final não possui a maior Accuracy, mas foi escolhido devido à necessidade de identificar uma parcela maior dos voos atrasados. O threshold de 0,30 aumentou o Recall da classe de atraso de 51,93% para 86,51% e também elevou o F1-score da classe de atraso de 57,24% para 64,43%.

Como limitação, o modelo apresenta uma quantidade elevada de falsos positivos. Portanto, a previsão de atraso não deve ser interpretada como uma certeza de que o voo irá atrasar. O modelo funciona como uma ferramenta de identificação de risco e apresenta um compromisso entre detectar atrasos e gerar alertas incorretos.

Outra limitação é que o conjunto de dados utilizado não contém necessariamente todas as informações operacionais que podem influenciar um atraso real, como condições meteorológicas em tempo real, congestionamento aeroportuário, indisponibilidade de aeronaves ou eventos operacionais específicos.

---

## 14/09/2026 — Organização do notebook e documentação

Após a conclusão dos experimentos, o notebook principal foi reorganizado para apresentar o processo de forma sequencial e reproduzível.

O notebook passou a contemplar:

1. Definição do problema;
2. Carregamento dos dados;
3. Análise exploratória;
4. Distribuição da variável-alvo;
5. Análise das principais variáveis;
6. Limpeza dos dados;
7. Transformação cíclica do horário;
8. Separação entre treinamento e teste;
9. Construção do baseline;
10. Pré-processamento;
11. Regressão Logística;
12. Árvore de Decisão;
13. Random Forest;
14. Comparação inicial;
15. Experimento com `Flight`;
16. Experimento com representação cíclica do horário;
17. Seleção do threshold utilizando validação;
18. Avaliação final;
19. Matriz de confusão;
20. Comparação consolidada;
21. Análise do que não funcionou;
22. Limitações;
23. Conclusão;
24. Salvamento dos resultados.

Também foram preparados os arquivos de resultados:

- `resultados/comparacao_modelos.csv`
- `resultados/matriz_confusao_modelo_final.png`
- `resultados/resultado_final.csv`
- `resultados/threshold_validation.csv`
- `resultados/threshold_validation.png`

O `README.md` foi atualizado para documentar o problema, fonte dos dados, processamento, modelos, resultados, limitações e instruções de execução.

O `requirements.txt` também foi atualizado para registrar as principais dependências utilizadas no projeto, incluindo versões específicas para `scikit-learn` e `kagglehub`.

---

## 14/09/2026 — Atualização do repositório GitHub

Nesta etapa foram adicionadas ao repositório as alterações realizadas na documentação, nas dependências e no notebook principal.

Foi criado o commit:

`add exploratory analysis and modeling notebook`

O commit adicionou o notebook completo de análise e modelagem e atualizou o `README.md` e o `requirements.txt`.

As alterações foram enviadas para a branch `main` do repositório público no GitHub.

O histórico do projeto passou a registrar pelo menos as seguintes etapas:

1. `initialize project structure and documentation`
2. `add exploratory analysis and modeling notebook`

A intenção é manter o histórico do Git associado às etapas reais de desenvolvimento, evitando concentrar todas as alterações em um único commit no final do projeto.

---

# Resumo dos principais resultados

O projeto demonstrou que é possível obter desempenho superior ao baseline utilizando modelos de aprendizado de máquina para prever atrasos de voos.

A melhor configuração para o objetivo definido foi uma Regressão Logística com variáveis categóricas relacionadas à companhia, voo e aeroportos, representação cíclica do horário e threshold de classificação igual a 0,30.

O resultado final no conjunto de teste foi:

- **Accuracy:** 57,45%
- **Precision da classe de atraso:** 51,33%
- **Recall da classe de atraso:** 86,51%
- **F1-score da classe de atraso:** 64,43%
- **ROC-AUC:** 70,20%

O principal ganho da estratégia final foi aumentar a capacidade de identificação dos voos atrasados. Em contrapartida, houve aumento expressivo dos falsos positivos.

O projeto reforçou que a escolha de um modelo de classificação não deve considerar somente a Accuracy. A métrica mais adequada depende do objetivo do problema e das consequências associadas aos diferentes tipos de erro.

---

