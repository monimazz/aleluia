---
date: "2020-08-07"
author: monimazz, isapi
diagram: true
draft: true
image:
  caption: 
  placement: 3
math: true
title: O que é a linguagem R? O beabá para iniciantes
---

#### Tópicos desse post
+ O que é a linguagem R?
+ Como a ciência de dados funciona no R?
+ Baixando e instalando o R
+ O maravilhoso mundo do R
+ Primeiro projetinho no R: Visualizando dados

Se você gosta de desvendar problemas, compreendendo quais os principais motivos dele estar acontecendo e quais as chances de acontecer em outros momentos futuros, você pode assim como nós estar com um pézinho na ciência de dados. A ciência de dados pode ser definida como:

> Uma disciplina que permite você transformar uma base de dados em informação, insights e conhecimento.

Com ela você pode analisar dados pensando no passado, presente e futuro, se perguntando o motivo de alguma situação como e por que ela ocorre, se existe um padrão e muito mais? Indo além e podendo usar machine learning :rocket: nas suas análises.

Para se tornar um cientista de dados existe um longo caminho, que ainda estou percorrendo, com diversas linguagens e ferramentas que irão ajudar nesse processo, como big data, python, estatística e muito mais, como se pode ver na imagem abaixo de Swami Chandrasekaran:
 
<img src="/img/roadmap2.png">

Dentre todo esse redemoinho de informações, acredito que a melhor linguagem para começar a aprender, colocar a mão na massa e já ter resultados legais é o R, altamente usado para ciência de dados. Mas o que é a linguagem R?

> R é uma ferramenta gratuita e open-source (ou seja, de código aberto) usada para análise de dados estatísticos e visualização da informação, muito utilizada por cientistas de dados e pesquisadores. https://www.r-project.org/

## E por que o R é tão legal?
Além do seu aprendizado ser intuitivo e possuir uma comunidade super aberta para troca de conhecimento, o R é uma ferramenta que agrega 3 elementos essenciais em um só programa: manipulação de dados, cálculos e display gráfico. Com ele você consegue fazer: 
+ Aplicativos web
+ Gráficos
+ Dashboards
+ Artigos acadêmicos
+ Blogs (esse aqui é todo feito no R)
+ Livros
+ Analisar dados de texto quantitativamente
+ Machine learning
+ Slides
+ Minerar dados
+ e mais!...

## Como a ciência de dados funciona no R?

<img src="/img/roadmap2.png">

Essa imagem representa o fluxo de trabalho que normalmente você realiza ao trabalhar com projetos no R, a ciência de dados funciona no R de acordo com essas características. 
+ Subir a base de dados
+ organizar a base de dados, fazer uma limpeza dela
+ Transformação
+ Visualização: gráficos
+ Modelagem.
+ Comunicação: juntar tudo o que foi feito e transmitir informação

## Baixando e instalando o R
Para começar você precisa baixar dois programas:
+  R: para Windows e Mac - O R raíz
+  Rstudio (link) - A IDE (Integrated development environment) que você sempre vai utilizar. Todos os trabalhos, análises - e literalmente TUDO - vão ser no Rstudio, ok? 

Essa é a cara do Rstudio:

<img src="/img/rstudio.png">

+ **Editor:** você pode criar scripts, documentos e outros, basicamente lá é onde você irá salvar os seus códigos em uma pasta escolhida no seu computador. Para fazer o seu código “rodar”, você pode selecionar o código ou linha e apertar “RUN” ou na última linha do código que você quer fazer rodar apertar o comando: CTRL+ENTER
+ **Console:** Local que o seu código roda, tanto o que você fez no editor ou que insere  diretamente no console.
+ **Output:** em Plots ou Viewer você poderá ver o resultado visual do seus gráficos. Files são as pastas do seu computador.
+ **Global Enviroment:** Todos os objetos que você criar ficarão aqui


### Tipo de dados que você trabalhará no R
+ **integer:** números inteiros
+ **numeric:** números decimais 
+ **complex:** números complexos
+ **logical:** FALSE, TRUE, NA
+ **factor:** categórica ex: (“ótimo”, “bom”, “médio”, “ruim”)
+ **character:**  texto ex: (“eu amo bolo de chocolate”)


## Exercício inicial: Olá Mundo!
No seu console do R digite `oi <- "oi mundo!"`. Esse código indica que você está atribuindo  valor  `<-` ao objeto `oi`, que acabou de criar, no caso, atribuindo a variável character “oi mundo”. Se você quiser ver esse objeto é só digitar no console:`oi` 


### Principais atalhos no Rstudio que vão te ajudar!
+ **CTRL+ENTER:** roda a linha selecionada do script.
+ **ALT+-:** (<-) atribuir variável.
+ **ALT+SHIFT+K:** janela com todos os atalhos disponíveis.


## O maravilhoso mundo do R
Alguns exemplos desse novo mundo
_(literalmente, copie e cole os códigos abaixo e veja a mágica acontecer)_

Antes de tudo, precisamos instalar alguns pacotes do R. Mas o que raios são pacotes? Os pacotes são grande parte de onde está a mágica do R, eles são definidos como:
> Packages are the fundamental units of reproducible R code.  
Mas eu prefiro entendê-los como se fossem caixas de ferramentas, que dentro há ferramentas que vão me ajudar a implementar o meu projeto. Há uma infinidade de pacotes (library), criadas diariamente por pessoas de todo o mundo (vantagens do open-source), cada uma dessas caixas, possuindo ferramentas diferentes! (quem sabe uma imagem de uma caixa de ferramentas?) 


```r
install.packages(“tidyverse”)
library(tidyverse) # o pacote mãe, soberano, é uma coleção de pacotes para data science
library(ggplot2) # gráficos
library(geobr) # mapas br
library(sf) # mapas
library(dplyr) # manipulação de dados
library(modelr)
library(gapminder) # banco de dados
library(lubridate) # processamento de datas
library(hexbin) # gráficos hexagonais
library(ggcorrplot) # correlação de dados
```


# Ex 1: scatterplot (o famoso gráfico de dispersão)
```r
mpg %>% ggplot(aes(displ, hwy, color = class)) +
  geom_point()
```


# Ex 2: gráfico de barras
```
diamonds %>% ggplot(aes(cut)) +
  geom_bar()
```


# Ex 3: mapas
no_axis <- theme(axis.title=element_blank(),
                 axis.text=element_blank(),
                 axis.ticks=element_blank())
states <- read_state(year=2014)
ggplot() +
  geom_sf(data=states, fill="#2D3E50", color="#FEBF57", size=.15, show.legend = FALSE) +
  labs(subtitle="States", size=8) +
  theme_minimal() +
  no_axis


# Ex 4: gráfico de correlação
diamonds %>% select(5:10) %>%
  cor() %>%
  ggcorrplot(hc.order = TRUE, type = "lower",
             outline.col = "white",
             ggtheme = ggplot2::theme_gray,
             colors = c("#6D9EC1", "white", "#E46726"))


# Ex 5: gráfico de hexágonos
ggplot(diamonds, aes(carat, price)) + 
  geom_hex(bins = 50)


Primeiro projetinho no R: Visualizando dados

# PARTE 2: EXPLORANDO ALGUMAS BASES DE DADOS 

# CASO MPG
# Rode os códigos abaixo para entender o que é essa base de dados:
mpg
?mpg

# PERGUNTA da análise: 
# Será que existe uma relação entre displ (tamanho do motor) e hwy (eficiência do combustível)?
# Por esse gráfico que vamos rodar agora, parece que sim.
mpg %>% ggplot(mapping = aes(x = displ, y = hwy)) +
  geom_point()

# Podemos adicionar uma linha ao gráfico a fim de entendermos melhor o lugar em que os pontos estão posicionados
mpg %>% ggplot(mapping = aes(x = displ, y = hwy)) +
  geom_point() +
  geom_smooth(se = FALSE) # linha posicionada no lugar mais perto de todos os pontos

# Podemos também adicionar mais uma variável: class. Pra isso, nos utilizaremos de cores pra adicionar essa informação. Eu decidi usar cores, mas note que poderia ser qualquer outra coisa, como formas ou tamanho dos pontos. Para isso, troque o argumento “color” por “shape” ou “size”.
mpg %>% ggplot(mapping = aes(x = displ, y = hwy, color = class)) +
  geom_point()

# Agora vamos dividir nosso gráfico. Rode o código abaixo e você verá que agora temos 7 gráficos! Cada um deles é específico de uma “class” de carros. Ou seja, ao invés de adicionarmos essa variável “class” através de cores, formatos, etc, estamos separando cada uma em um gráfico diferente. 
mpg %>% ggplot(mapping = aes(x = displ, y = hwy)) +
  geom_point() +
  facet_wrap(facets = vars(class))



Quer mais conteúdo para se aprofundar? Aqui vão alguns conteúdos ótimos em português:

R para cientistas sociais
Github R ladies SP com diversos workshops
Github R ladies Goiania

Para quem se aventura no inglês, segue o livro que eu leio vira e mexe volto pra ele em momentos de dúvidas, uma fonte de aprendizado ótima: https://r4ds.had.co.nz/

Outro que tem sido meu guia principal em visualização de dados é o (Data to viz)[https://www.data-to-viz.com/]

Fique ligado nos eventos das R-ladies! Há sempre tutoriais e workshops super legais e gratuitos para todos! Há diversos R-ladies no mundo, aqui vão algumas dicas de perfis que eu sigo no instagram e github, recomendo!
@rladiesp
@rladiesthe
@rladiesbh


R-ladies é uma iniciativa global que promove diversidade na comunidade R, realizando diversos compartilhamento de conteúdo, workshops, mentorias, eventos e conexões! Quem fundou essa comunidade GLOBAL foi a Gabriela Queiroz, brasileira e cientista de dados, lá em 2012, que acabou tendo muito sucesso e expandido para várias regiões do mundo.https://gqueiroz.shinyapps.io/rshinylady/
