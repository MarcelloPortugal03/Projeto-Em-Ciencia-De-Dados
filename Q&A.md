# Q&A Assíncrono — Defesa do Projeto

**Projeto:** Classificação de Qualidade de Vinhos Verdes
**Equipa:** Afonso Carvalho (2023132594) · Marcello Portugal (2022136899) · Grupo 4
**UC:** Projeto em Ciência de Dados — Profª. Dora Melo
**ISCAC — Coimbra Business School**

Antecipação de três dúvidas técnicas sobre o modelo final (Random Forest, 382 árvores) e respetivas respostas. Cada resposta baseia-se nos resultados documentados em `docs/M3_modelacao.md` e nos notebooks `notebooks/2.0_modelacao_treino.ipynb` e `3.0_interpretacao.ipynb`.

---

## Pergunta 1 — Porquê Random Forest e não XGBoost ou outro algoritmo mais recente?

**Resposta:**

O Random Forest foi selecionado após uma comparação sistemática com três alternativas (Regressão Linear, SVR e XGBoost) usando validação cruzada de 5 partições no conjunto de treino. Os critérios de seleção foram três, por ordem de prioridade: (1) desempenho preditivo, (2) estabilidade entre folds, (3) interpretabilidade.

| Critério | RF | XGBoost | SVR | Reg. Linear |
|---|:---:|:---:|:---:|:---:|
| MAE teste | **0.4319** | 0.4421 | 0.5183 | 0.5827 |
| Desvio CV (5-fold) | **±0.0135** | ±0.0192 | ±0.0214 | ±0.0177 |
| Interpretabilidade (feature importance + PDP) | ✅ nativa | parcial | ❌ caixa-preta | ✅ trivial |

O XGBoost ficou apenas 0.0102 pontos atrás no MAE — diferença residual, **dentro da margem de erro da validação cruzada**. Optámos pelo Random Forest porque:

1. **Maior estabilidade entre folds** (desvio menor) sugere melhor generalização — importante quando temos poucos exemplos extremos.
2. **Importância de variáveis nativa e impurity-based**, complementada por *permutation importance* no notebook 3.0, dá-nos uma narrativa interpretável para a defesa académica.
3. **Menos hiperparâmetros sensíveis** que o XGBoost (que beneficia muito de tuning fino e learning rate); o RF com `n_estimators` e `max_features` capturou quase todo o ganho disponível.

A escolha foi pragmática: ganhámos interpretabilidade e estabilidade ao custo de 1% no MAE.

---

## Pergunta 2 — O MAE de 0.4319 é bom o suficiente? O que significa na prática para o produtor de vinho?

**Resposta:**

O objetivo SMART definido na Milestone 1 era atingir um MAE inferior a 0.5 na escala 0–10 de qualidade sensorial. Atingimos **0.4319 (-13.6% abaixo do limite)**, mas a leitura pragmática é mais rica do que esse único número:

**Distribuição dos erros no conjunto de teste (1.300 vinhos):**

| Tolerância | Vinhos | Percentagem |
|---|:---:|:---:|
| Acerto exato (erro = 0) | 866 | **66.6%** |
| Erro ≤ 1 ponto | 1.167 | 89.8% |
| Erro > 1 ponto | 133 | 10.2% |
| Erro > 1.5 pontos | 34 | 2.6% |

**Tradução prática:**

- O modelo é **útil como ferramenta de triagem inicial** — em ~9 de cada 10 vinhos, a previsão fica a menos de 1 ponto da nota real, suficiente para identificar lotes que merecem inspeção sensorial detalhada.
- Os **erros maiores concentram-se nas notas extremas (3, 4, 8, 9)**, onde o dataset tem poucos exemplos. Para vinhos médios (5–7, que são 96% da produção real), o modelo é particularmente fiável.
- **Não substitui o provador certificado** para decisões finais de classificação comercial. O posicionamento correto é: pré-triagem rápida em laboratório → validação humana nas amostras flagradas pelo modelo.

Resumindo: o MAE de 0.4319 não é "perfeito", mas representa um **ganho de eficiência operacional** real para produtores que façam centenas ou milhares de análises por ano.

---

## Pergunta 3 — Como evitaram overfitting com 382 árvores? E porque é que a variável `is_red` (tipo de vinho) tem importância tão baixa?

**Resposta:**

**Sobre o overfitting:**

As 382 árvores foram escolhidas pelo `RandomizedSearchCV` com 100 combinações de hiperparâmetros e validação cruzada de 5 folds no conjunto de treino. A curva de aprendizagem (`reports/figures/2.0_modelacao/M3_curva_aprendizagem_rf.png`) confirma ausência de overfitting:

- **MAE de treino:** 0.418
- **MAE de validação cruzada:** 0.443
- **MAE de teste (hold-out nunca visto):** 0.4319

A diferença treino–teste é de apenas 0.014 pontos, dentro do desvio padrão da validação cruzada (±0.0135). As três salvaguardas que aplicámos:

1. **`max_features='sqrt'`** — cada divisão considera apenas √12 ≈ 3 variáveis aleatórias, forçando diversidade entre árvores.
2. **Validação cruzada estratificada** — garantiu que o modelo não memorizava padrões específicos de um único split.
3. **Conjunto de teste estritamente separado** (`stratify=quality`, 20% holdout) — nunca tocado durante tuning. O MAE no teste é o "exame final".

O número 382 é alto mas, em Random Forest, **mais árvores reduzem variância sem aumentar enviesamento** — o limite é apenas o custo computacional e os retornos decrescentes.

**Sobre o `is_red` com importância < 0.1%:**

Esta foi a descoberta mais contra-intuitiva do projeto e merece destaque na defesa. Inicialmente esperávamos que a cor do vinho fosse relevante (tintos e brancos têm perfis químicos distintos), mas três análises convergem na mesma conclusão:

1. **Feature importance (impurity-based):** `is_red` aparece em último lugar, com importância de 0.0008.
2. **Permutation importance:** ao permutar aleatoriamente os valores de `is_red`, o MAE não aumenta de forma estatisticamente significativa.
3. **Modelos isolados (tintos vs brancos):** treinámos dois modelos separados, um por tipo. Os MAEs foram 0.45 (tintos) e 0.43 (brancos) — diferença que **não justifica a complexidade adicional** de manter dois pipelines.

**Interpretação química:** as variáveis que realmente determinam a qualidade (álcool, acidez volátil, densidade, SO2) capturam diretamente o impacto da cor — vinhos tintos tendem a ter maior acidez volátil e menor açúcar residual, mas o modelo aprende esses padrões a partir dos próprios atributos químicos, **tornando a variável `is_red` redundante**.

Esta é uma boa ilustração do princípio de *feature engineering*: variáveis aparentemente importantes podem ser totalmente explicadas por outras variáveis mais granulares no dataset.

---

*Documento preparado para o Q&A assíncrono da defesa da Milestone 4 — 2026-05-18.*
