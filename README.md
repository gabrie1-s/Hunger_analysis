# Análise da fome

## Introdução
Este estudo investiga a grave insegurança alimentar no Brasil, onde, em 2022, mais de 21 milhões de indivíduos enfrentaram a fome, resultante da falta de acesso a alimentos seguros e nutritivos. Essa condição tem um impacto adverso na saúde pública, ocasionando déficits cognitivos e atrasos no desenvolvimento de crianças. Através da aplicação de modelos de machine learning e da análise de dados socioeconômicos (CadÚnico) e de dados antropométricos (SISVAN), a pesquisa apresenta uma metodologia para a estimativa de indicadores de insegurança alimentar, contribuindo para a formulação de políticas públicas eficazes, com a possibilidade estensão das análises para níveis submunicipais, como em bairros e comunidades. Buscamos fornecer uma metodologia que oriente decisões governamentais no enfrentamento da fome.

## Metodologia

### Obtensão dos dados
Foram extraídos do [SISVAN](https://sisaps.saude.gov.br/sisvan/relatoriopublico/index) indicadores antropométricos para crianças menores de cinco anos, como baixo peso para altura e idade, em termos percentuais por município. Dados socioeconômicos do CadÚnico foram obtidos via web scraping pela plataforma [CECAD](https://cecad.cidadania.gov.br/tab_cad.php).

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
<td style="text-align: left;">log2, 0.25, sqrt, 1.0</td>
</tr>
<tr class="odd">
<td rowspan="4" style="text-align: center;">GB</td>
<td style="text-align: left;"><em>learning_rate</em></td>
<td style="text-align: left;">0.025, 0.05, 0.1, 0.2, 0.3</td>
</tr>
<tr class="even">
<td style="text-align: left;"><em>max_depth</em></td>
<td style="text-align: left;">2, 3, 5, 7, 10</td>
</tr>
<tr class="odd">
<td style="text-align: left;"><em>max_features</em></td>
<td style="text-align: left;">log2, 0.25, sqrt, 1.0</td>
</tr>
<tr class="even">
<td style="text-align: left;"><em>subsample</em></td>
<td style="text-align: left;">0.15, 0.5, 0.75, 1.0</td>
</tr>
<tr class="odd">
<td rowspan="4" style="text-align: center;">XGBoost</td>
<td style="text-align: left;"><em>learning_rate</em></td>
<td style="text-align: left;">0.025, 0.05, 0.1, 0.2, 0.3</td>
</tr>
<tr class="even">
<td style="text-align: left;"><em>max_depth</em></td>
<td style="text-align: left;">2, 3, 5, 7, 10, 100</td>
</tr>
<tr class="odd">
<td style="text-align: left;"><em>colsample_bylevel</em></td>
<td style="text-align: left;">0.25, 1.0</td>
</tr>
<tr class="even">
<td style="text-align: left;"><em>subsample</em></td>
<td style="text-align: left;">0.15, 0.5, 0.75, 1.0</td>
</tr>
<tr class="odd">
<td rowspan="5" style="text-align: center;">LightGBM</td>
<td style="text-align: left;"><em>learning_rate</em></td>
<td style="text-align: left;">0.025, 0.05, 0.1, 0.2, 0.3</td>
</tr>
<tr class="even">
<td style="text-align: left;"><em>num_leaves</em></td>
<td style="text-align: left;">3, 7, 15, 31, 127, 1024</td>
</tr>
<tr class="odd">
<td style="text-align: left;"><em>top_rate</em></td>
<td style="text-align: left;">0.2, 0.4, 0.6, 0.7</td>
</tr>
<tr class="even">
<td style="text-align: left;"><em>other_rate</em></td>
<td style="text-align: left;">0.05, 0.1, 0.3</td>
</tr>
<tr class="odd">
<td style="text-align: left;"><em>feature_fraction_bynode</em></td>
<td style="text-align: left;">log2, 0.25, sqrt, 1.0</td>
</tr>
<tr class="even">
<td rowspan="4" style="text-align: center;">CatBoost</td>
<td style="text-align: left;"><em>learning_rate</em></td>
<td style="text-align: left;">0.025, 0.05, 0.1, 0.2, 0.3</td>
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
<caption>Desempenho dos modelos na validação cruzada</caption>
<thead>
<thead>
<tr class="header">
<th style="text-align: center;"></th>
<th style="text-align: center;">Baixo peso/idade</th>
<th style="text-align: center;">Baixo peso/altura</th>
<th style="text-align: center;">Baixa altura/idade</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: center;">RF</td>
<td style="text-align: center;"><span class="math inline">$2.3 \pm
0.5$</span></td>
<td style="text-align: center;"><span class="math inline">$2.41 \pm
0.522$</span></td>
<td style="text-align: center;"><span class="math inline">$4.88 \pm
0.53$</span></td>
</tr>
<tr class="even">
<td style="text-align: center;">GB</td>
<td style="text-align: center;"><span class="math inline">$2.33 \pm
0.482$</span></td>
<td style="text-align: center;"><span class="math inline">$2.44 \pm
0.548$</span></td>
<td style="text-align: center;"><span class="math inline">$4.89 \pm
0.52$</span></td>
</tr>
<tr class="odd">
<td style="text-align: center;">XGBoost</td>
<td style="text-align: center;"><span class="math inline">$2.3 \pm
0.48$</span></td>
<td style="text-align: center;"><span class="math inline">$2.4 \pm
0.53$</span></td>
<td style="text-align: center;"><span class="math inline">$4.875 \pm
0.53$</span></td>
</tr>
<tr class="even">
<td style="text-align: center;">CatBoost</td>
<td style="text-align: center;"><span class="math inline">$2.29 \pm
0.482$</span></td>
<td style="text-align: center;"><span class="math inline">$2.39 \pm
0.5$</span></td>
<td style="text-align: center;"><span class="math inline">$4.872 \pm
0.512$</span></td>
</tr>
<tr class="odd">
<td style="text-align: center;">LightGBM</td>
<td style="text-align: center;"><span class="math inline">$2.31 \pm
0.48$</span></td>
<td style="text-align: center;"><span class="math inline">$2.4 \pm
0.5$</span></td>
<td style="text-align: center;"><span class="math inline">$4.86 \pm
0.518$</span></td>
</tr>
</tbody>
</table>
</div>

A tabela acima apresenta o RMSE médio e o desvio padrão dos melhores modelos com os melhores hiperparâmetros testados por validação cruzada. Os resultados indicam um desempenho semelhante entre os modelos, com diferenças dentro do desvio padrão. O CatBoost foi selecionado para as análises subsequentes por apresentar um RMSE ligeiramente inferior para todos os indicadores. Esse modelo foi então treinado com todo o conjunto de treinamento, e os resultados no conjunto de teste estão na tabela abaixo, incluindo as métricas MAE e MAPE para melhorar a interpretabilidade.

<div align="center">
<table>
<caption>Métricas de desempenho do CatBoost no conjunto de teste</caption>
<thead>
<tr class="header">
<th style="text-align: left;"></th>
<th style="text-align: center;">RMSE</th>
<th style="text-align: center;">MAE</th>
<th style="text-align: center;">MAPE</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;">Baixo peso para a idade</td>
<td style="text-align: center;">1.23</td>
<td style="text-align: center;">0.99</td>
<td style="text-align: center;">0.51</td>
</tr>
<tr class="even">
<td style="text-align: left;">Baixo peso para altura</td>
<td style="text-align: center;">2.16</td>
<td style="text-align: center;">1.37</td>
<td style="text-align: center;">0.41</td>
</tr>
<tr class="odd">
<td style="text-align: left;">Baixa altura para a idade</td>
<td style="text-align: center;">3.95</td>
<td style="text-align: center;">2.6</td>
<td style="text-align: center;">0.22</td>
</tr>
</tbody>
</table>
</div>

Também fizemos mapas que mostram os indicadores reais e os preditos para todos os municípios do conjunto de teste (do estado do Ceará).

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
</p>
