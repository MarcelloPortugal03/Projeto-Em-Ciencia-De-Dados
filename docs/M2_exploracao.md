# Milestone 2: Análise Exploratória e Engenharia de Atributos
## 1. Análise Exploratória de Dados (EDA)
### 1.1. Distribuição da Variável Alvo

A variável alvo deste projeto é a `quality`, que representa a pontuação atribuída aos vinhos numa escala de 0 a 10. Como estamos perante um problema de regressão, a análise da dispersão e da forma como estes valores se distribuem é essencial para o sucesso da modelação futura.

* **Concentração Central:** A grande maioria dos vinhos no nosso dataset concentra-se nas notas intermédias, especificamente entre os valores 5, 6 e 7. Isto indica que temos uma base sólida para prever vinhos de qualidade média.
* **Escassez de Extremos:** Observamos que existem poucos registos nos extremos da escala (notas 3, 4, 8 e 9). Isto é algo que poderá vir a exigir uma atenção especial na fase de treino, uma vez que o modelo terá menos exemplos desses para aprender as características de vinhos de qualidade muito baixa ou de excelência.


### 1.2. Análise Univariada

Nesta etapa, analisamos individualmente cada atributo do dataset para identificar padrões de distribuição, presença de valores atípicos e o equilíbrio entre os tipos de vinho.

**Variáveis Categóricas (Tipo de Vinho):**
* **Predomínio de Vinhos Brancos:** O dataset apresenta um desequilíbrio acentuado entre os tipos de vinho. Os vinhos brancos compõem cerca de 75,39% da amostra (4898 registos), enquanto os vinhos tintos representam apenas 24,61% (1599 registos). Esta disparidade indica que o modelo será treinado com uma base muito mais vasta de perfis químicos de vinhos brancos.

**Variáveis Numéricas (Físico-Químicas):**
* **Assimetria e Distribuição:** A maioria das variáveis apresenta uma distribuição positivamente assimétrica (cauda longa à direita). Atributos como `residual sugar`, `chlorides` e `free sulfur dioxide` mostram picos de concentração em valores baixos, com uma dispersão longa para valores mais elevados.
* **Presença de Outliers:** Através dos boxplots, é possível observar uma quantidade significativa de outliers em quase todas as variáveis, com especial destaque para `residual sugar` e `chlorides`. Estes valores extremos refletem vinhos com características químicas muito fora da média, o que exigirá um tratamento de escala ou limpeza para evitar o enviesamento dos algoritmos de regressão.
* **Proximidade à Normalidade:** Variáveis como o `pH` e a `density` são as que mais se aproximam de uma distribuição gaussiana, apresentando uma forma mais simétrica e centrada em comparação com as restantes.
#### 1.2.1. Análise de Métricas Relevantes
Ao cruzarmos as visualizações com as estatísticas descritivas, selecionámos os indicadores mais significativos para fundamentar as observações feitas anteriormente:

* **Açúcar Residual:** A amplitude desta variável é consideravelmente alta, variando entre 0.6 e 65.8. O facto de o desvio padrão (4.76) ser quase tão alto quanto a média (5.44) confirma que os vinhos neste dataset têm perfis de doçura muito distintos entre si.
* **Cloretos:** Embora 75% dos vinhos tenham valores de cloretos abaixo de 0.065, o valor máximo chega aos 0.611. Esta diferença de quase dez vezes prova que os outliers que vimos nos gráficos são, de facto, desvios extremos que precisam de atenção.
* **pH:** Os números reforçam a estabilidade do pH, que apresenta um desvio padrão muito baixo (0.16). Com a média (3.22) praticamente igual à mediana (3.21), confirmamos que esta é a variável com o comportamento mais previsível e simétrico do conjunto.
  
### 1.3. Análise Bivariada e Correlações Relevantes


Nesta fase, avaliámos a intensidade e a direção das correlações entre as variáveis do dataset, com foco no seu impacto na qualidade e na deteção de possíveis problemas de multicolinearidade.

### 1.3.1. Visão Geral: Matriz de Correlação (Heatmap)
O **Heatmap de Pearson** é a nossa ferramenta de triagem principal. Através da intensidade das cores e dos coeficientes numéricos, observamos que:
* **Complexidade do Dataset:** Não existe uma única variável que determine a qualidade isoladamente com uma correlação perfeita (próxima de 1). 
* **Distribuição de Força:** A maioria das variáveis apresenta correlações fracas (entre -0.1 e 0.1), o que justifica a necessidade de um modelo de Machine Learning que combine múltiplos fatores para obter previsões precisas.

### 1.3.2. Análise dos Atributos vs. Alvo (Qualidade)
Embora tenhamos gerado múltiplos gráficos, focámos a análise detalhada nos preditores que demonstraram maior significância estatística:

* **Os Preditores Mais Fortes:**
    * **Teor Alcoólico ($r = 0.44$):** É a variável com maior impacto positivo. Visualmente, a linha de regressão é a mais inclinada, confirmando que o álcool é o fator que mais "empurra" a nota final para cima.
    * **Acidez Volátil ($r = -0.27$):** É o principal detrator da qualidade. Escolhemos este gráfico porque ele explica a maior parte da variação negativa; quanto mais o vinho "avinagra" (ácido acético), mais a qualidade desce de forma linear.

* **Os Preditores Irrelevantes (Correlações Fracas):**
    * Variáveis como Açúcar Residual ($r = 0.01$) e pH ($r = -0.05$) apresentam linhas de regressão praticamente horizontais. Isto indica que, isoladamente, estas características têm quase influência nula na percepção de qualidade, podendo ser candidatas a exclusão para simplificar o modelo.

### 1.3.3. Interdependência e Multicolinearidade
Para garantir a estabilidade do modelo e evitar a redundância de dados (multicolinearidade), analisámos a forma como as variáveis comunicam entre si. Em vez de testarmos todas as combinações, focámo-nos em três relações específicas. Esta escolha foi intencional: selecionámos os pares que apresentaram as correlações mais fortes no nosso Heatmap e que, simultaneamente, possuem uma explicação lógica na química do vinho:

1.  **Álcool vs. Densidade ($r = -0.69$):** A relação mais forte de todo o dataset. Fisicamente, o álcool reduz a densidade do vinho, criando uma correlação inversa muito nítida.
2.  **Açúcar Residual vs. Densidade ($r = 0.55$):** O açúcar aumenta a massa volúmica, contrabalançando o efeito do álcool.
3.  **Acidez Fixa vs. pH ($r = -0.25$):** Uma relação química básica que valida a integridade dos dados (maior acidez fixa baixa o pH).

## 2. Qualidade dos Dados e Limpeza

Nesta fase, auditamos a integridade do dataset para garantir que a modelagem futura não seja enviesada por dados ausentes ou inconsistentes.

### 2.1. Diagnóstico de Valores Nulos
Utilizámos o método `isnull().sum()` para verificar a presença de lacunas em todas as variáveis físico-químicas e na variável alvo.

| Coluna | Percentagem de Nulos | Estado |
| :--- | :--- | :--- |
| fixed acidity | 0% | OK |
| volatile acidity | 0% | OK |
| citric acid | 0% | OK |
| residual sugar | 0% | OK |
| chlorides | 0% | OK |
| free sulfur dioxide | 0% | OK |
| total sulfur dioxide | 0% | OK |
| density | 0% | OK |
| pH | 0% | OK |
| sulphates | 0% | OK |
| alcohol | 0% | OK |
| quality | 0% | OK |

**Resultado:** A inspeção confirmou que o dataset apresenta **0% de valores nulos** em todas as colunas. 

### 2.2. Definição da Estratégia de Limpeza
Embora o dataset atual esteja completo, estabelecemos o seguinte protocolo de qualidade para assegurar a reprodutibilidade do projeto em novos carregamentos de dados:

1.  **Tratamento de Nulos:** Caso surgissem valores ausentes em variáveis numéricas, a estratégia prioritária seria a **Imputação pela Mediana**, dado que o `df.describe()` revelou um desvio padrão considerável e a presença de potenciais *outliers* em colunas como `residual sugar`.
2.  **Integridade de Tipos:** A inspeção via `df.info()` confirmou que todos os preditores são numéricos (`float64` ou `int64`), o que é ideal para o processamento matemático sem necessidade de conversões complexas nesta fase.

### 2.3. Documentação da Decisão Final
* **Estratégia Adotada:** Manutenção integral do dataset original.
* **Justificação:** Como não foram detetados valores nulos ou tipos de dados incongruentes, optámos por não aplicar algoritmos de imputação ou eliminação de linhas, preservando a variância natural e a distribuição original dos dados químicos.
### 2.4. Outliers e Inconsistências
*Descrevam se encontraram valores impossíveis (ex: idade = 200) e como os resolveram.*
## 3. Engenharia de Atributos (Feature Engineering)
### 3.1. Transformações Realizadas
* **Encoding:** (Ex: "Convertemos a variável 'Género' em numérica usando One-Hot Encoding.")
* **Escalonamento:** (Ex: "Aplicámos o StandardScaler nas variáveis numéricas para que todas
fiquem na mesma escala.")
### 3.2. Criação de Novos Atributos
*Descrevam as variáveis que criaram para ajudar o modelo.*
* **Nova Variável [Nome]:** (Ex: "Criámos a 'Tenure_Per_Year' que divide o tempo de contrato
pela idade do cliente.")
## 4. Dicionário de Dados Final (Pós-Processamento)
*Listagem final das variáveis que serão entregues ao modelo na Fase 3.*
| Atributo | Tipo | Descrição |
| :--- | :--- | :--- |
| `cliente_id` | ID | Removido (não preditivo) |
| `idade_norm` | Float | Idade após normalização |
| `is_premium` | Binary | 1 para clientes com plano superior |
## 5. Conclusões da Fase de Exploração
*O que aprenderam sobre o dataset que não sabiam no final do Milestone 1? Os dados são suficientes
para avançar para a modelação?*
---
*Data de última atualização: [DD/MM/AAAA]* 
