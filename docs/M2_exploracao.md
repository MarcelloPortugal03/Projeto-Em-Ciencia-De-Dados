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

* **Açúcar Residual:** A amplitude desta variável é enorme, variando entre 0.6 e 65.8. O facto de o desvio padrão (4.76) ser quase tão alto quanto a média (5.44) confirma que os vinhos neste dataset têm perfis de doçura muito distintos entre si.
* **Cloretos:** Embora 75% dos vinhos tenham valores de cloretos abaixo de 0.065, o valor máximo chega aos 0.611. Esta diferença de quase dez vezes prova que os outliers que vimos nos gráficos são, de facto, desvios extremos que precisam de atenção.
* **pH:** Os números reforçam a estabilidade do pH, que apresenta um desvio padrão muito baixo (0.16). Com a média (3.22) praticamente igual à mediana (3.21), confirmamos que esta é a variável com o comportamento mais previsível e simétrico do conjunto.
  
### 1.3. Análise Bivariada e Correlações Relevantes
*Quais as variáveis que têm maior relação com o problema? Incluam referências a gráficos que
geraram no Kaggle.*
* **Atributo A vs. Alvo:** (Ex: "Notámos que quanto maior a idade, menor a probabilidade de
cancelamento.")
* **Atributo B vs. Alvo:** (Ex: "O tipo de contrato mensal está fortemente ligado à saída de
clientes.")
## 2. Qualidade dos Dados e Limpeza
### 2.1. Tratamento de Dados em Falta (Missing Data)
* **Colunas afetadas:** [Lista de colunas]
* **Estratégia adotada:** (Ex: "Substituímos os nulos da coluna 'Salário' pela mediana para
evitar o impacto de outliers.")
### 2.2. Outliers e Inconsistências
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
