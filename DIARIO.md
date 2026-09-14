# Diário de decisões

Este arquivo registra o processo de desenvolvimento do projeto, incluindo decisões, experimentos, resultados, problemas encontrados e próximos passos.

---

## 14/09/2026 — Definição do projeto e preparação do repositório

### O que foi feito

Foi definido o tema do projeto: **predição de atrasos de voos utilizando aprendizado de máquina**.

O problema foi escolhido por representar uma situação real relacionada ao transporte aéreo e por permitir a utilização de dados reais para investigar uma tarefa de classificação.

A pergunta inicial definida para o projeto foi:

> É possível prever se um voo sofrerá atraso utilizando características disponíveis sobre o voo antes de sua realização?

Foi escolhido o dataset **Airlines Dataset to predict a delay**, disponibilizado na plataforma Kaggle.

O repositório público `ml-airline-delay-prediction` foi criado no GitHub e clonado localmente antes do início do desenvolvimento, conforme solicitado na atividade.

Também foi criada a estrutura inicial do projeto, contendo diretórios para dados, notebooks, código-fonte e resultados, além dos arquivos de documentação e configuração.

### Decisões

* Problema: previsão de atrasos de voos.
* Tipo de problema: classificação supervisionada.
* Dataset inicial: Airlines Dataset to predict a delay.
* Fonte do dataset: Kaggle.
* Ambiente de desenvolvimento: Google Colab.
* Linguagem: Python.
* Repositório: GitHub público.
* Os dados ainda não foram modificados.
* Nenhum modelo foi treinado nesta etapa.
* Nenhuma métrica foi calculada nesta etapa.
* A definição final das variáveis utilizadas será feita após a inspeção do dataset.
* A métrica principal será definida após a análise da variável-alvo e da distribuição das classes.

### Questões em aberto

* Quantos registros existem no arquivo utilizado?
* Quantas variáveis estão disponíveis?
* Existem valores ausentes?
* Existem registros duplicados?
* Como a variável de atraso está distribuída?
* Quais variáveis podem ser utilizadas antes da realização do voo?
* Existem variáveis que podem causar vazamento de dados?
* Qual será a linha de base adequada?
* Quais modelos serão comparados?

### Próximo passo

Baixar o dataset original e realizar uma inspeção exploratória inicial dos dados antes de qualquer transformação ou treinamento de modelos.
