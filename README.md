# Classificação de Qualidade de Vinhos (Indústria Alimentar)
## Identificação da Equipa
| Nome | Número de Aluno | Nº Grupo |
| :--- | :---: | :--- |
| Afonso Carvalho | 2023132594 | 4 |
| Marcello Portugal | 2022136899 | 4 |
## Organização do Repositório
A estrutura deste projeto segue as boas práticas de Ciência de Dados e Engenharia de Software:
* **`data/`**: Armazenamento de dados (dados brutos em `raw/` e processados em `processed/`).
* **`docs/`**: Documentação técnica detalhada dividida por Milestones (M1, M2 e M3).
* **`notebooks/`**: Jupyter Notebooks para experimentação, limpeza e modelação.
* **`src/`**: Código-fonte modular (scripts `.py`) para funções reutilizáveis.
* **`reports/`**: Relatórios finais, apresentações e exportação de figuras (`figures/`).
* **`requirements.txt`**: Ficheiro de configuração com as bibliotecas necessárias.
## 1. Iniciação (Milestone 1)
### Contexto e Problema de Negócio
A avaliação da qualidade do vinho é tradicionalmente um processo sensorial, subjetivo e demorado. O problema de negócio consiste em otimizar este processo, utilizando dados de testes laboratoriais (propriedades físico-químicas) para prever de forma rápida e objetiva a qualidade final do vinho, auxiliando os produtores na tomada de decisão e no controlo de qualidade.
### Objetivos SMART
Desenvolver um modelo de Aprendizagem Automática (baseado em algoritmos de regressão) para prever a qualidade sensorial do Vinho Verde (escala 0 a 10) utilizando variáveis físico-químicas, com a meta de atingir um Erro Médio Absoluto (MAE) inferior a 0.5 pontos até ao final da Milestone 3.

### Perguntas de Investigação: 
*PI 1: Qual é a capacidade de previsão de um modelo baseado apenas em dados físico-químicos?*

*PI 2: Quais são as 3 variáveis físico-químicas que mais contribuem para a previsão de um vinho de "Alta Qualidade" (nota ≥ 7)?*

*PI 3: Existem diferenças estatisticamente significativas nas variáveis que determinam a qualidade quando comparamos modelos treinados exclusivamente para vinhos tintos versus vinhos brancos?*
  
### Fonte de Dados
* **Dataset:** [UCI Wine Quality](https://archive.ics.uci.edu/dataset/186/wine+quality)
* **Dataset Original:** 6.497 registos (1.599 tintos / 4.898 brancos) | 12 atributos.
* **Dataset Processado:** 6.497 registos | 14 atributos.
* **Variável Alvo:** `quality` (Pontuação de 0 a 10, onde valores mais altos indicam melhor qualidade sensorial).
  
### Ferramentas e Bibliotecas Python
* **Pandas & NumPy:** Para manipulação e tratamento estatístico dos dados.
* **Seaborn & Matplotlib:** Para a criação de gráficos e análise visual.
* **Scikit-learn:** Para a construção e avaliação dos modelos de Aprendizagem Automática.

## 2. Exploração (Milestone 2)

### Limpeza e Preparação
A Fase 2 arrancou com o *dataset* unificado (`data/processed/wine_quality_unificado.csv`), que já inclui nativamente a variável `is_red` e `type` geradas na Fase 1. O foco desta etapa foi garantir a máxima qualidade dos dados para a modelação:

* **Tratamento de Anomalias:** O *dataset* apresentou 0% de nulos. Aplicámos limitação (*capping*) aos *outliers* químicos para evitar enviesamentos, sem perda de informação.
* **Engenharia de Atributos (*Feature Engineering*):** Normalização dos dados (`StandardScaler`) e criação de novos rácios enológicos (ex: rácio de acidez volátil) para captar desequilíbrios estruturais.

> *Nota: Os detalhes técnicos e o dicionário de dados pós-processamento estão disponíveis no relatório completo: `docs/M2_exploracao.md`*

### Principais Conclusões da Análise Exploratória (*EDA*)

* **Os Motores da Qualidade:** O teor alcoólico é o principal impulsionador de notas altas, enquanto a acidez volátil atua como a principal penalizadora.
* **A Irrelevância da Cor:** A qualidade é ditada pela química do vinho; a categoria (tinto/branco) tem um impacto estatístico quase nulo na nota final.
* **O Desafio da Modelação:** Os dados concentram-se fortemente nas notas médias (5, 6 e 7), havendo uma escassez de exemplos extremos (notas muito altas ou baixas) para o modelo aprender.
> *Nota: Os principais gráficos e visualizações gerados durante a Análise Exploratória de Dados encontram-se disponíveis na pasta `reports/figures/`.*
> 
### Estado dos Dados:
Após as análises e tratamentos descritos, os dados encontram-se totalmente limpos, normalizados e enriquecidos com atributos estratégicos. O *dataset* final (`wine_quality_model_ready.csv`) está, assim, 100% pronto para alimentar a fase de modelação.
## 3. Modelação (Milestone 3)
### Abordagem Técnica
* **Modelos:** [Ex: Random Forest e XGBoost]
* **Métrica Principal:** [Ex: F1-Score ou RMSE]
## 4. Finalização (Milestone 4)
### Resposta ao Problema
[Resumo da solução e como ela gera valor para o negócio.]
### Recomendações de Inovação
1. [Sugestão prática baseada nos resultados]

## 5. Refêrencias
1. P. Cortez, A. Cerdeira, F. Almeida, T. Matos and J. Reis. Modeling wine preferences by data mining from physicochemical properties. In Decision Support Systems, Elsevier, 47(4):547-553. ISSN: 0167-9236.


## Como Reproduzir este Projeto
1. Clone o repositório: `git clone [url-do-repo]`
2. Instale as dependências: `pip install -r requirements.txt`
3. Execute os notebooks na pasta `notebooks/` seguindo a ordem numérica.

   **Instituição:** Coimbra Business School | ISCAC

   **Curso:** Licenciatura em Ciência de Dados para a Gestão

   **Unidade Curricular:** Projeto em Ciência de Dados

   **Professor Responsável:** Dora Melo (dmelo@iscac.pt) 
  
