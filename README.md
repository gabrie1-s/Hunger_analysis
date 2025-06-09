# Análise da fome e insegurança alimentar

## Introdução
Este estudo investiga a grave insegurança alimentar no Brasil, onde, em 2022, mais de 21 milhões de indivíduos enfrentaram a fome, resultante da falta de acesso a alimentos seguros e nutritivos. Essa condição tem um impacto adverso na saúde pública, ocasionando déficits cognitivos e atrasos no desenvolvimento de crianças. Através da aplicação de modelos de machine learning e da análise de dados socioeconômicos (CadÚnico) e de dados antropométricos (SISVAN), a pesquisa apresenta uma metodologia para a estimativa de indicadores de insegurança alimentar, contribuindo para a formulação de políticas públicas eficazes, com a possibilidade estensão das análises para níveis submunicipais, como em bairros e comunidades. Buscamos fornecer uma metodologia que oriente decisões governamentais no enfrentamento da fome.

## Organição dos arquvos
Os dados extraídos do CECAD e SISVAN se encontram no diretório `data`.

O algoritmo de web scraping usado para coletar os dados do CECAD está em `web_scraping/web_scraping_cadunico.ipynb`.

As implementações dos modelos utilizados estão no diretório `Prediction and Analysis Brazil`.  

## Metodologia

### Obtensão dos dados
Foram extraídos do [SISVAN](https://sisaps.saude.gov.br/sisvan/relatoriopublico/index) indicadores antropométricos para crianças menores de cinco anos, como baixo peso para altura (BPA), baixo peso para a idade (BPI) e baixa altura para a idade (BAI), em termos percentuais por município. Dados socioeconômicos do CadÚnico foram obtidos via web scraping pela plataforma [CECAD](https://cecad.cidadania.gov.br/tab_cad.php).

### Pré-processamento
O conjunto de dados foi dividido em treino e teste, com o estado do Ceará (184 municípios) sendo usado exclusivamente para teste, visando avaliar a generalização do modelo para municípiod de um estado desconhecido. Em seguida, selecionaram-se variáveis relevantes para cada modelo por meio do Boruta-SHAP. Modelos baseados em árvores foram priorizados, devido à sua eficácia em dados tabulares.

### Treinamento e otimização

Modelos de Random Forest (RF), Gradient Boosting (GB), XGBoost, LightGBM e CatBoost foram testados e comparados. Cada modelo foi submetido a uma seleção de hiperparâmetros por busca em grade, avaliada com validação cruzada de 10 folds no conjunto de treinamento, com base na métrica RMSE. A seguir, vê-se a grade de hiperparâmetros

<div align="center">
<table>
<caption>Hiperparâmetros testados na busca em grade</caption>
<thead>
<tr class="header">
<th style="text-align: center;"> </th>
<th style="text-align: left;">Hiperparâmetro</th>
<th style="text-align: left;">Valores da busca em grade</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td rowspan="4" style="text-align: center;">RF</td>
<td style="text-align: left;"><em>max_depth</em></td>
<td style="text-align: left;">5, 8, 10</td>
</tr>
<tr class="even">
<td style="text-align: left;"><em>min_samples_split</em></td>
<td style="text-align: left;">2, 5, 10, 20</td>
</tr>
<tr class="odd">
<td style="text-align: left;"><em>min_samples_leaf</em></td>
<td style="text-align: left;">1, 25, 50, 70</td>
</tr>
<tr class="even">
<td style="text-align: left;"><em>max_features</em></td>
<td style="text-align: left;">log2, 0,25, sqrt, 1,0</td>
</tr>
<tr class="odd">
<td rowspan="4" style="text-align: center;">GB</td>
<td style="text-align: left;"><em>learning_rate</em></td>
<td style="text-align: left;">0.025, 0,05, 0.1, 0,2, 0,3</td>
</tr>
<tr class="even">
<td style="text-align: left;"><em>max_depth</em></td>
<td style="text-align: left;">2, 3, 5, 7, 10</td>
</tr>
<tr class="odd">
<td style="text-align: left;"><em>max_features</em></td>
<td style="text-align: left;">log2, 0,25, sqrt, 1,0</td>
</tr>
<tr class="even">
<td style="text-align: left;"><em>subsample</em></td>
<td style="text-align: left;">0,15, 0,5, 0,75, 1,0</td>
</tr>
<tr class="odd">
<td rowspan="4" style="text-align: center;">XGBoost</td>
<td style="text-align: left;"><em>learning_rate</em></td>
<td style="text-align: left;">0,025, 0,05, 0,1, 0,2, 0,3</td>
</tr>
<tr class="even">
<td style="text-align: left;"><em>max_depth</em></td>
<td style="text-align: left;">2, 3, 5, 7, 10, 100</td>
</tr>
<tr class="odd">
<td style="text-align: left;"><em>colsample_bylevel</em></td>
<td style="text-align: left;">0,25, 1,0</td>
</tr>
<tr class="even">
<td style="text-align: left;"><em>subsample</em></td>
<td style="text-align: left;">0,15, 0,5, 0,75, 1,0</td>
</tr>
<tr class="odd">
<td rowspan="5" style="text-align: center;">LightGBM</td>
<td style="text-align: left;"><em>learning_rate</em></td>
<td style="text-align: left;">0,025, 0,05, 0,1, 0,2, 0,3</td>
</tr>
<tr class="even">
<td style="text-align: left;"><em>num_leaves</em></td>
<td style="text-align: left;">3, 7, 15, 31, 127, 1024</td>
</tr>
<tr class="odd">
<td style="text-align: left;"><em>top_rate</em></td>
<td style="text-align: left;">0,2, 0,4, 0,6, 0,7</td>
</tr>
<tr class="even">
<td style="text-align: left;"><em>other_rate</em></td>
<td style="text-align: left;">0,05, 0,1, 0,3</td>
</tr>
<tr class="odd">
<td style="text-align: left;"><em>feature_fraction_bynode</em></td>
<td style="text-align: left;">log2, 0,25, sqrt, 1,0</td>
</tr>
<tr class="even">
<td rowspan="4" style="text-align: center;">CatBoost</td>
<td style="text-align: left;"><em>learning_rate</em></td>
<td style="text-align: left;">0,025, 0,05, 0,1, 0,2, 0,3</td>
</tr>
<tr class="odd">
<td style="text-align: left;"><em>max_depth</em></td>
<td style="text-align: left;">3, 6, 9</td>
</tr>
<tr class="even">
<td style="text-align: left;"><em>leaf_estimation_iterations</em></td>
<td style="text-align: left;">1, 10</td>
</tr>
<tr class="odd">
<td style="text-align: left;"><em>l2_leaf_reg</em></td>
<td style="text-align: left;">1, 3, 6, 9</td>
</tr>
</tbody>
</table>
</div>

O desempenho dos modelos foi dado por

<div align="center">
<table>
  <caption>Desempenho dos modelos na validação cruzada </caption>
  <thead>
    <tr>
      <th rowspan="2">Modelo</th>
      <th colspan="2">BPI</th>
      <th colspan="2">BPA</th>
      <th colspan="2">BAI</th>
    </tr>
    <tr>
      <th>RMSE</th><th>Nº var.</th>
      <th>RMSE</th><th>Nº var.</th>
      <th>RMSE</th><th>Nº var.</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>RF</td><td>2,302 ± 0,498</td><td>16</td><td>2,406 ± 0,516</td><td>11</td><td>4,880 ± 0,531</td><td>41</td></tr>
    <tr><td>GB</td><td>2,340 ± 0,486</td><td>20</td><td>2,429 ± 0,534</td><td>13</td><td>4,900 ± 0,526</td><td>22</td></tr>
    <tr><td>XGBoost</td><td>2,304 ± 0,488</td><td>18</td><td>2,405 ± 0,527</td><td>6</td><td>4,875 ± 0,530</td><td>18</td></tr>
    <tr><td>CatBoost</td><td>2,295 ± 0,477</td><td>16</td><td>2,409 ± 0,538</td><td>16</td><td>4,888 ± 0,510</td><td>26</td></tr>
    <tr><td>LightGBM</td><td>2,306 ± 0,482</td><td>8</td><td>2,398 ± 0,502</td><td>5</td><td>4,888 ± 0,525</td><td>24</td></tr>
  </tbody>
</table>
</div>


A tabela acima apresenta o RMSE médio e o desvio padrão (após o $\pm$) dos melhores modelos com os melhores hiperparâmetros testados por validação cruzada. Os resultados indicam um desempenho semelhante entre os modelos, com diferenças dentro do desvio padrão. Foi escolhido, portanto, para cada indicador, o modelo que utilizou menos variáveis na validação cruzada. Em outras palavras, escolheu-se o LightGBM para os indicadores BPI e BPA, e o XGBoost para o indicador BAI. Esses modelos foram, então, treinados com todo o conjunto de treinamento, e os resultados no conjunto de teste estão na tabela abaixo, incluindo as métricas MAE e MAPE para melhorar a interpretabilidade.

<div align="center">
  <table>
    <caption>Métricas de desempenho no conjunto de teste dos modelos selecionados por validação cruzada</caption>
    <thead>
      <tr>
        <th></th>
        <th>RMSE</th>
        <th>MAE</th>
        <th>MAPE</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>BPI (LigthGBM)</td>
        <td>1,32</td>
        <td>1,1</td>
        <td>0,57</td>
      </tr>
      <tr>
        <td>BPA (LigthGBM)</td>
        <td>2,22</td>
        <td>1,45</td>
        <td>0,43</td>
      </tr>
      <tr>
        <td>BAI (XGBoost)</td>
        <td>3,93</td>
        <td>2,6</td>
        <td>0,22</td>
      </tr>
    </tbody>
  </table>
</div>

<!-- Também fizemos mapas que mostram os indicadores reais e os preditos para todos os municípios do conjunto de teste (do estado do Ceará).

<p align="center">
  <b>Imagem com valores reais (esquerda) e preditos (direita) para o baixo peso para a altura</b><br>
  <img src="imgs/peso_altura_real.png" alt="Valores Reais - Baixo Peso para Altura" width="400"/>
  <img src="imgs/peso_altura_pred.png" alt="Valores Preditos - Baixo Peso para Altura" width="400"/>
</p>

<p align="center">
  <b>Imagem com valores reais (esquerda) e preditos (direita) para a baixa estatura para a idade</b><br>
  <img src="imgs/altura_idade_real.png" alt="Valores Reais - Baixa Estatura para Idade" width="400"/>
  <img src="imgs/altura_idade_pred.png" alt="Valores Preditos - Baixa Estatura para Idade" width="400"/>
</p>

<p align="center">
  <b>Imagem com valores reais (esquerda) e preditos (direita) para o baixo peso para a idade</b><br>
  <img src="imgs/peso_idade_real.png" alt="Valores Reais - Baixo Peso para Idade" width="400"/>
  <img src="imgs/peso_idade_pred.png" alt="Valores Preditos - Baixo Peso para Idade" width="400"/>
</p> -->
