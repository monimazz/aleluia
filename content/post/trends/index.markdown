---
title: Como a pandemia afetou as relações interpessoais? Analisando dados do Google Trends no R
author: monimazz
date: '2020-09-06'
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: "Smart"
  preview_only: true
slug: google-trends-no-r-todo-mundo-mente
categories:
  - Tutorial
tags:
  - tutorial
  - googletrends
subtitle: ''
summary: ''
authors: []
lastmod: '2020-09-06T23:56:50Z'
projects: []
---

**O Google é hoje a maior base de dados sobre o comportamento humano**, conseguindo refletir nossos desejos, medos, gostos, angustias, dúvidas e mais, só é necessário fazer as perguntas certas. Isto é o defende Seth Stephens-Davidowitz no livro "Todo Mundo Mente", explicando que:

> "O poder do Google está no fato de as pessoas "contarem" ao gigantesco mecanismo de busca o que não diriam a mais ninguém. [...] As buscas do google são o conjunto de dados mais importante jamais coletado sobre a pisque humana.

### Por que isso acontece?

Devido ao anonimato das buscas, o google parece um grande confidente ou confessionário, assim, as pessoas não sentem a pressão ou julgamento em relação a um assunto, algo que poderiam ter em uma pesquisa ou em uma conversa com um amigo, parente, levando-as a mentir, até para sí próprias. 

No livro, Davidowitz apresenta um estudo super interessante que fez utilizando o Google Trends, analisando buscas realizadas por pessoas racistas, construindo assim um mapa dos locais mais racistas nos Estados Unidos. Este mapa é muito similar as regiões que elegeram Trump em 2016.

![](https://www.washingtonpost.com/wp-apps/imrs.php?src=https://arc-anglerfish-washpost-prod-washpost.s3.amazonaws.com/public/3276NB3DTQ6KBEYT5AFX7KW33U.png&w=916)


Dessa forma, inspirada no livro e porque recentemente foi Dia do Sexo (06/09), fiquei me perguntando: o que o brasileiro busca no google em relação a sexo? quais têm sido as tendências desde o início da pandemia? Resolvi escolher esse tema justamente pelo tabu social envolvido e ser algo extremamente intímo, potencialmente revelando algo interessante sobre o comportamento do brasileiro no assunto que pouco sabemos, justamente pelo tabu do assunto. 

Para isso, vou utilizar o pacote `gtrends`, que permite capturar informações do Google Trends pelo R e fazer análises! Para mais informações do pacote, acessar o CRAN: [gtrends](https://cran.r-project.org/web/packages/gtrendsR/gtrendsR.pdf). Se você quiser explorar mais o pacote, tem um site que eu acompanho que fez uma intro super fácil, recomendo acessar o [post](http://data7.blog/gtrendsr-google-trends-r/).

Primeiro é necessário baixar o pacote e as librays que vão ser utilizadas.


```r
# Instalar o pacote gtrendsR
devtools::install_github('PMassicotte/gtrendsR')
```



```r
#Carregar os pacotes
library(gtrendsR)
library(tidyverse)
```

### Buscar a informação

Primeiro específicamos as palavras ou palavaras, depois o localização que queremos, no caso Brasil e o tempo de busca. Resolvi procurar de forma suuuper genérica mesmo, só o termo, não necessáriamente é a "pergunta correta"

```r
resultados_all <- gtrends(c("sexo"), geo = "BR", time = "all")

resultados_tres <- gtrends("sexo", geo = "BR", time = "2020-03-01 2020-09-07")
```

A partir dessa busca com um termo amplo, vou analisar quais os tópicos e buscas relacionadas, comparando os últimos 3 meses com o período atual, houve aumento de alguma busca? Quais os termos?

Vale ressaltar que tanto em tópicos quanto em buscas para realizar o gráfico estou excluindo os assuntos em ascensão e deixando somente o que está como principais buscas relaciondas, no entanto ainda dando destaque os que estão em ascensão por meio de uma tabela, por possuírem uma escala diferente que não vai de 0 a 100.

### Topicos relacionados

```r
topic_all <- resultados_all$related_topics
topic_all <- topic_all %>% mutate(tipo = "2004-hoje")

topic_tres <- resultados_tres$related_topics
topic_tres <- topic_tres %>% mutate(tipo = "Março (01) - Setembro (07)")

topic <- rbind(topic_all, topic_tres)

topic %>%
  mutate(topics = case_when(value == "Video" ~ "Vídeo",
                            value == "Scene" ~ "Cena",
                            value == "Property caretaker" ~ "Caseiro - Profissão",
                            value == "Position" ~ "Posição",
                            value == "GIF" ~ "GIF",
                            value == "Crown" ~ "Coroa",
                            value == "couple" ~ "Casal",
                            value == "Amateur" ~ "Amador",
                            value == "Virtual sex" ~ "Sexo virtual", TRUE ~ "Outros")
  ) %>%
  filter(related_topics != "rising") %>%
  mutate(peso = as.numeric(subject)) %>%
  ggplot(aes(x=topics, y=peso)) +
  geom_segment( aes(xend=topics, yend=0)) +
  geom_point( size=4, color="purple") +
  facet_grid(cols = vars(tipo)) +
  coord_flip()+
  theme_minimal() +
  labs(x= "", y= "", title = "Principais tópicos relacionados")
```

<img src="/post/trends/index_files/figure-html/unnamed-chunk-4-1.png" width="672" />
Os tópicos relacionados não tiveram grande mudança, no entanto, em vista de tempos de isolamento social **GIF** apareceu como um tópico novo, será é uma nova forma das pessoas continuarem suas relações e se comunicando? Vou explorar esse assunto. Casal (couple) também é um termo que está como um dos principais nas buscas nesse período de quarentena.

Explorando mais o contexto de isolamento social, é possível perceber um grande aumento de buscas relacionadas a "sexo virtual", justamente no período de maior isolamento no país


```r
result2_all <- gtrends(c("sexo virtual"), geo = "BR", time = "today 12-m", cookie_url = "http://trends.google.com/Cookies/NID",   onlyInterest = TRUE)

result2_all$interest_over_time %>%
  ggplot(aes(x=date, y=hits)) +
  geom_line() +
  theme_minimal() +
  labs(x= "", y= "", title = "Buscas nos ultimos 12 meses")
```

<img src="/post/trends/index_files/figure-html/unnamed-chunk-5-1.png" width="672" />

### Buscas relacionadas


```r
search_all <- resultados_all$related_queries
search_all <- search_all %>% mutate(tipo = "2004-hoje")

search_tres <- resultados_tres$related_queries
search_tres <- search_tres %>% mutate(tipo = "Março (01) - Setembro (06)")

search <- rbind(search_all, search_tres)

search %>%
  filter(related_queries != "rising") %>%
  mutate(peso = as.numeric(subject)) %>%
  ggplot(aes(x=value, y=peso)) +
  geom_segment( aes(xend=value, yend=0)) +
  geom_point( size=4, color="purple") +
  facet_grid(cols = vars(tipo)) +
  coord_flip()+
  theme_minimal() +
  labs(x= "", y= "", title = "Principais buscas relacionadas")
```

<img src="/post/trends/index_files/figure-html/unnamed-chunk-6-1.png" width="672" />

Algo interessante de notar é o aumento durante o período da pandemia que houve um aumento nas buscas sobre "sonhar com sexo", será que os desejos das pessoas e o isolamento está levando que isso ocorra com maior frequencia?


#### GIFS, quais as buscas relacionadas?

Olhando somente os termos em ascensão de busca, nota-se que a relação do uso de GIFS sexuais, possivelmente uma forma que passou a ser mais amplamente utilizada durante o período de isolamento para se comunicar em relação a sexo. 


```r
library(kableExtra)
library(knitr)
gif <- gtrends("GIF", geo = "BR", time = "all", cookie_url = "http://trends.google.com/Cookies/NID")

gif$related_queries %>% filter(related_queries == "rising") %>% 
  kable() %>%
  row_spec(c(6, 11, 18, 20), bold = T, color = "white",background = "#D7261E" )
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> subject </th>
   <th style="text-align:left;"> related_queries </th>
   <th style="text-align:left;"> value </th>
   <th style="text-align:left;"> geo </th>
   <th style="text-align:left;"> keyword </th>
   <th style="text-align:right;"> category </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Breakout </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> gif tumblr </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Breakout </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> bom dia gif </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Breakout </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> boa noite gif </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Breakout </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> gif whatsapp </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Breakout </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> gif facebook </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: #D7261E !important;"> Breakout </td>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: #D7261E !important;"> rising </td>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: #D7261E !important;"> gif de bom dia </td>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: #D7261E !important;"> BR </td>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: #D7261E !important;"> GIF </td>
   <td style="text-align:right;font-weight: bold;color: white !important;background-color: #D7261E !important;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Breakout </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> gif de boa noite </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Breakout </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> gif online </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Breakout </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> gif instagram </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Breakout </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> gif para whatsapp </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: #D7261E !important;"> Breakout </td>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: #D7261E !important;"> rising </td>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: #D7261E !important;"> gif aniversario </td>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: #D7261E !important;"> BR </td>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: #D7261E !important;"> GIF </td>
   <td style="text-align:right;font-weight: bold;color: white !important;background-color: #D7261E !important;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Breakout </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> gif oral </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Breakout </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> gretchen gif </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Breakout </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> gif sexy </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Breakout </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> gif orkut </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Breakout </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> naruto gif </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Breakout </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> como criar gif </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: #D7261E !important;"> Breakout </td>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: #D7261E !important;"> rising </td>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: #D7261E !important;"> boa tarde gif </td>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: #D7261E !important;"> BR </td>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: #D7261E !important;"> GIF </td>
   <td style="text-align:right;font-weight: bold;color: white !important;background-color: #D7261E !important;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Breakout </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> gif no whatsapp </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: #D7261E !important;"> Breakout </td>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: #D7261E !important;"> rising </td>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: #D7261E !important;"> gif para tumblr </td>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: #D7261E !important;"> BR </td>
   <td style="text-align:left;font-weight: bold;color: white !important;background-color: #D7261E !important;"> GIF </td>
   <td style="text-align:right;font-weight: bold;color: white !important;background-color: #D7261E !important;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Breakout </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> como salvar gif </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Breakout </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> como baixar gif </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Breakout </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> gif engraÃ§ado </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Breakout </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> gif para orkut </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> +850% </td>
   <td style="text-align:left;"> rising </td>
   <td style="text-align:left;"> gif gay </td>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:left;"> GIF </td>
   <td style="text-align:right;"> 0 </td>
  </tr>
</tbody>
</table>

Algo que não apareceu, mas estou curiosa, será que a busca por sexshop aumentou devido a quarentena?


```r
shop_all <- gtrends(c("sex shop"), geo = "BR", time = "today 12-m")

shop_all$interest_over_time %>%
  ggplot(aes(x=date, y=hits)) +
  geom_line() +
  theme_minimal() +
  labs(x= "", y= "", title = "Buscas no tempo por Sex Shop no Brasil")
```

<img src="/post/trends/index_files/figure-html/unnamed-chunk-8-1.png" width="672" />

SIM e muito!!! nos últimos 12 meses houve um pico das buscas por sexshop no google.

Pra quem acha interessante (e necessário) falar sobre sexo para além dos tabus, recomendo muito o @prazerela e @prazerele.

Fizeram uma [live](https://www.instagram.com/p/CE0I1YWHZ9J/) muito interessante pensando no dia do **Dia do Sexo**, sobre sexo, sexualidade e pandemia respondendo diversas perguntas e mostrando como nossas relações sexuais moldam nossa vida cultural e reproduzem o machismo, entre outros.



