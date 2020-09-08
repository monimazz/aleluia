---
title: Google Trends no R - Qual o comportamento do brasileiro em relação a sexo durante a pandemia?
author: monimazz
date: '2020-09-06'
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
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

Você já procurou no google coisas que nunca contou a ninguém? Que tem medo ou vergonha de compartilhar?

**O Google é hoje a maior base de dados sobre o comportamento humano**, conseguindo refletir nossos desejos, medos, gostos, angustias, dúvidas e mais, só é necessário fazer as perguntas certas. Isto é o defende Seth Stephens-Davidowitz no livro "Todo Mundo Mente", explicando que:

> "O poder do Google está no fato de as pessoas "contarem" ao gigantesco mecanismo de busca o que não diriam a mais ninguém. [...] As buscas do google são o conjunto de dados mais importante jamais coletado sobre a pisque humana.

### Por que isso acontece?

Devido ao anonimato das buscas, o google parece um grande confidente ou confessionário, assim, as pessoas não sentem a pressão ou julgamento em relação a um assunto, algo que poderiam ter em uma pesquisa ou em uma conversa com um amigo, parente, levando-as a mentir, até para sí próprias. 

Dessa forma, inspirada no livro e porque recentemente foi Dia do Sexo (06/09), fiquei me perguntando: o que o brasileiro busca no google em relação a sexo? quais têm sido as tendências desde o início da pandemia? Resolvi escolher esse tema justamente pelo tabu social envolvido e ser algo extremamente intímo, potencialmente revelando algo interessante sobre o comportamento do brasileiro no assunto que pouco sabemos, justamente pelo tabu do assunto. 

Para isso, vou utilizar o pacote `gtrends`, que permite capturar informações do Google Trends pelo R e fazer análises! Para mais informações do pacote, acessar o CRAN: [gtrends](https://cran.r-project.org/web/packages/gtrendsR/gtrendsR.pdf). Se você quiser explorar mais o pacote, tem um site que eu acompanho que fez uma intro super fácil, recomendo acessar o [post](http://data7.blog/gtrendsr-google-trends-r/).

Primeiro é necessário baixar o pacote e as librays que vão ser utilizadas.





```r
# Instalar o pacote gtrendsR
# devtools::install_github('PMassicotte/gtrendsR')

#Carregar os pacotes
library(gtrendsR)
library(tidyverse)
```

### Buscar a informação

Primeiro específicamos as palavras ou palavaras, depois o localização que queremos, no caso Brasil e o tempo de busca. Resolvi procurar de forma suuuper genérica mesmo, só o termo, não necessáriamente é a "pergunta correta"

```r
resultados_all <- gtrends(c("sexo"), geo = "BR", time = "all")

resultados_tres <- gtrends(c("sexo"), geo = "BR", time = "2020-03-01 2020-09-06")
```

```
## Warning in system("timedatectl", intern = TRUE): running command 'timedatectl'
## had status 1
```

A partir dessa busca com um termo amplo, vou analisar quais os tópicos e buscas relacionadas, comparando os últimos 3 meses com o período atual, houve aumento de alguma busca? Quais os termos?

Vale ressaltar que tanto em tópicos quanto em buscas para realizar o gráfico estou excluindo os assuntos em ascensão e deixando somente o que está como principais buscas relaciondas, no entanto ainda dando destaque os que estão em ascensão por meio de uma tabela, por possuírem uma escala diferente que não vai de 0 a 100.

### Topicos relacionados

```r
topic_all <- resultados_all$related_topics
topic_all <- topic_all %>% mutate(tipo = "2004-hoje")

topic_tres <- resultados_tres$related_topics
topic_tres <- topic_tres %>% mutate(tipo = "Março (01) - Setembro (06)")

topic <- rbind(topic_all, topic_tres)

topic %>%
  filter(related_topics != "rising") %>%
  mutate(peso = as.numeric(subject)) %>%
  ggplot(aes(x=value, y=peso)) +
  geom_segment( aes(xend=value, yend=0)) +
  geom_point( size=4, color="orange") +
  facet_grid(cols = vars(tipo)) +
  coord_flip()+
  theme_minimal() +
  labs(x= "", y= "", title = "Principais tópicos relacionados")
```

<img src="/post/2020-09-06-google-trends-no-r-todo-mundo-mente.en_files/figure-html/unnamed-chunk-4-1.png" width="672" />
Os tópicos relacionados não tiveram grande mudança, no entanto, em vista de tempos de isolamento social **GIF** apareceu como um tópico novo, será é uma nova forma das pessoas continuarem suas relações e se comunicando? Vou explorar esse assunto. Casal (couple) também é um termo que está como um dos principais nas buscas nesse período de quarentena. Outra interessante que pode estar relacionada é **"Sexo virtual"** e **casal**, pessoas buscando novas formas de se relacionar?


Já os assuntos em ascensão:

```r
topic %>% filter(related_topics == "rising") %>% knitr::kable()
```



|subject |related_topics |value               |geo |keyword | category|tipo                       |
|:-------|:--------------|:-------------------|:---|:-------|--------:|:--------------------------|
|+170%   |rising         |Property caretaker  |BR  |sexo    |        0|2004-hoje                  |
|+130%   |rising         |Amateur             |BR  |sexo    |        0|2004-hoje                  |
|+90%    |rising         |Position            |BR  |sexo    |        0|2004-hoje                  |
|+60%    |rising         |Scene               |BR  |sexo    |        0|2004-hoje                  |
|+50%    |rising         |Virtual sex         |BR  |sexo    |        0|Março (01) - Setembro (06) |
|+40%    |rising         |No Strings Attached |BR  |sexo    |        0|Março (01) - Setembro (06) |


Nos termos em ascensão, nota-se os vídeos (no período desde de 2004), já no período mais recente está "sexo virtual" e "sem compromisso". 

Olhando o gráfico, o que é presente e de certa forma mostra o lado "escondido" do comportamento (ainda que há várias pesquisas sobre isso em relação a pornografia) são os termos **padrasto**, não vou explorar profundamente o assunto pois já existem pesquisas, mas em uma busca pelos tópicos similares os termos incesto, erotismo, estupro e outros estão fortemente relacionados ao tópico, o que demonstra o problema em nosso país relacionado a pedofilia, uma reportagem da [Gazeta do Povo](https://www.gazetadopovo.com.br/vida-e-cidadania/alerta-aos-pais-pedofilia-virtual-aumenta-no-brasil-em-meio-a-pandemia/) tratou do assunto que infelizmente aumentou virtualmente na quarentena.

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
  geom_point( size=4, color="orange") +
  facet_grid(cols = vars(tipo)) +
  coord_flip()+
  theme_minimal() +
  labs(x= "", y= "", title = "Principais buscas relacionadas")
```

<img src="/post/2020-09-06-google-trends-no-r-todo-mundo-mente.en_files/figure-html/unnamed-chunk-6-1.png" width="672" />
As buscas relacionadas ao termo trazem questões interessantes, os termos foram: "viciado em sexo", "perguntas sobre sexo", "sexo tantrico", não tenho respostas sobre o  porque as pessoas fizeram essas buscas, o que as motivou. **O que você acha que pode ter sido? Quais as suas hipóteses? Comenta aí!**


Já os assuntos em ascensão:

```r
search %>% filter(related_queries == "rising") %>% knitr::kable()
```



|subject |related_queries |value                |geo |keyword | category|tipo                       |
|:-------|:---------------|:--------------------|:---|:-------|--------:|:--------------------------|
|+350%   |rising          |videos de amor       |BR  |sexo    |        0|2004-hoje                  |
|+350%   |rising          |se                   |BR  |sexo    |        0|2004-hoje                  |
|+300%   |rising          |sexo sem compromisso |BR  |sexo    |        0|2004-hoje                  |
|+150%   |rising          |frases de sexo       |BR  |sexo    |        0|2004-hoje                  |
|+50%    |rising          |sonhar com sexo      |BR  |sexo    |        0|Março (01) - Setembro (06) |
|+40%    |rising          |sexo sem compromisso |BR  |sexo    |        0|Março (01) - Setembro (06) |

Algo interessante de notar é o aumento durante o período da pandemia que houve um aumento nas buscas sobre "sonhar com sexo", será que os desejos das pessoas e o isolamento está levando que isso ocorra com maior frequencia?



Explorando mais o contexto de isolamento social, é possível perceber um grande aumento de buscas relacionadas a "sexo virtual", justamente no período de maior isolamento no país


```r
result2_all <- gtrends(c("sexo virtual"), geo = "BR", time = "today 12-m", cookie_url = "http://trends.google.com/Cookies/NID",   onlyInterest = TRUE)

result2_all$interest_over_time %>%
  ggplot(aes(x=date, y=hits)) +
  geom_line() +
  theme_minimal() +
  labs(x= "", y= "", title = "Buscas nos ultimos 12 meses")
```

<img src="/post/2020-09-06-google-trends-no-r-todo-mundo-mente.en_files/figure-html/unnamed-chunk-8-1.png" width="672" />

#### GIFS, quais as buscas relacionadas?

Olhando somente os termos em ascensão de busca, nota-se que há uma grande relação do uso de GIFS sexuais, possivelmente uma forma que passou a ser mais amplamente utilizada durante o período de isolamento para se comunicar em relação a sexo. 


```r
gif <- gtrends("GIF", geo = "BR", time = "all", cookie_url = "http://trends.google.com/Cookies/NID")

gif$related_queries %>% filter(related_queries == "rising") %>% knitr::kable()
```



|subject  |related_queries |value             |geo |keyword | category|
|:--------|:---------------|:-----------------|:---|:-------|--------:|
|Breakout |rising          |tumblr gif        |BR  |GIF     |        0|
|Breakout |rising          |gif bom dia       |BR  |GIF     |        0|
|Breakout |rising          |gif boa noite     |BR  |GIF     |        0|
|Breakout |rising          |gif whatsapp      |BR  |GIF     |        0|
|Breakout |rising          |gif facebook      |BR  |GIF     |        0|
|Breakout |rising          |gif de bom dia    |BR  |GIF     |        0|
|Breakout |rising          |gif de boa noite  |BR  |GIF     |        0|
|Breakout |rising          |gif instagram     |BR  |GIF     |        0|
|Breakout |rising          |gif youtube       |BR  |GIF     |        0|
|Breakout |rising          |gif beijo         |BR  |GIF     |        0|
|Breakout |rising          |gif para whatsapp |BR  |GIF     |        0|
|Breakout |rising          |gif sexy          |BR  |GIF     |        0|
|Breakout |rising          |oral gif          |BR  |GIF     |        0|
|Breakout |rising          |gretchen gif      |BR  |GIF     |        0|
|Breakout |rising          |gif no facebook   |BR  |GIF     |        0|
|Breakout |rising          |como criar gif    |BR  |GIF     |        0|
|Breakout |rising          |gif no whatsapp   |BR  |GIF     |        0|
|Breakout |rising          |gif boa tarde     |BR  |GIF     |        0|
|Breakout |rising          |gif dançando      |BR  |GIF     |        0|
|Breakout |rising          |gif para tumblr   |BR  |GIF     |        0|
|Breakout |rising          |wallpaper gif     |BR  |GIF     |        0|
|Breakout |rising          |gif para celular  |BR  |GIF     |        0|
|Breakout |rising          |gif engraçado     |BR  |GIF     |        0|
|Breakout |rising          |como baixar gif   |BR  |GIF     |        0|
|+900%    |rising          |gif gay           |BR  |GIF     |        0|

Algo que não apareceu, mas estou curiosa, será que a busca por sexshop aumentou devido a quarentena?


```r
shop_all <- gtrends(c("sex shop"), geo = "BR", time = "today 12-m")

shop_all$interest_over_time %>%
  ggplot(aes(x=date, y=hits)) +
  geom_line() +
  theme_minimal() +
  labs(x= "", y= "", title = "Buscas no tempo por Sex Shop no Brasil")
```

<img src="/post/2020-09-06-google-trends-no-r-todo-mundo-mente.en_files/figure-html/unnamed-chunk-10-1.png" width="672" />

SIM e muito!!! nos últimos 12 meses houve um pico das buscas por sexshop no google.

Pra quem acha interessante (e necessário) falar sobre sexo para além dos tabus, recomendo muito o @prazerela e @prazerele.

Fizeram uma live muito interessante pensando no dia do **Dia do Sexo**, sobre sexo, sexualidade e pandemia respondendo diversas perguntas e mostrando como nossas relações sexuais moldam nossa vida cultural e reproduzem o machismo, entre outros.

<blockquote class="instagram-media" data-instgrm-captioned data-instgrm-permalink="https://www.instagram.com/tv/CE0I1YWHZ9J/?utm_source=ig_embed&amp;utm_campaign=loading" data-instgrm-version="12" style=" background:#FFF; border:0; border-radius:3px; box-shadow:0 0 1px 0 rgba(0,0,0,0.5),0 1px 10px 0 rgba(0,0,0,0.15); margin: 1px; max-width:540px; min-width:326px; padding:0; width:99.375%; width:-webkit-calc(100% - 2px); width:calc(100% - 2px);"><div style="padding:16px;"> <a href="https://www.instagram.com/tv/CE0I1YWHZ9J/?utm_source=ig_embed&amp;utm_campaign=loading" style=" background:#FFFFFF; line-height:0; padding:0 0; text-align:center; text-decoration:none; width:100%;" target="_blank"> <div style=" display: flex; flex-direction: row; align-items: center;"> <div style="background-color: #F4F4F4; border-radius: 50%; flex-grow: 0; height: 40px; margin-right: 14px; width: 40px;"></div> <div style="display: flex; flex-direction: column; flex-grow: 1; justify-content: center;"> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; margin-bottom: 6px; width: 100px;"></div> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; width: 60px;"></div></div></div><div style="padding: 19% 0;"></div> <div style="display:block; height:50px; margin:0 auto 12px; width:50px;"><svg width="50px" height="50px" viewBox="0 0 60 60" version="1.1" xmlns="https://www.w3.org/2000/svg" xmlns:xlink="https://www.w3.org/1999/xlink"><g stroke="none" stroke-width="1" fill="none" fill-rule="evenodd"><g transform="translate(-511.000000, -20.000000)" fill="#000000"><g><path d="M556.869,30.41 C554.814,30.41 553.148,32.076 553.148,34.131 C553.148,36.186 554.814,37.852 556.869,37.852 C558.924,37.852 560.59,36.186 560.59,34.131 C560.59,32.076 558.924,30.41 556.869,30.41 M541,60.657 C535.114,60.657 530.342,55.887 530.342,50 C530.342,44.114 535.114,39.342 541,39.342 C546.887,39.342 551.658,44.114 551.658,50 C551.658,55.887 546.887,60.657 541,60.657 M541,33.886 C532.1,33.886 524.886,41.1 524.886,50 C524.886,58.899 532.1,66.113 541,66.113 C549.9,66.113 557.115,58.899 557.115,50 C557.115,41.1 549.9,33.886 541,33.886 M565.378,62.101 C565.244,65.022 564.756,66.606 564.346,67.663 C563.803,69.06 563.154,70.057 562.106,71.106 C561.058,72.155 560.06,72.803 558.662,73.347 C557.607,73.757 556.021,74.244 553.102,74.378 C549.944,74.521 548.997,74.552 541,74.552 C533.003,74.552 532.056,74.521 528.898,74.378 C525.979,74.244 524.393,73.757 523.338,73.347 C521.94,72.803 520.942,72.155 519.894,71.106 C518.846,70.057 518.197,69.06 517.654,67.663 C517.244,66.606 516.755,65.022 516.623,62.101 C516.479,58.943 516.448,57.996 516.448,50 C516.448,42.003 516.479,41.056 516.623,37.899 C516.755,34.978 517.244,33.391 517.654,32.338 C518.197,30.938 518.846,29.942 519.894,28.894 C520.942,27.846 521.94,27.196 523.338,26.654 C524.393,26.244 525.979,25.756 528.898,25.623 C532.057,25.479 533.004,25.448 541,25.448 C548.997,25.448 549.943,25.479 553.102,25.623 C556.021,25.756 557.607,26.244 558.662,26.654 C560.06,27.196 561.058,27.846 562.106,28.894 C563.154,29.942 563.803,30.938 564.346,32.338 C564.756,33.391 565.244,34.978 565.378,37.899 C565.522,41.056 565.552,42.003 565.552,50 C565.552,57.996 565.522,58.943 565.378,62.101 M570.82,37.631 C570.674,34.438 570.167,32.258 569.425,30.349 C568.659,28.377 567.633,26.702 565.965,25.035 C564.297,23.368 562.623,22.342 560.652,21.575 C558.743,20.834 556.562,20.326 553.369,20.18 C550.169,20.033 549.148,20 541,20 C532.853,20 531.831,20.033 528.631,20.18 C525.438,20.326 523.257,20.834 521.349,21.575 C519.376,22.342 517.703,23.368 516.035,25.035 C514.368,26.702 513.342,28.377 512.574,30.349 C511.834,32.258 511.326,34.438 511.181,37.631 C511.035,40.831 511,41.851 511,50 C511,58.147 511.035,59.17 511.181,62.369 C511.326,65.562 511.834,67.743 512.574,69.651 C513.342,71.625 514.368,73.296 516.035,74.965 C517.703,76.634 519.376,77.658 521.349,78.425 C523.257,79.167 525.438,79.673 528.631,79.82 C531.831,79.965 532.853,80.001 541,80.001 C549.148,80.001 550.169,79.965 553.369,79.82 C556.562,79.673 558.743,79.167 560.652,78.425 C562.623,77.658 564.297,76.634 565.965,74.965 C567.633,73.296 568.659,71.625 569.425,69.651 C570.167,67.743 570.674,65.562 570.82,62.369 C570.966,59.17 571,58.147 571,50 C571,41.851 570.966,40.831 570.82,37.631"></path></g></g></g></svg></div><div style="padding-top: 8px;"> <div style=" color:#3897f0; font-family:Arial,sans-serif; font-size:14px; font-style:normal; font-weight:550; line-height:18px;"> Ver essa foto no Instagram</div></div><div style="padding: 12.5% 0;"></div> <div style="display: flex; flex-direction: row; margin-bottom: 14px; align-items: center;"><div> <div style="background-color: #F4F4F4; border-radius: 50%; height: 12.5px; width: 12.5px; transform: translateX(0px) translateY(7px);"></div> <div style="background-color: #F4F4F4; height: 12.5px; transform: rotate(-45deg) translateX(3px) translateY(1px); width: 12.5px; flex-grow: 0; margin-right: 14px; margin-left: 2px;"></div> <div style="background-color: #F4F4F4; border-radius: 50%; height: 12.5px; width: 12.5px; transform: translateX(9px) translateY(-18px);"></div></div><div style="margin-left: 8px;"> <div style=" background-color: #F4F4F4; border-radius: 50%; flex-grow: 0; height: 20px; width: 20px;"></div> <div style=" width: 0; height: 0; border-top: 2px solid transparent; border-left: 6px solid #f4f4f4; border-bottom: 2px solid transparent; transform: translateX(16px) translateY(-4px) rotate(30deg)"></div></div><div style="margin-left: auto;"> <div style=" width: 0px; border-top: 8px solid #F4F4F4; border-right: 8px solid transparent; transform: translateY(16px);"></div> <div style=" background-color: #F4F4F4; flex-grow: 0; height: 12px; width: 16px; transform: translateY(-4px);"></div> <div style=" width: 0; height: 0; border-top: 8px solid #F4F4F4; border-left: 8px solid transparent; transform: translateY(-4px) translateX(8px);"></div></div></div></a> <p style=" margin:8px 0 0 0; padding:0 4px;"> <a href="https://www.instagram.com/tv/CE0I1YWHZ9J/?utm_source=ig_embed&amp;utm_campaign=loading" style=" color:#000; font-family:Arial,sans-serif; font-size:14px; font-style:normal; font-weight:normal; line-height:17px; text-decoration:none; word-wrap:break-word;" target="_blank">um papo descontraído sobre sexo e sexualidade com Prazerela e @prazerele vem ver!</a></p> <p style=" color:#c9c8cd; font-family:Arial,sans-serif; font-size:14px; line-height:17px; margin-bottom:0; margin-top:8px; overflow:hidden; padding:8px 0 7px; text-align:center; text-overflow:ellipsis; white-space:nowrap;">Uma publicação compartilhada por <a href="https://www.instagram.com/prazerela/?utm_source=ig_embed&amp;utm_campaign=loading" style=" color:#c9c8cd; font-family:Arial,sans-serif; font-size:14px; font-style:normal; font-weight:normal; line-height:17px;" target="_blank"> Prazerela por Mariana Stock</a> (@prazerela) em <time style=" font-family:Arial,sans-serif; font-size:14px; line-height:17px;" datetime="2020-09-07T00:09:02+00:00">6 de Set, 2020 às 5:09 PDT</time></p></div></blockquote> <script async src="//www.instagram.com/embed.js"></script>

