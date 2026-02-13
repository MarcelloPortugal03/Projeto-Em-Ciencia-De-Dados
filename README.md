# Classificação de Qualidade de Vinhos (Indústria Alimentar)
## Identificação da Equipa
* **Grupo nº:** 4
* **Membros:**
 * Afonso Carvalho - a2023132594
 * Marcello Portugal - 2022136899

## Organização do Repositório
A estrutura deste projeto segue as boas práticas de Ciência de Dados e Engenharia de Software:
* **`data/`**: Armazenamento de dados (dados brutos em `raw/` e processados em `processed/`).
* **`docs/`**: Documentação técnica detalhada dividida por Milestones (M1, M2 e M3).
* **`notebooks/`**: Jupyter Notebooks para experimentação, limpeza e modelação.
* * **`src/`**: Código-fonte modular (scripts `.py`) para funções reutilizáveis.
* **`reports/`**: Relatórios finais, apresentações e exportação de figuras (`figures/`).
* **`requirements.txt`**: Ficheiro de configuração com as bibliotecas necessárias.
## 1. Iniciação (Milestone 1)
### Contexto e Problema de Negócio
A forma tradicional de avaliar a qualidade de um vinho depende muito de provas sensoriais feitas por especialistas. O problema é que este processo é subjetivo (o que agrada a um especialista pode não agradar a outro) e tem um custo elevado para os produtores. O objetivo deste projeto é perceber se conseguimos prever a qualidade de um vinho (neste caso, o Vinho Verde português) utilizando apenas dados laboratoriais.

A relevância deste desafio reside na procura por um padrão de qualidade que seja constante. Na produção em larga escala, as variações químicas naturais podem alterar o perfil do vinho de lote para lote. Ao conseguirmos correlacionar estas métricas (como o pH, a densidade ou os sulfatos) com a aceitação do consumidor final, permitimos que a empresa tome decisões mais seguras antes mesmo do vinho chegar à garrafa.

No fundo, trata-se de criar uma ferramenta de apoio à decisão que ajude a garantir a qualidade dos vinhos, otimize os custos de prova e permita uma resposta mais rápida às exigências do mercado, transformando dados químicos complexos em informação estratégica para o negócio.
### Objetivos do Projeto
* **Objetivo 1:** Desenvolver um modelo preditivo capaz de estimar a pontuação de qualidade do vinho (tinto e branco) com base nos seus componentes químicos.
* **Objetivo 2:** Identificar quais são os principais fatores físico-químicos (ex: Álcool, Acidez Volátil) que mais influenciam positiva ou negativamente a classificação final da qualidade.
* **Objetivo 3:** Analisar as diferenças estruturais entre as variantes tinto e branco para perceber se os padrões de qualidade se mantêm consistentes entre ambos.
### Fonte de Dados
* **Dataset:** https://archive.ics.uci.edu/dataset/186/wine+quality
* **Dimensão:** Total de linhas 6.497 / Total de colunas 12
  
    Vinho Tinto: 1.599 instâncias (linhas).
  
    Vinho Branco: 4.898 instâncias (linhas).

    Atributos: 12 colunas em ambos os ficheiros (11 variáveis físico-químicas de entrada + 1 variável de saída sensorial).
## 2. Exploração (Milestone 2)
### Limpeza e Preparação
* [Breve resumo das ações de limpeza tomadas. Detalhes em `docs/M2_exploracao.md`]
### Principais Conclusões (EDA)
> *Dica: Insere aqui o gráfico mais importante do projeto.*
* **Ponto-chave:** [Ex: Identificámos que o fator X influencia em 40% o resultado Y, por aplicação
do método ganho de informação]
## 3. Modelação (Milestone 3)
### Abordagem Técnica
* **Modelos:** [Ex: Random Forest e XGBoost]
* **Métrica Principal:** [Ex: F1-Score ou RMSE]
## 4. Finalização (Milestone 4)
### Resposta ao Problema
[Resumo da solução e como ela gera valor para o negócio.]
### Recomendações de Inovação
1. [Sugestão prática baseada nos resultados]
## Como Reproduzir este Projeto
1. Clone o repositório: `git clone [url-do-repo]`
2. Instale as dependências: `pip install -r requirements.txt`
3. Execute os notebooks na pasta `notebooks/` seguindo a ordem numérica.
**Instituição:** Coimbra Business School | ISCAC
**Curso:** Licenciatura em Ciência de Dados para a Gestão
**Unidade Curricular:** Projeto em Ciência de Dados
**Professor Responsável:** Dora Melo (dmelo@iscac.pt) 
  
