### Atualização do Dicionário de Dados: Pré-Processamento

Durante a fase de pré-processamento e preparação dos dados, o dataset foi atualizado com a seguinte modificação:

* **`is_red` (Encoding):** Variável numérica binária criada para substituir a variável categórica original referente ao tipo de vinho (`1` = Tinto, `0` = Branco).
* Esta padronização assegura que todas as variáveis tenham o mesmo peso no treino do algoritmo, evitando que o modelo seja prejudicado pelas diferenças de escala entre os atributos.
