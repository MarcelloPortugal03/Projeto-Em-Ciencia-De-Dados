# Milestone 3: Modelação e Avaliação

---

## 1. Estratégia de Modelação

### 1.1. Divisão do Conjunto de Dados

Para garantir que o modelo é avaliado em condições realistas, dividimos o conjunto de dados em subconjuntos de treino e de teste, segundo uma proporção de **80% para treino e 20% para teste**, com semente aleatória fixa (`random_state=42`).

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
 
## 2. Experiências Realizadas 
### 2.1. Modelo Baseline 
*O ponto de partida simples.* 
* **Algoritmo:** (p/ex.: Regressão Logística) 
* **Resultado:** (p/ex.: Accuracy: 0.72) 
 
### 2.2. Modelos Candidatos 
*Listagem dos algoritmos testados e a justificação da escolha.* 
 
| Algoritmo | Parâmetros Base | Métrica (Treino) | Métrica (Teste) | Notas | 
| :--- | :--- | :--- | :--- | :--- | 
| Random Forest | n_estimators=100 | 0.95 | 0.82 | Sinais de overfitting | 
| XGBoost | default | 0.88 | 0.85 | Melhor generalização | 
| SVM | kernel='rbf' | 0.80 | 0.79 | Lento no treino | 
 
## 3. Otimização (Tuning) 
*Descrevam como melhoraram o melhor modelo.* 
* **Técnica Utilizada:** (p/ex.: "Utilizámos GridSearchCV para ajustar os hiperparâmetros 
`max_depth` e `learning_rate`.") 
* **Melhoria obtida:** (p/ex.: "O F1-Score subiu de 0.85 para 0.88 após o ajuste.") 
 
## 4. Avaliação do Modelo Final 
### 4.1. Matriz de Confusão / Erros 
*Analisem onde o modelo mais falha.* 
> **Análise:** (p/ex.: "O modelo ainda confunde a Classe A com a Classe B em 10% dos casos devido 
à semelhança nos atributos X e Y.") 
 
### 4.2. Importância dos Atributos (Feature Importance) 
*Quais as variáveis que o modelo considerou mais importantes para decidir?* 
1. [Variável X] 
2. [Variável Y] 
 
## 5. Conclusão da Fase de Modelação 
*Justifiquem por que razão este modelo está pronto (ou não) para ser apresentado como solução 
final.* 
 --- 
*Data de última atualização: [DD/MM/AAAA]* 
