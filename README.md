# Vem cá pra eu te explicar tudo que tá rolando nesse código  😄 😆 😊 😃 ☺️ 

As primeiras 4 linhas são de importações.

Vamos entender o que essa função abaixo faz: Esta função é responsável pela limpeza e preparação dos dados. Ela se encaixa na parte de limpeza/padronização dos dados. Ela filtra os dados, reformula a tabela e combina duas fontes de dados diferentes em uma única tabela.


```python
def prepare_country_stats(oecd_bli, gdp_per_capita): 
    oecd_bli = oecd_bli[oecd_bli["INEQUALITY"]=="TOT"]
    oecd_bli = oecd_bli.pivot(index="Country", columns="Indicator", values="Value")
    gdp_per_capita.rename(columns={"2015": "GDP per capita"}, inplace=True)
    gdp_per_capita.set_index("Country", inplace=True)
    full_country_stats = pd.merge(left=oecd_bli, right=gdp_per_capita,
                                  left_index=True, right_index=True)
    full_country_stats.sort_values(by="GDP per capita", inplace=True)
    remove_indices = [0, 1, 6, 8, 33, 34, 35]
    keep_indices = list(set(range(36)) - set(remove_indices))
    return full_country_stats[["GDP per capita", 'Life satisfaction']].iloc[keep_indices]

```

Agora, esta próxima parte:

```python
import os
datapath = os.path.join("datasets", "lifesat", "")
```

Eu estou apenas definindo o caminho para os datasets que serão utilizados. Basicamente definindo onde os arquivos estão localizados.

```python
%matplotlib inline
import matplotlib as mpl
mpl.rc('axes', labelsize=14)
mpl.rc('xtick', labelsize=12)
mpl.rc('ytick', labelsize=12)
```

Essas linhas de código configuram o ambiente para a visualização de gráficos. O %matplotlib inline é um comando específico do Jupyter Notebook que permite que os gráficos sejam exibidos diretamente no notebook.

```python
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
import sklearn.linear_model


oecd_bli = pd.read_csv(datapath + "oecd_bli_2015.csv", thousands=',')
gdp_per_capita = pd.read_csv(datapath + "gdp_per_capita.csv",thousands=',',delimiter='\t',
                             encoding='latin1', na_values="n/a")
```

Aqui estou importando as bibliotecas necessárias e lendo os dados dos arquivos CSV. Os parâmetros adicionais na leitura dos arquivos ajudam a tratar os dados corretamente, como o thousands=',' que define o separador de milhares e na_values="n/a" que define o que deve ser considerado como valor ausente.

```python

country_stats = prepare_country_stats(oecd_bli, gdp_per_capita)
X = np.c_[country_stats["GDP per capita"]]
y = np.c_[country_stats["Life satisfaction"]]

```

Chamo a função `prepare_country_stats` para preparar os dados e, em seguida, cria as variáveis `X` e `y`, que serão usadas no modelo de regressão linear. X contém os dados de PIB per capita e y contém os dados de satisfação com a vida.


```python
country_stats.plot(kind='scatter', x="GDP per capita", y='Life satisfaction')
plt.show()
```

Criando um gráfico de dispersão para visualizar a relação entre o PIB per capita e a satisfação com a vida.

```python
model = sklearn.linear_model.LinearRegression()
```
Definindo modelo de regressão linear, que será usado para entender a relação entre as variáveis e fazer previsões. (vou explicar melhor isso no final, fica d boas)

```python
model.fit(X, y)

X_new = [[22587]]  # Cyprus' GDP per capita
print(model.predict(X_new)) # outputs [[ 5.96242338]]

```

FINALMENTE!Treino meu modelito com os dados (model.fit(X, y)) e faz uma previsão (model.predict(X_new)) para o PIB per capita de Chipre.( Poderia ser outro mas o querido do Livro que eu to lendo fez com esse testei com outros tbm)

O PIB per capita pode ser interpretado como a “grana que a pessoa tem”, ou seja, é uma medida da riqueza econômica de um país dividida pelo número de habitantes.


## Um pouco bem pouco quase nada sobre regressão linear 🚎


Em um modelo de regressão linear, a variável dependente é aquela que você tenta prever ou explicar, e a variável independente é aquela que você usa para fazer a previsão. No contexto do que estamos fazendo ficaria algo assim:

A variável dependente é a ‘Life satisfaction’ (satisfação com a vida), pois é o que o modelo tentará prever.
A variável independente é o ‘GDP per capita’ (PIB per capita), pois é o preditor que você está usando para explicar as variações na satisfação com a vida.

A ideia é que, ao analisar os dados, podemos usar o PIB per capita para prever a satisfação com a vida das pessoas em diferentes países. O modelo de regressão linear tentará encontrar a melhor linha reta (ou seja, a relação linear) que se ajusta aos dados de PIB per capita e satisfação com a vida, permitindo fazer previsões sobre a satisfação com a vida com base em novos valores de PIB per capita.



### conclusao 

A linha no gráfico é chamada de linha de regressão. Ela é usada para mostrar a tendência geral dos dados. Neste caso, a linha de regressão mostra que, em geral, à medida que o PIB per capita aumenta, a satisfação com a vida também aumenta.

tchauuu ateh o prox capitulo 🚕
