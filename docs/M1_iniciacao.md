# Milestone 1: Iniciação e Definição do Projeto
## 1. Descrição Detalhada do Problema
O projeto insere-se no setor da Indústria Vinícola, especificamente na produção de Vinho Verde no norte de Portugal. A qualidade do vinho é tradicionalmente avaliada através de testes sensoriais (provas) realizados por especialistas humanos. No entanto, este método apresenta desafios críticos: é subjetivo, dispendioso, lento e sujeito a inconsistências individuais.

No cenário atual de produção em larga escala e exportação global, a manutenção de um padrão de qualidade rigoroso é fundamental para a competitividade das empresas. A relevância deste problema reside na necessidade de digitalizar o controlo de qualidade. Ao correlacionar variáveis físico-químicas (como acidez volátil, cloretos e álcool) com as pontuações de qualidade, é possível criar um sistema de apoio à decisão que:

- Antecipe a classificação do vinho antes da etapa sensorial.
- Detete anomalias químicas que possam comprometer lotes inteiros.
- Otimize os custos operacionais ao reduzir a dependência exclusiva de provadores para todas as fases da produção.

Desta forma, o problema deixa de ser apenas uma questão de paladar e passa a ser uma questão de análise preditiva de dados laboratoriais para garantir a consistência do produto final.
## 2. Objetivos SMART
Desenvolver um modelo de Machine Learning com métricas de regressão para prever a qualidade sensorial do Vinho Verde (escala 0-10) com base em 11 variáveis físico-químicas, atingindo um erro médio absoluto (MAE) inferior a 0.5 pontos até ao final da Milestone 3 deste projeto.
## 3. Metodologia de Gestão (PBL)
 * **Afonso Carvalho:** Responsável pela Engenharia de Dados e Visualização.
 * **Marcello Portugal:** Responsável pela Modelação Estatística e Documentação.
* **Ferramentas de Colaboração:** Utilizamos o GitHub Projects (Kanban), o Kaggle (código) e o Discord/WhatsApp para a comunicação diária do projeto.
## 4. Análise de Viabilidade dos Dados
* **Disponibilidade:** Os dados foram obtidos através do repositório oficial da UCI e a sua integração foi validada com sucesso no ambiente de trabalho.
* **Qualidade Inicial:** O dataset é robusto e apresenta-se bem estruturado, com uma ausência total de valores nulos. As variáveis físico-químicas apresentam intervalos de valores consistentes com a realidade, e a amostra de 6.497 registos garante o volume de dados necessário para uma modelação estatística fiável e representativa.
* **Ética e Citação:** O projeto cumpre as normas de proteção de dados e privacidade, uma vez que apenas estão disponíveis variáveis físico-químicas e sensoriais, sem qualquer informação sobre marcas ou produtores. Conforme solicitado pelos autores (P. Cortez et al., 2009), a utilização destes dados para fins de investigação será devidamente creditada através da citação do artigo original publicado na revista Decision Support Systems.
## 5. Cronograma Interno
| Fase | Data Limite | Entregável Esperado |
| :--- | :--- | :--- |
| M1: Iniciação | [Data] | Repositório estruturado e Plano de Projeto. |
| M2: Exploração | [Data] | Notebook de EDA e Dados Processados. |
| M3: Modelação | [Data] | Comparação de algoritmos e métricas. |
| M4: Finalização| [Data] | Pitch e Relatório Final. |
---
*Data de última atualização: [DD/MM/AAAA]*
