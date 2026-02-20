# Milestone 1: Iniciação e Definição do Projeto
## 1. Descrição Detalhada do Problema
O projeto insere-se no setor da Indústria Vinícola, especificamente na produção de Vinho Verde no norte de Portugal. A qualidade do vinho é tradicionalmente avaliada através de testes sensoriais (provas) realizados por especialistas humanos. No entanto, este método apresenta desafios críticos: é subjetivo, dispendioso, lento e sujeito a inconsistências individuais.

No cenário atual de produção em larga escala e exportação global, a manutenção de um padrão de qualidade rigoroso é fundamental para a competitividade das empresas. A relevância deste problema reside na necessidade de digitalizar o controlo de qualidade. Ao correlacionar variáveis físico-químicas (como acidez volátil, cloretos e álcool) com as pontuações de qualidade, é possível criar um sistema de apoio à decisão que:

- Antecipe a classificação do vinho antes da etapa sensorial.
- Detete anomalias químicas que possam comprometer lotes inteiros.
- Otimize os custos operacionais ao reduzir a dependência exclusiva de provadores para todas as fases da produção.

Desta forma, o problema deixa de ser apenas uma questão de paladar e passa a ser uma questão de análise preditiva de dados laboratoriais para garantir a consistência do produto final.
## 2. Objetivos SMART
O objetivo principal deste projeto é avaliar até que ponto as características químicas de um vinho podem substituir ou apoiar as avaliações sensoriais humanas, que são muitas vezes subjetivas e dispendiosas. Optou-se pela utilização de modelos de regressão, uma vez que a variável de qualidade está definida numa escala numérica de 0 a 10. Desta forma, o modelo não se limita a classificar o vinho em categorias, mas tenta prever com exatidão a pontuação que seria atribuída pelos especialistas.

A meta de alcançar um Erro Médio Absoluto (MAE) inferior a 0.5 justifica-se pela necessidade de precisão no setor vitivinícola. Na prática, um desvio superior a meio ponto poderia levar a uma classificação incorreta do perfil de um lote, afetando o seu posicionamento no mercado. Por exemplo, prever uma nota 6 para um vinho que é, na verdade, um 7, poderia desvalorizar um produto de qualidade superior. Assim, este limite de erro garante que as previsões sejam fiáveis e úteis para o controlo de qualidade. Este percurso de otimização será realizado até ao final da Milestone 3, permitindo testar e ajustar os algoritmos para cumprir este rigor técnico.
## 3. Perguntas de Investigação

O projeto pretende responder às seguintes questões de forma a validar a utilidade prática do modelo:

* **PI 1: Qual é a capacidade de previsão de um modelo baseado apenas em dados físico-químicos?**
  Pretende-se verificar se é possível apoiar/substituir a avaliação sensorial humana através de um algoritmo com uma margem de erro aceitável.
* **PI 2: Quais são as 3 variáveis físico-químicas que mais contribuem para a previsão de um vinho de "Alta Qualidade" (nota ≥ 7)?**
  O objetivo é identificar, entre os 11 atributos físico-químicos, quais funcionam como os principais indicadores de excelência.
* **PI 3: Existem diferenças críticas nos padrões de qualidade entre vinhos tintos e vinhos brancos?**
  Utilizando a variável `is_red`, o projeto irá testar se um modelo treinado numa variante de vinho mantém a eficácia ao prever a qualidade da outra.
## 4. Ferramentas e Bibliotecas Utilizadas

O ambiente de trabalho foi configurado através do Kaggle Kernels, permitindo-nos processar o código em nuvem com acesso direto aos ficheiros de dados. Para garantir que tudo se mantém organizado, o Kaggle está conectado ao GitHub, o que facilita a sincronização do código e o controlo das versões que vamos produzindo.

A gestão e o desenvolvimento foram assegurados pelas seguintes ferramentas:

* **Gestão:** Para gerir as tarefas, utilizámos o GitHub Projects com um quadro Kanban, enquanto a comunicação rápida e as decisões de equipa foram feitas através do Discord e WhatsApp.
* **Desenvolvimento:** Nesta fase, a biblioteca Pandas foi a nossa ferramenta central e a única que utilizámos efetivamente para carregar os ficheiros, unificar os dados dos vinhos e realizar a primeira inspeção.

Embora bibliotecas como NumPy, Matplotlib e Seaborn já tenham sido importadas no início do nosso notebook, ainda não as chamámos para nenhuma operação nesta etapa inicial. Estas ferramentas ficam reservadas para as fases de análise visual e modelação que se seguem.
## 5. Metodologia de Gestão (PBL)
 * **Afonso Carvalho:** Responsável pela Engenharia de Dados e Visualização.
 * **Marcello Portugal:** Responsável pela Modelação Estatística e Documentação.
* **Ferramentas de Colaboração:** Utilizamos o GitHub Projects (Kanban), o Kaggle para o desenvolvimento do código e o Discord/WhatsApp para a comunicação diária do projeto.
## 6. Estrutura e Preparação dos Dados

Após a importação dos dados originais, o primeiro passo consistiu na unificação dos ficheiros de vinhos tintos e brancos através do comando `pd.concat()`. Esta junção resultou num conjunto de dados robusto com 6.497 amostras, consolidando todas as variáveis físico-químicas num único objeto de análise. Durante este processo, foi fundamental validar a integridade do dataset; a execução do comando `df.isnull().sum()` confirmou a ausência total de valores nulos, o que assegura que não haverá necessidade de realizar limpezas ou imputações de dados nas fases seguintes.

Para organizar esta unificação, criámos duas variáveis distintas com propósitos diferentes. A variável categórica `type` foi mantida para facilitar a identificação visual e a análise exploratória (segmentando os vinhos como "red" ou "white"). Em paralelo, criámos a variável binária `is_red` (codificada como 1 para tinto e 0 para branco), uma etapa essencial de engenharia de dados que permite que os futuros algoritmos de Aprendizagem Automática processem a diferença entre os tipos de vinho de forma matemática. 

Através do comando `df.describe()`, observámos que a variável alvo (`quality`) se distribui entre os 3 e os 9 pontos. No entanto, é importante notar que o conjunto de dados apresenta um desequilíbrio, com a grande maioria das amostras concentrada nos valores médios (5 e 6), existindo pouca representatividade nos vinhos de qualidade extrema (muito baixa ou muito alta).



### Dicionário de Variáveis e Intervalos Observados

| Variável | Tipo | Natureza | Descrição | Intervalo [Mín - Máx] |
| :--- | :--- | :--- | :--- | :--- |
| `fixed acidity` | Float64 | Preditiva | Ácidos fixos do vinho. | [3.8 - 15.9] |
| `volatile acidity` | Float64 | Preditiva | Ácidos voláteis (aroma). | [0.08 - 1.58] |
| `citric acid` | Float64 | Preditiva | Confere frescura. | [0.0 - 1.66] |
| `residual sugar` | Float64 | Preditiva | Açúcar pós-fermentação. | [0.6 - 65.8] |
| `chlorides` | Float64 | Preditiva | Teor de sais. | [0.009 - 0.611] |
| `free sulfur dioxide` | Float64 | Preditiva | SO2 na forma livre. | [1.0 - 289.0] |
| `total sulfur dioxide` | Float64 | Preditiva | Teor total de SO2. | [6.0 - 440.0] |
| `density` | Float64 | Preditiva | Densidade da amostra. | [0.987 - 1.039] |
| `pH` | Float64 | Preditiva | Nível de acidez. | [2.72 - 4.01] |
| `sulphates` | Float64 | Preditiva | Aditivos antioxidantes. | [0.22 - 2.0] |
| `alcohol` | Float64 | Preditiva | Teor alcoólico (%). | [8.0 - 14.9] |
| **`quality`** | **Int64** | **Alvo** | **Pontuação de 0 a 10.** | **[3 - 9]** |
| `type` | Object | Identificação | Texto (red/white). | [N/A] |
| `is_red` | Int64 | Identificação | Binário (1=T / 0=B). | [0 - 1] |
## 7. Viabilidade e Confiança nos Dados
Validada a estrutura dos dados no ponto anterior, a continuidade do trabalho é assegurada pelos seguintes requisitos técnicos e éticos:
* **Disponibilidade:** Os dados foram obtidos através do repositório oficial da UCI e a sua integração foi validada com sucesso no ambiente de trabalho Kaggle.
* **Fiabilidade:** A integridade dos dados e a ausência de valores nulos permitem que o desenvolvimento se foque diretamente na criação e otimização dos modelos, prevenindo a necessidade de correções ou limpezas complexas durante o processo.
* **Ética e Citação:** O projeto cumpre as normas de proteção de dados e privacidade, uma vez que apenas estão disponíveis variáveis físico-químicas e sensoriais, sem qualquer informação sobre marcas ou produtores. Conforme solicitado pelos autores (P. Cortez et al., 2009), a utilização destes dados para fins de investigação será devidamente creditada através da citação do artigo original publicado na revista Decision Support Systems.
## 8. Cronograma Interno
| Fase | Data Limite | Entregável Esperado |
| :--- | :--- | :--- |
| M1: Iniciação | [Data] | Repositório estruturado e Plano de Projeto. |
| M2: Exploração | [Data] | Notebook de EDA e Dados Processados. |
| M3: Modelação | [Data] | Comparação de algoritmos e métricas. |
| M4: Finalização| [Data] | Pitch e Relatório Final. |
---
*Data de última atualização: [DD/MM/AAAA]*
