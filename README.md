<h1 align="center">
PREVISÃO DE PREÇOS DE PRODUTOS<br>
NO VAREJO DA MODA
</h1>

<h6>- <a href="README-EN.md">ENGLISH VERSION</a></h6>

![banner](img/DeptoModa.jpg)

# 1. INTRODUÇÃO

Este relatório descreve os detalhes do projeto de Ciência de Dados referente à criação de um **Algoritmo de Regressão** utilizando *machine learning* para previsão dos preços de produtos em uma empresa de e-commerce.

Em resumo, neste projeto fizemos uso das seguintes ferramentas e técnicas:

- **Ferramentas**: VS-Code, Jupyter Notebook, Mini Conda, GitHub, GPT-4
- **Programação & Bibliotecas**: Python, Pandas, Numpy, Scikit-Learn, Matplotlib, Seaborn
- **Modelos de Machine Learning para Regressão**: Regressão Linear, Random Forest, XGBoost, CatBoost, LightGBM, Voting Regressor
- **Modelo de Machine Learning para Clusterização**: K-Means
- **Seleção de Features & Validação**: Boruta, Cross-Validation, Optuna

Os arquivos de dados foram fornecidos pela administração do evento Hackday, contendo 23.309 registros, e estão disponíveis na respectiva página do [Kaggle](https://www.kaggle.com/competitions/product-price-predicition-20/overview). Foram testados cinco algoritmos de aprendizado de máquina (seção 5 do código), e o *Random Forest Regressor* foi selecionado como o modelo final devido ao seu desempenho superior na métrica utilizada (SMAPE - *Symmetric Mean Absolute Percentage Error*).

Os produtos finais podem ser conferidos abaixo na [seção 2.2](#2-o-problema-de-neg%C3%B3cio--o-plano-de-solu%C3%A7%C3%A3o) e na [seção 7](#7-resultados) deste relatório.


# 2. O PROBLEMA DE NEGÓCIO & O PLANO DE SOLUÇÃO

## 2.1. O Problema

Nesse ambiente fictício, a empresa Dados & Decotes atua no e-commerce no ramo de moda masculina e feminina com foco em roupas, calçados e acessórios. O desafio atual da varejista é **otimizar os preços dos seus produtos** de modo a maximizar o lucro nas vendas.

O sistema original de precificação de produtos tem duas etapas. <b>Primeiro</b> é colocada margem fixa de lucro sobre o preço de custo do produto. Na <b>segunda etapa</b>, após 3 meses de avaliação do comportamento das vendas, os preços são ajustados de acordo com as quantidades demandadas de cada produto.

A partir dos estudos a serem realizados pela Equipe de Dados, deseja-se implantar uma **terceira etapa** de precificação, na qual os preços dos produtos novos e recém lançados no mercado serão otimizado com base nas suas características, tais como marca, categoria, tecido e outras.

O fluxograma ilustra o processo atual e a introdução da nova etapa de precificação.

<table align="center">
<tr><td>
<img src="img/EstrategiaPrecificacao.png" align="center">
</td></tr>
</table>

O desafio da Eequipe de Cientistas de Dados é encontrar quais os preços das novas mercadorias, com base no histórico de preços e nas características das mercadorias de maior sucesso em vendas nas duas primeiras etapas de precificação.

## 2.2 Produto Final

São dois os resultados esperados:

1. O desenvolvimento de um procedimento de análise de dados e <i>machine learning</i> projetado para atender as demandas da empresa quanto a previsão de preços de mercadorias, e
2. Elaboração de [insights](#72-os-principais-insights) relacionados ao negócio, a partir da análise dos dados.


## 2.3. Estratégia de Solução

O trabalho foi realizado seguindo o método CRISP-DM<sup>1</sup>, ou "<i>Cross Industry Standard Process for Data Mining</i>", uma abordagem cíclica objetivando aprimorar a qualidade e agilizar a entrega de resultado em projetos de Ciência de Dados. O método pode ser resumido no seguinte conjunto de etapas:
1. Entendimento do negócio
2. Coleta, tratamento e modelagem de dados
3. Algoritmos de <i>Machine Learning</i>
4. Avaliação dos resultados
5. Entrada em produção.

![banner](img/crispy.png)


# 3. COLETA DE DADOS

Conforme apresentado na seção 1 - descrição do problema de negócio, as informações a serem utilizadas no projeto de previsão de preços foram fornecidas pela empresa cliente. O dataset fornecido tem a seguinte estrutura:

<table align="center">
  <tr>
    <th align="center">ATRIBUTO</th>
    <th>DESCRIÇÃO E OBSERVAÇÕES</th>
  </tr>
  <tr>
    <td align="center">_id</td>
    <td>Identificador único da linha.</td>
  </tr>
  <tr>
    <td align="center">pid</td>
    <td>Identificador do produto.</td>
  </tr>
  <tr>
    <td align="center">average_rating</td>
    <td>Avaliação média do produto.</td>
  </tr>
  <tr>
    <td align="center">number_of_reviews</td>
    <td>Número de avaliações do produto.</td>
  </tr>
  <tr>
    <td align="center">brand</td>
    <td>Marca do produto.</td>
  </tr>
  <tr>
    <td align="center">category</td>
    <td>Categoria do produto.</td>
  </tr>
  <tr>
    <td align="center">crawled_at</td>
    <td>Data e hora em que o dado foi coletado no site.</td>
  </tr>
  <tr>
    <td align="center">description</td>
    <td>Descrição do produto.</td>
  </tr>
  <tr>
    <td align="center">images</td>
    <td>URL das imagens do produto na vitrine.</td>
  </tr>
  <tr>
    <td align="center">out_of_stock</td>
    <td>Se o produto encontra-se ou não no estoque.</td>
  </tr>
  <tr>
    <td align="center">avg_time_delivery_days</td>
    <td>Tempo médio de entrega em dias.</td>
  </tr>
  <tr>
    <td align="center">product_details</td>
    <td>Dicionário contendo detalhes do produto (ver seção 4.2)</td>
  </tr>
  <tr>
    <td align="center">seller</td>
    <td>Vendedor do produto.</td>
  </tr>
  <tr>
    <td align="center">sub_category</td>
    <td>Subcategoria do produto.</td>
  </tr>
  <tr>
    <td align="center">fabrication_time</td>
    <td>Tempo de fabricação do produto em dias.</td>
  </tr>
  <tr>
    <td align="center">title</td>
    <td>Título do anúncio do produto.</td>
  </tr>
  <tr>
    <td align="center">actual_price</td>
    <td>Preço alvo (variável de resposta).</td>
  </tr>
</table>


(Fonte: [Kaggle](https://www.kaggle.com/competitions/product-price-predicition-20/overview))


# 4. FEATURE ENGINEERING

Esta etapa tem por objetivo preparar as variáveis (features), bem assim sintetizar novas variáveis, para que estejam disponíveis para a análise exploratória e para todas as demais etapas do desenvolvimento do modelo de previsão.

## 4.1. Transformação da variável alvo

A variável alvo é o preço da mercadoria (ou "actual_price"). O histograma indicou assimetria para a direita. Assim, para tornar a distribuição mais próxima da curva normal, fez-se a transformação logarítmica do preço. O resultado é apresentado na figura.

<table align="center">
<tr><td>
<img src="img/LogTransform.jpg" align="center">
</td></tr>
</table>


## 4.2. Coluna "product_details"

Esta coluna é de grande importância, pois contém diversas **sub-colunas** que informam as características de cada produto. Observa-se que diferentes produtos podem ter diferentes tipos de características. Vejamos alguns exemplos:

<b>Exemplo-1</b>: produto nº 22898
- Título: <b>Slim Men Light Blue Jeans</b>
- Detalhes do produto: {'Style Code': 'D63-D'}, {'Ideal For': 'Men'}, {'Suitable For': 'Western Wear'}, {'Pack Of': '1'}, {'Pocket Type': 'Curved Pocket'}, {'Pattern': 'Chevron'}, {'Reversible': 'No'}, {'Fabric': 'Cotton Polyester Lycra Blend'}, {'Faded': 'Light Fade'}, {'Rise': 'Mid Rise'}, {'Distressed': 'Mild Distress'}, {'Stretchable': 'Yes'}, {'Color': 'Light Blue'}, {'Generic Name': 'Jeans'}, {'Country of Origin': 'India'}

<b>Exemplo-2</b>: produto nº 9459
- Título: <b>ADJUSTER Cap</b>
- Detalhes do produto: {'Fabric': '55% cotton 45 % Polyster'}, {'Color': 'Black'}, {'Style Code': 'DS09BC073K'}, {'Occasion': 'Casual'}, {'Sales Package': '1 Cap'}

<b>Exemplo-3</b>: produto nº 18458
- Título: <b>Slides</b>
- Detalhes do produto: {'Color': 'Black'}, {'Care instructions': 'Dust any dry dirt from the surface using a clean wet cloth or use soap in case of excess dirt, do not use any polish. Store your pair of slippers in a clean place.'}, {'Sole Material': 'PVC'}

Nesses exemplos a característica "Color" aparece nos três produtos. Por outro lado, há várias características que aparecem em apenas um dos produtos, como "Country of Origin", "Suitable For", "Sales Package", "Care instructions", entre outras. Ou seja, esses exemplos mostram que as sub-colunas de "product_detail" contêm informações esparsas acerca dos produtos. Assim, para aprimorar a qualidade da análise, foi necessário converter essas sub-colunas em novas colunas de dados para previsão, e então submetê-las a diversos pré-processamentos.

## 4.3. Outras Colunas e Sub-Colunas

Além do pré-processamento da coluna "product_details" citado acima, os seguintes aprimoramentos foram também implementados dentro do restrito período de 48 horas do evento Hackday:
- As sub-colunas "fabric" (tipo de tecido) e "brand fit" (~forma do produto) foram utilizadas para gerar as colunas binárias "has_cotton", "has_polyester", "has_lycra", "is_regular", "is_slim" e "is_fit".
- A coluna "images" foi utilizada para contar o número de fotografias disponíveis para cada produto.

Posteriormente àquele Hackday, implementamos outros aprimoramentos à etapa de *feature engineering*, resultando em melhoria da acurácia do modelo. As novas técnicas utilizadas foram as seguintes:
- Contagem de Palavras: as colunas "title" e "description" contêm extensos textos livres descritivos das mercadorias. Assim, foi realizada a contagem de todas as palavras que, ranqueadas, foram convertidas em 275 colunas binárias indicativas de quais palavras definem cada produto (seção 2.7 do código). Essa técnica foi utilizada também com outras sub-colunas, como "other details" e "generic name". Com a aplicação dessa técnica, conseguimos reduzir o erro do modelo, de 9,1148 para 8,1864.
- As sub-colunas "pack of" e "number of contents in sales package" tiveram seu conteúdo reunido em uma única coluna, dado conterem informações semelhantes.
- A sub-coluna "size" foi convertida para informação numérica, respeitando uma grade de tamanhos, do tipo XXL, XL, L, M, S, XS, XXS, etc.

## 4.4. Descrição de mercadorias e contagem de palavras

A base de dados contém muitas variáveis em texto, em especial o campo de descrição do produto - "description" - caracterizado como texto livre. Visando a aproveitar essa informação, foi elaborado código para extração e contagem de palavras (seção 2.7 do código). O algoritmo tem a seguinte sequência de passos:

1. Seleção de um subconjunto de colunas que contenham informações informações de texto relevantes para uma análise detalhada. É o caso da coluna "description" e também "title", "OTHER DETAILS", "category", entre outras.

2. Criação de um conjunto de palavras-chave, por iteração e busca nas colunas selecionadas. No processamento, foram encontradas **6.820** palavras únicas.

3. Contagem de palavras, de modo a definir a importância relativa de cada uma como função de sua frequência na base.

4. Filtragem de palavras por ocorrência, de modo a manter no processamento apenas as palavras de maior frequência, consideradas as mais importantes. No presente caso, foram mantidas **275 palavras** de maior frequência.

5. Criação de 275 colunas no dataframe, para representar a ocorrência de cada palavra em cada registro da base.

Com a inclusão dessas 275 novas *features*, foi possível obter expressiva melhoria no desempenho do modelo final de regressão, que teve seu erro SMAPE reduzido de 9,1148 para 8,1864.

<table align="center">
<tr><td>
<img src="img/WordCloud3.png" align="center">
</td></tr>
</table>


## 4.5. Pré-Processamento: codificação de categorias

A base de dados deste projeto se caracteriza por ter uma quantidade expressiva de variáveis categóricas em forma de texto. Como a maioria dos algoritmos de <i>machine learning</i> requerem dados numéricos, foi providenciada sua codificação numérica por meio de "one-hot encoding" e "target encoding".

A codificação do tipo "one-hot encoding" permite criar uma variável binária para cada categoria existente na variável categórica original. No presente projeto, decidiu-se por utilizar essa codificação para a coluna "category", gerando três novas colunas binárias.

A codificação do tipo "target encoding", ou codificação focada no alvo, busca medir o efeito que determinada categoria causaria na variável alvo, ou a probabilidade de o alvo representar uma codificação daquela categoria, conforme explicam Trevisan<sup>2</sup> e Lewinson<sup>5</sup>, pg. 555-556.

No presente projeto, a maioria das variáveis categóricas foram codificadas utilizando o método "target encoding".

## 4.6. Pré-Processamento: K-MEANS para agrupamento de produtos semelhantes

O método K-Means é um algoritmo não-supervisionado de agrupamento (ou clusterização). Há autores que sugerem a utilização desse algoritmo de agrupamento como estratégia não-linear para redução de dimensionalidade, ou ainda para a criação de colunas extras para treinar um outro modelo (ver Géron<sup>4</sup>, pg. 265).

Trazendo a ideia para o presente projeto, durante o pré-processamento, utilizamos o método K-Means para formar grupos de produtos com características semelhantes para depois oferecer esta informação como uma nova coluna para o modelo de previsão de preços. Várias combinações de características dos produtos foram experimentadas de modo a permitir as clusterizações mais eficientes. Ao final, foram aproveitados três agrupamentos distintos, resultando em aumento da precisão do modelo.

## 4.7. Pré-Processamento: seleção de variáveis

Após todo o pré-processamento, fez-se uso do modelo de <i>Random Forest</i> para ranquear as variáveis de acordo com sua capacidade de contribuir no resultado do modelo de previsão. O resultado é apresentado na figura abaixo.

<table align="center">
<tr><td>
<img src="img/RankFeatures.png" align="center">
</td></tr>
</table>


# 5. ANÁLISE EXPLORATÓRIA DE DADOS

A análise exploratória foi dividida em quatro etapas visando a aprofundar o conhecimento sobre os dados e, consequentemente, sobre o negócio.

As etapas foram:
- Análise univariada - para conhecer a distribuição de cada variável;
- Análise bivariada - como cada variável impacta no preço da mercadoria;
- Análise multivariada - busca por correlações entre as variáveis;
- Formulação de hipóteses de negócios.

Como resultado dessa análise, foram observadas algumas características do negócio e seus efeitos no preço das mercadorias. As conclusões estão descritas no [tópico 7.2](#72-os-principais-insights), abaixo.


# 6. ALGORITMOS DE <i>MACHINE LEARNING</i>

O trabalho foi dividido em duas etapas. 
- Etapa-1: identificar o algoritmo que resultasse no modelo de menor erro. Para isso, foram testados seis diferentes algoritmos de <i>machine learning</i>, configurados com os hiperparâmetros em seus valores-padrão.
- Etapa-2: usar o algoritmo eleito na etapa anterior e otimizar seus hiperparâmetros para se chegar ao modelo definitivo.

Na tabela abaixo apresenta-se o resultado da etapa-1 com o desempenho de cada um dos modelos testados.

<table align="center">
  <tr>
    <th align="center">Model</th>
    <th align="right">MAE</th>
    <th align="right">MAPE</th>
    <th align="right">RMSE</th>
    <th align="right">SMAPE</th>
  </tr>
  <tr>
    <td align="center">RandomForest</td>
    <td align="right">170.251782</td>
    <td align="right">0.108400</td>
    <td align="right">391.477521</td>
    <td align="right">10.295032</td>
  </tr>
  <tr>
    <td align="center">VotingRegressor</td>
    <td align="right">192.442082</td>
    <td align="right">0.125997</td>
    <td align="right">380.228307</td>
    <td align="right">12.166790</td>
  </tr>
  <tr>
    <td align="center">XGBoost</td>
    <td align="right">203.984165</td>
    <td align="right">0.133901</td>
    <td align="right">394.197619</td>
    <td align="right">12.941397</td>
  </tr>
  <tr>
    <td align="center">CatBoost</td>
    <td align="right">206.766272</td>
    <td align="right">0.136696</td>
    <td align="right">378.871272</td>
    <td align="right">13.279772</td>
  </tr>
  <tr>
    <td align="center">LightGBM</td>
    <td align="right">220.647464</td>
    <td align="right">0.145251</td>
    <td align="right">411.002568</td>
    <td align="right">14.018622</td>
  </tr>
  <tr>
    <td align="center">LinearRegression</td>
    <td align="right">348.806300</td>
    <td align="right">0.231574</td>
    <td align="right">1004.327188</td>
    <td align="right">22.284824</td>
  </tr>
</table>


Como se pode observar, o algoritmo Random Forest foi o que trouxe o melhor resultado para a métrica sob análise, motivo pelo qual foi o algoritmo utilizado na etapa-2.

- OBS: SMAPE = *Symmetric Mean Absolute Percentage Error*


# 7. RESULTADOS

## 7.1. Desempenho do Modelo de Previsão

O desempenho do modelo durante a etapa de competição Hackday consta no [quadro do Kaggle](https://www.kaggle.com/competitions/product-price-predicition-20/leaderboard), tendo alcançado erro percentual SMAPE de <b>9,11%</b>, conforme figura abaixo.

Em relação a esse resultado, vale ressaltar que, durante a competição, o desempenho do modelo esteve sujeito a duas importantes restrições, a saber, o tempo escasso de menos de 48 horas para desenvolvimento do trabalho e o número limitado de submissões para avaliação. Juntas, essas duas restrições limitaram o aprofundamento das pesquisas. Ainda assim, nossa equipe de cientistas de dados conseguiu o honroso <b>1º Lugar no Certame</b>, tanto no critério de redução do erro, quanto no critério de votação pelos pares.

![banner](img/Hackday6_leaderboard.png)

Em pesquisas posteriores, após encerrada a competição, aplicamos novas rodadas do método CRISP-DM, com revisão da etapa de <i>feature engineering</i>. Com isso, conseguimos melhorar o desempenho do modelo, que alcançou erro percentual SMAPE de <b>8,19%</b>, conforme figura.

<table align="center">
<tr><td>
<img src="img/Hackday6_LateSubmission.png" align="center">
</td></tr>
</table>


## 7.2. Os Principais <i>Insights</i>

### 7.2.1. Relação entre "número de imagens" e "preço do produto"

A análise dos dados indicou que os produtos com mais imagens possuem preços maiores. A partir deste <i>insight</i>, é possível afirmar que uma campanha de aprimoramento das informações visuais dos produtos pode ter impacto positivo na demanda, e consequentemente no preço.

![banner](img/Insight_01.png)

### 7.2.2. Relação entre "número de avaliações" e "preço do produto"

A análise dos dados não indicou haver correlação notável entre essas características.

![banner](img/Insight_02.png)


### 7.2.3. Relação entre "condição do estoque" e "preço do produto"

A análise mostrou uma tendência a terem preços maiores os produtos em falta no estoque. Tal condição pode representar uma estratégia da empresa para administrar o estoque, majorando os preços de produtos que estejam em vias de terem seu estoque zerado.

<table align="center">
<tr><td>
<img src="img/Insight_03.png" align="center">
</td></tr>
</table>


# 8. CONCLUSÃO

No presente trabalho foi analisado um problema de negócio envolvendo empresa do ramo varejista, tendo por objetivo o desenvolvimento de modelo de <i>machine learning</i> para previsão de preços de produtos. Feito o trabalho, dois resultados foram alcançados: (i) um modelo de <i>machine learning</i> com erro menor que 8,2% em ambiente de produção, e (ii) alguns importantes <i>insights</i> quanto ao comportamento do negócio.


# 9. PRÓXIMOS PASSOS

- Implantar otimização do estoque com base no modelo de previsão de preços.
- Aprimorar a etapa de <i>feature engineering</i> com o aprimoramento da seleção de features.
- Implementar técnicas de redução de dimensionalidade, tais como: <i>principal component analysis</i> (PCA), <i>locally linear embedding</i> (LLE) ou <i>autoencoders</i>.

# 10. EQUIPE DE DESENVOLVEDORES

O trabalho foi desenvolvido durante o 6º Hackday da Comunidade DS, competição de Ciência de Dados ocorrida nos dias 21.22/outubro/2023<sup>3</sup>.

Os participantes da equipe <b>MAY THE DATA BE WITH YOU</b> foram:
- Edilson Santos
- Aroldo Brancalhão
- Leonardo Rose
- Manoel Mendonça
- M.Alessandro Fonseca


# 11. REFERÊNCIAS
1. Web: sobre o [Método CRISP-DM](https://www.escoladnc.com.br/blog/data-science/metodologia-crisp-dm/)
2. Artigo: [Target-encoding Categorical Variables](https://towardsdatascience.com/dealing-with-categorical-variables-by-using-target-encoder-a0f1733a4c69), Vinícius Trevisan, 17-março-2022.
3. Site no Kaggle: [6º Hackday CDS - Product Price Prediction](https://www.kaggle.com/competitions/product-price-predicition-20/overview)
4. Livro: "Hands-On Machine Learning with Scikit-Learn, Keras & TensorFlow", Aurélien Géron, 3ª edição, 2023
5. Livro: "Python for Finance Cookbook", Eryk Lewinson, 2ª edição, 2022

