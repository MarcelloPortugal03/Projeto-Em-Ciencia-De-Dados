# Milestone 4: Conclusão e entrega de valor

---

## 1. Síntese de resultados

### 1.1. O problema que nos propusemos resolver

Na Milestone 1, definimos o seguinte objetivo: construir um modelo de regressão capaz de prever a qualidade sensorial do Vinho Verde a partir de variáveis físico-químicas, com um erro médio absoluto (MAE) inferior a 0,5 pontos numa escala de 0 a 10.

O modelo final é uma *Random Forest* com 382 árvores, otimizada via `RandomizedSearchCV`. No conjunto de teste obteve um MAE de **0,4319**, cumprindo o objetivo com margem de 0,068 pontos.

### 1.2. Respostas às perguntas de investigação

**PI 1 : Qual é a capacidade de previsão de um modelo baseado apenas em dados físico-químicos?**

O modelo atingiu um MAE de 0,4319 e um R² de 0,512. Em termos práticos, isto significa que as variáveis de laboratório permitem prever a qualidade com um erro médio inferior a meio ponto. Se um vinho tem nota real de 6, o modelo prevê tipicamente entre 5,6 e 6,4.

Os dados químicos explicam cerca de metade da variação nas notas. A outra metade depende de fatores que o *dataset* não capta: a perceção subjetiva dos provadores, as condições da prova, ou variáveis sensoriais sem correspondência direta nas análises laboratoriais.

Os dados físico-químicos têm, portanto, capacidade preditiva útil, embora limitada pela natureza subjetiva da variável alvo.

**PI 2 : Quais são as 3 variáveis físico-químicas que mais contribuem para a previsão de um vinho de qualidade superior (e.g., nota ≥ 7)?**

A análise de importância de variáveis do modelo final identificou três atributos com maior peso: o teor alcoólico (`alcohol`, 17,7%), o rácio de acidez volátil (`volatile_acidity_ratio`, 11,3%) e a densidade (`density`, 10,9%).

Vinhos com graduação alcoólica mais alta tendem a receber notas superiores. A acidez volátil funciona como penalizador: quanto maior a proporção face aos ácidos fixos, mais a nota desce. A densidade é negativamente correlacionada com a qualidade, o que faz sentido porque vinhos menos densos costumam ter mais álcool e menos açúcar residual.

Já na análise exploratória da Milestone 2 o teor alcoólico aparecia como o principal preditor positivo e a acidez volátil como o maior detrator. O modelo confirmou esse padrão.

**PI 3 : Existem diferenças nas variáveis que determinam a qualidade entre vinhos tintos e brancos?**

A variável `is_red` (indicador do tipo de vinho) obteve uma importância de 0,1% no modelo final — o valor mais baixo de todos os atributos. Este resultado indica que, quando as variáveis físico-químicas estão presentes, saber se o vinho é tinto ou branco não acrescenta informação útil à previsão.

O modelo foi treinado com o conjunto unificado de 6.497 registos (tintos + brancos). O facto de `is_red` ser praticamente irrelevante sugere que as variáveis que determinam a qualidade são as mesmas para ambos os tipos. A qualidade é determinada pela composição química do vinho, não pela sua cor.

### 1.3. Valor prático da solução

O modelo pode funcionar como ferramenta de triagem rápida na linha de produção. Um produtor que receba os resultados laboratoriais de um lote pode obter uma estimativa da qualidade sensorial antes de submeter o vinho ao painel de degustadores.

Isto não substitui a avaliação humana, substitui a espera por ela. Em operações com dezenas ou centenas de lotes, ter uma previsão imediata permite priorizar quais avançam primeiro para prova e quais precisam de ajustes químicos.

Das 1.300 previsões no conjunto de teste, 66,6% acertaram a nota exata. Apenas 2,6% erraram mais de 1,5 pontos. Para uma ferramenta de apoio à decisão (e não de decisão final), estes números são aceitáveis.

---

## 2. Análise crítica e limitações

### 2.1. Limitações dos dados

O *dataset* concentra-se nas notas intermédias. As notas 5, 6 e 7 representam a grande maioria dos registos, enquanto as notas extremas (3, 4, 8 e 9) têm poucos exemplos. Este desequilíbrio faz com que o modelo tenha mais dificuldade a prever vinhos de qualidade muito baixa ou muito alta, que são precisamente os casos em que a previsão seria mais valiosa.

A variável alvo (`quality`) é discreta e atribuída por consenso de um painel. Dois painéis diferentes podem atribuir notas diferentes ao mesmo vinho, o que introduz ruído nos dados que nenhum modelo consegue eliminar.

### 2.2. Limitações do modelo

A *Random Forest* apresenta sobreajuste moderado: o MAE no treino (0,1608) é bastante inferior ao MAE no teste (0,4319), com uma diferença de 0,275. Isto significa que o modelo memoriza parcialmente os dados de treino em vez de generalizar. A otimização de hiperparâmetros reduziu este efeito mas não o eliminou.

O modelo prevê valores contínuos, mas a variável alvo são inteiros. Quando o modelo prevê 5,4 para um vinho de nota 5, conta como erro de 0,4 pontos. Isto é uma consequência natural de usar regressão para prever uma escala discreta.

### 2.3. Contextos onde o modelo não é recomendado

O modelo não deve ser usado como único critério para classificar comercialmente um vinho. A margem de erro, embora aceitável para triagem, pode levar a erros de um ponto na escala, o que num contexto de certificação ou rótulo pode ter consequências legais.

Os dados são exclusivamente de Vinho Verde português. Aplicar o modelo a vinhos de outras regiões ou castas sem retreino não é aconselhável, porque os perfis químicos podem ser diferentes.

---

## 3. Considerações éticas

### 3.1. Privacidade

Conforme documentado pelos próprios autores do *dataset* (Cortez et al., 2009): *"Due to privacy and logistic issues, only physicochemical (inputs) and sensory (the output) variables are available (e.g. there is no data about grape types, wine brand, wine selling price, etc.)."* Os dados foram intencionalmente limitados a variáveis químicas e à pontuação de qualidade, sem incluir informação que permita identificar marcas, castas, quintas ou amostras individuais.

### 3.2. Transparência e explicabilidade

A *Random Forest* permite extrair a importância de cada variável na previsão. Isto torna o modelo interpretável: sabemos que o álcool, a acidez volátil e a densidade são os principais fatores, e podemos explicar porquê. O modelo não opera como uma "caixa negra".

O notebook `3.0_interpretacao.ipynb` contém uma análise dedicada à explicabilidade do modelo final, com gráficos de dependência parcial, importância por permutação e exemplos de previsões individuais.

Todos os resultados são reprodutíveis: os notebooks utilizam sementes fixas (`random_state=42`) e estão disponíveis publicamente no repositório. Qualquer pessoa pode correr o código e obter os mesmos valores.

---

## 4. Aplicação web

Desenvolvemos uma aplicação web onde se podem introduzir os valores laboratoriais de um vinho e obter a previsão de qualidade em tempo real.

**Acesso:** [https://vinhos-app.onrender.com/](https://vinhos-app.onrender.com/)

A aplicação recebe as 11 variáveis físico-químicas e o tipo de vinho, calcula automaticamente os rácios de engenharia de atributos (SO₂ e acidez volátil), normaliza os dados e devolve a previsão do modelo.

> *Nota: A aplicação está alojada num servidor gratuito. Se não receber visitas durante alguns minutos, o primeiro acesso pode demorar até 60 segundos a carregar. Basta aguardar.*

---

## 5. Trabalhos futuros

Se alguém quiser continuar ou melhorar este projeto, há duas direções concretas.

A primeira passa por lidar com o desequilíbrio dos dados. Técnicas como *SMOTE* ou reamostragem estratificada poderiam gerar exemplos sintéticos das notas extremas (3, 4, 8, 9), dando ao modelo mais material para aprender a prever os casos onde atualmente mais falha.

A segunda seria incluir variáveis externas. O *dataset* atual limita-se a 11 propriedades químicas. Variáveis como a casta, a região de produção, o ano de colheita ou as condições meteorológicas poderiam ajudar a explicar os 49% de variação que o modelo não capta.

---

*Data de conclusão: 17/05/2026*
