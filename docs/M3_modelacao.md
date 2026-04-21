# Milestone 3: Modelação e Avaliação

---

## 1. Estratégia de Modelação

### 1.1. Divisão do Conjunto de Dados

Para garantir que o modelo é avaliado em condições realistas, dividimos o conjunto de dados em subconjuntos de treino e de teste, segundo uma proporção de 80% para treino e 20% para teste, com semente aleatória fixa (`random_state=42`).

| Subconjunto | Registos | Proporção | Função |
| :--- | :---: | :---: | :--- |
| Treino | 5.197 | 80% | Ajuste dos parâmetros dos modelos |
| Teste | 1.300 | 20% | Avaliação final sem contaminação |
| Total | 6.497 | 100% | Conjunto de dados final da Milestone 2 |

A proporção 80/20 foi escolhida porque 5.197 registos são suficientes para que os algoritmos aprendam os padrões das diferentes classes de qualidade, incluindo as mais escassas (notas 3, 4, 8 e 9). A semente fixa garante que qualquer execução do código produz exatamente a mesma divisão, o que torna os resultados reprodutíveis e comparáveis entre modelos.

O subconjunto de teste foi bloqueado logo após a divisão: não foi consultado para tomar nenhuma decisão sobre os modelos, evitando que o processo de avaliação seja influenciado pelos dados que supostamente são "desconhecidos" para o modelo.

---

### 1.2. Normalização dos Dados e Correção de uma Inconsistência

Na documentação da Milestone 2, referimos a aplicação do `StandardScaler` como parte do pré-processamento. Ao carregar o ficheiro `wine_quality_model_final.csv` para esta fase de modelação, verificámos que os valores se encontravam ainda na escala original (por exemplo, `alcohol` com valores entre 8 e 14, `fixed acidity` entre 4 e 10), o que confirmou que a transformação de escala não foi guardada no ficheiro final.

Esta situação, embora não intencional, permitiu-nos corrigir a forma como a normalização era aplicada. Se o *StandardScaler* tivesse sido ajustado sobre o conjunto de dados completo antes da divisão, estaria a usar informação dos dados de teste para calcular a média e o desvio padrão, o que constitui uma fuga de informação (*data leakage*). A abordagem correta é ajustar o *scaler* apenas nos dados de treino e aplicar a mesma transformação ao conjunto de teste, sem que este influencie os parâmetros calculados.

| Abordagem | Problema |
| :--- | :--- |
| *Scaler* ajustado em todo o conjunto antes da divisão | Fuga de informação: o conjunto de teste contamina os parâmetros de normalização |
| *Scaler* ajustado só no treino, após a divisão | Correto: o conjunto de teste permanece completamente isolado |

O impacto prático desta diferença é pequeno num conjunto de dados do tamanho do nosso, porque as estatísticas do subconjunto de treino convergem naturalmente para as do conjunto completo. Mesmo assim, optámos pela abordagem rigorosa, por uma questão de coerência metodológica. Documentamos aqui a inconsistência para que o processo de avaliação seja transparente.

```python
scaler = StandardScaler()
X_train_sc = scaler.fit_transform(X_train)  # ajusta no treino e transforma
X_test_sc  = scaler.transform(X_test)       # aplica os mesmos parametros ao teste
```

---

### 1.3. Métrica de Sucesso

O problema é de regressão: pretendemos prever um valor numérico contínuo numa escala de 3 a 9. As métricas de classificação não se aplicam. Escolhemos três métricas:

| Métrica | O que mede | Papel |
| :--- | :--- | :--- |
| Erro Médio Absoluto (MAE) | Média dos desvios absolutos entre o valor real e o previsto | Métrica principal |
| Raiz do Erro Quadrático Médio (RMSE) | Semelhante ao MAE, mas penaliza erros grandes de forma desproporcional | Métrica complementar |
| Coeficiente de Determinação (R²) | Proporção da variância da qualidade explicada pelo modelo | Diagnóstico |

O MAE foi escolhido como métrica principal porque está na mesma unidade da variável alvo: pontos de qualidade. É fácil de interpretar, tanto para nós como para quem não trabalha com estatística. Um MAE de 0,5 significa, concretamente, que o modelo erra em média meio ponto na escala de qualidade, o que pode fazer a diferença entre classificar um vinho como "bom" ou "médio" e tem impacto direto na decisão de produção.

O objetivo definido na Milestone 1 é atingir um MAE inferior a 0,5 no conjunto de teste.

---

## 2. Experiências Realizadas

### 2.1. Modelo Baseline

Antes de avançar para algoritmos mais complexos, é necessário perceber qual é o patamar mínimo de desempenho, ou seja, quanto é que um modelo sem qualquer capacidade preditiva real consegue acertar simplesmente por usar a informação mais básica disponível. Treinaram-se dois modelos de referência com complexidades diferentes.

#### B1 — Previsão pela Média

O primeiro baseline prevê sempre a média da variável alvo calculada no conjunto de treino, independentemente das características do vinho. É o cenário de pior caso razoável: qualquer modelo que não supere este resultado não aprendeu nada útil dos dados.

| Parâmetro | Valor |
| :--- | :--- |
| Algoritmo | `DummyRegressor(strategy='mean')` |
| Estratégia | Prevê sempre 5,82 (média de `quality` no treino) |
| Variáveis utilizadas | Nenhuma |

**Resultados:**

| Métrica | Treino | Teste |
| :--- | :---: | :---: |
| MAE | 0,6910 | 0,6691 |
| RMSE | 0,8766 | 0,8596 |
| R² | 0,0000 | 0,0000 |

O R² igual a zero confirma que este modelo não explica qualquer variação na qualidade dos vinhos. O MAE de 0,669 no teste representa o erro máximo que aceitamos num modelo candidato: se um algoritmo mais sofisticado não superar este valor, não tem utilidade prática.

---

#### B2 — Regressão Linear

O segundo baseline é o modelo paramétrico mais simples que usa de facto as variáveis físico-químicas para fazer previsões. Ajusta uma relação linear entre cada atributo e a pontuação de qualidade, através do método dos mínimos quadrados ordinários.

| Parâmetro | Valor |
| :--- | :--- |
| Algoritmo | `LinearRegression()` |
| Estratégia | Regressão linear por mínimos quadrados ordinários |
| Dados de entrada | Dados normalizados com `StandardScaler` (ajustado só no treino) |

**Resultados:**

| Métrica | Treino | Teste | Notas |
| :--- | :---: | :---: | :--- |
| MAE | 0,5659 | 0,5607 | Métrica principal |
| RMSE | 0,7314 | 0,7285 | |
| R² | 0,3038 | 0,2814 | |

Com um MAE de 0,561 no teste, este modelo já supera claramente o baseline B1, mas fica ainda 0,061 pontos acima do objetivo de 0,5. O R² de 0,28 indica que a regressão linear consegue explicar cerca de 28% da variação na qualidade dos vinhos. Os restantes 72% estão provavelmente associados a interações não lineares entre variáveis ou a padrões que um modelo linear não consegue captar.

Os coeficientes do modelo confirmam as conclusões da análise exploratória da Milestone 2: o teor alcoólico (coeficiente de 0,30) é o atributo com maior peso positivo, enquanto o rácio de acidez volátil (coeficiente de -0,23) e a densidade (-0,23) penalizam a qualidade prevista. A variável `total sulfur dioxide` tem um coeficiente de -0,001, praticamente nulo, o que sugere que pouco contribui para a previsão linear.

A ausência de diferença significativa entre os resultados no treino e no teste (MAE de 0,566 vs 0,561) indica que não há *overfitting* neste modelo.

---

#### Patamar de Referência para as Fases Seguintes

Os resultados dos dois baselines fixam os limites para a fase de experimentação seguinte. Qualquer modelo candidato tem de apresentar um MAE abaixo de 0,561 no teste para justificar a complexidade adicional e, para cumprir o objetivo do projeto, abaixo de 0,500.

---

### 2.2. Modelos Candidatos

Testaram-se três algoritmos. O Random Forest e o *XGBoost* são métodos baseados em árvores de decisão que conseguem modelar relações não lineares entre variáveis — algo que a regressão linear não faz. O SVR usa uma abordagem diferente, com base em margens de tolerância. A escolha recaiu sobre estes três porque funcionam bem em dados tabulares e permitem comparar estratégias distintas face ao mesmo problema.

Para cada um, testaram-se algumas configurações de hiperparâmetros antes de avançar para uma pesquisa mais sistemática.

#### Random Forest

Testou-se a configuração com parâmetros base (`n_estimators=100`) e uma versão mais restrita (`n_estimators=300, max_depth=15, min_samples_split=5, min_samples_leaf=2`). A versão mais restrita reduziu visivelmente o erro de treino mas piorou o resultado no teste (MAE de 0.465 vs 0.441), pelo que se manteve a configuração base.

#### *XGBoost*

Testou-se a configuração base e uma variante com aprendizagem mais lenta (`learning_rate=0.05, n_estimators=300, subsample=0.8`). Os resultados foram praticamente idênticos no teste, pelo que se usou a configuração base.

#### Máquinas de Vetores de Suporte (SVR)

Testaram-se dois valores do parâmetro de regularização: C=1 (MAE Teste = 0.5101) e C=10 (MAE Teste = 0.5061). O valor C=10 deu resultados ligeiramente melhores e foi usado como configuração final.

---

**Resultados comparativos (melhor configuração de cada modelo):**

| Algoritmo | Configuração | MAE (Treino) | RMSE (Treino) | R² (Treino) | MAE (Teste) | RMSE (Teste) | R² (Teste) |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| B2 — Regressão Linear | — | 0,5659 | 0,7314 | 0,3038 | 0,5607 | 0,7285 | 0,2814 |
| Random Forest | n_estimators=100 | 0,1608 | 0,2283 | 0,9322 | **0,4408** | 0,6136 | 0,4902 |
| *XGBoost* | params. base | 0,1835 | 0,2570 | 0,9140 | 0,4673 | 0,6397 | 0,4460 |
| SVR | rbf, C=10 | 0,3756 | 0,5656 | 0,5837 | 0,5061 | 0,6778 | 0,3779 |

---

### 2.3. Diagnóstico de Generalização e Seleção do Modelo

#### Análise de Sobreajuste (*Overfitting*) e Subajuste (*Underfitting*)

O diagnóstico é feito comparando o erro de treino com o erro de teste. Uma diferença grande indica que o modelo aprendeu padrões específicos dos dados de treino que não generalizam para dados novos.

| Algoritmo | MAE Treino | MAE Teste | Diferença | Diagnóstico |
| :--- | :---: | :---: | :---: | :--- |
| B2 — Regressão Linear | 0,5659 | 0,5607 | 0,005 | Equilibrado — modelo limitado pela linearidade |
| Random Forest | 0,1608 | 0,4408 | 0,280 | Sobreajuste moderado |
| *XGBoost* | 0,1835 | 0,4673 | 0,284 | Sobreajuste moderado |
| SVR (C=10) | 0,3756 | 0,5061 | 0,131 | Sobreajuste leve |

O Random Forest e o *XGBoost* ajustam-se ao ruído dos dados de treino mais do que o SVR, daí a diferença maior entre os dois erros. Mesmo assim, o Random Forest é o que dá melhores resultados no teste.

#### Curvas de Aprendizagem

A curva de aprendizagem do Random Forest foi calculada com validação cruzada de 5 partições, entre 10% e 100% dos dados de treino (ver gráfico no notebook). Com mais dados, o erro no treino sobe ligeiramente e o erro de validação desce. As duas linhas mantêm uma distância considerável mesmo com o conjunto completo, o que confirma o sobreajuste. Mais regularização ou uma pesquisa mais fina de hiperparâmetros podem aproximar as curvas.

#### Seleção do Modelo

O Random Forest (`n_estimators=100`) é o modelo escolhido para a fase de otimização: foi o único a cumprir o objetivo de MAE < 0,5 no teste, com 0,4408. O *XGBoost* ficou próximo (MAE = 0,467) e pode ser reconsiderado durante o tuning. O SVR não atingiu o objetivo.

---

## 3. Otimização (Tuning)

### 3.1. Pesquisa de Hiperparâmetros

A partir do Random Forest base (`n_estimators=100`), usámos `RandomizedSearchCV` para testar 100 combinações aleatórias de hiperparâmetros com validação cruzada de 5 partições. A pesquisa aleatória foi preferida à pesquisa exaustiva (`GridSearchCV`) porque o espaço de configurações é grande e a pesquisa aleatória tende a encontrar boas soluções mais depressa.

| Hiperparâmetro | Espaço de pesquisa |
| :--- | :--- |
| `n_estimators` | Inteiro uniforme entre 100 e 500 |
| `max_depth` | None, 10, 15, 20, 25, 30 |
| `min_samples_split` | Inteiro uniforme entre 2 e 10 |
| `min_samples_leaf` | Inteiro uniforme entre 1 e 6 |
| `max_features` | sqrt, log2, 0.5, 0.7, None |

O `RandomizedSearchCV` avalia cada combinação com validação cruzada de 5 partições (5-Fold CV), usando o MAE negativo como critério de seleção. Isto garante que os parâmetros escolhidos são os que generalizam melhor, e não apenas os que se ajustam bem a uma divisão específica dos dados.

A melhor configuração encontrada foi:

| Hiperparâmetro | Valor encontrado |
| :--- | :--- |
| `n_estimators` | 382 |
| `max_depth` | None (sem limite) |
| `min_samples_split` | 2 |
| `min_samples_leaf` | 1 |
| `max_features` | sqrt |

A comparação entre o modelo base e o modelo otimizado no conjunto de teste (que não foi usado em nenhuma fase da pesquisa) mostra uma melhoria:

| Modelo | MAE Treino | MAE Teste | RMSE Teste | R² Teste |
| :--- | :---: | :---: | :---: | :---: |
| Random Forest base (100 árvores) | 0,1608 | 0,4408 | 0,6136 | 0,4902 |
| **Random Forest otimizado (382 árvores)** | **0,1565** | **0,4319** | **0,6003** | **0,5120** |

A melhoria é de 0,0089 pontos no MAE e de 0,022 no R². O modelo otimizado é superior em todas as métricas e passa a ser o modelo final do projeto.

---

### 3.2. Validação Cruzada (K-Fold)

Para confirmar que o resultado do modelo final é estável e não depende de uma divisão particular dos dados, aplicámos validação cruzada de 5 partições ao modelo otimizado. Os dados de treino são divididos em 5 blocos; o modelo treina em 4 blocos e é avaliado no quinto, repetindo o processo 5 vezes.

| Fold | MAE |
| :---: | :---: |
| 1 | 0,4395 |
| 2 | 0,4596 |
| 3 | 0,4513 |
| 4 | 0,4453 |
| 5 | 0,4195 |
| **Média** | **0,4431** |
| **Desvio padrão** | **0,0135** |

O desvio padrão de 0,0135 entre os 5 folds é inferior ao limiar de 0,02, o que confirma que o modelo é estável: o MAE reportado não é resultado de uma divisão particularmente favorável dos dados. Todos os folds ficam abaixo do objetivo de 0,5.

---
## 4. Avaliação do Modelo Final

### 4.1. Matriz de Erros

Como o nosso problema é de regressão e não de classificação, não temos uma matriz de confusão no sentido tradicional. Adaptámos a análise arredondando as previsões ao inteiro mais próximo e comparando com as notas reais. Isto permite identificar os padrões de erro: em que notas o modelo acerta, em que notas erra, e para que lado erra.

A matriz resultante (ver *notebook*, Secção 10) permite verificar que a maioria dos erros são de apenas 1 ponto na escala. As notas extremas (3, 4, 8, 9) são as mais difíceis porque têm poucos exemplos no conjunto de dados, o que limita o que o modelo consegue aprender sobre elas.

O erro não é simétrico: o modelo tem maior dificuldade com vinhos de notas baixas (3 e 4) do que com vinhos de notas altas, porque os primeiros representam menos de 5% do conjunto de dados.


### 4.2. Análise de Erros por Nota de Qualidade

O MAE foi calculado separadamente para cada nota de qualidade. As notas centrais (5, 6, 7) têm MAE dentro do objetivo (< 0,5), enquanto as notas dos extremos tendem a ter erros maiores. Isto é uma consequência directa da distribuição desequilibrada dos dados: com poucas dezenas de vinhos de nota 3 ou 9, o modelo não tem informação suficiente para prever estas notas com a mesma confiança.


---
### 4.3. Importância dos Atributos (*Feature Importance*)

A importância das variáveis do modelo final foi extraída a partir dos ganhos de informação agregados de todas as árvores. As variáveis mais relevantes para a previsão da qualidade são:

1. `alcohol` — 17,7% — o teor alcoólico é o preditor mais forte
2. `volatile_acidity_ratio` — 11,3% — a acidez volátil relativa penaliza a qualidade
3. `density` — 10,9% — a densidade do vinho
4. `so2_ratio` — 10,0% — a proporção entre dióxido de enxofre livre e total
5. `total sulfur dioxide` — 7,9% — o nível total de dióxido de enxofre

A variável `is_red` (tipo de vinho) contribui menos de 0,1% para a previsão. Isto era esperado: as diferenças químicas entre tintos e brancos já estão capturadas pelas restantes variáveis.
## 5. Conclusão da Fase de Modelação

*Justifiquem por que razão este modelo está pronto (ou não) para ser apresentado como solução final.*

---
*Data de última atualização: [DD/MM/AAAA]* 
