---
title: Google Trends no R - Covid 19
author: monimazz
date: '2020-09-07'
draft: true
slug: google-trends-no-r-covid-19
categories:
  - Tutorial
tags:
  - covid
  - googletrends
subtitle: ''
summary: ''
authors: []
lastmod: '2020-09-07T04:53:42Z'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

Inspirada no livro "Todo Mundo Mente" de XXXX e no Dia do Sexo 06/09, quais tem sido as tendencias relacionadas a sexo dos brasileiros durante a pandemia? Quais seus principais questionamentos?

> "O poder do Google está no fato de as pessoas "contarem" ao gigantesco mecanismo de busca o que não diriam a mais ninguém. [...] As buscas do google são o conjunto de dados mais importante jamais coletado sobre a pisque humana.

Porque isso acontece?

Devido ao anonimato das buscas, o google parece um grande confidente ou confessionário, assim, permite que possamos analisar buscas que refletem desejos, medos e comportamentos do ser humano. 

Para acessar o R tem uma API através do pacote [gtrends](https://cran.r-project.org/web/packages/gtrendsR/gtrendsR.pdf)



```r
# Instalar o pacote gtrendsR
# devtools::install_github('PMassicotte/gtrendsR')

#Carregar os pacotes
library(gtrendsR)
library(tidyverse)
```


Se você quiser explorar mais o pacote, tem um site que eu acompanho que fez uma intro super fácil, recomendo acessar o [post](http://data7.blog/gtrendsr-google-trends-r/)

Agora analisando os dados

Buscando os dados

```r
resultados_all <- gtrends(c("ansiedade"), geo = "BR", time = "all")

resultados_tres <- gtrends(c("ansiedade"), geo = "BR", time = "today 3-m")
```


Topicos relacionados

```r
topic_all <- resultados_all$related_topics
topic_all <- topic_all %>% mutate(tipo = "2004-hoje")

topic_tres <- resultados_tres$related_topics
topic_tres <- topic_tres %>% mutate(tipo = "Últimos 3 meses")

topic <- rbind(topic_all, topic_tres)

topic %>%
  mutate(peso = as.numeric(subject)) %>%
  top_n(peso, 10) %>%
  ggplot(aes(x=value, y=peso)) +
  geom_segment( aes(xend=value, yend=0)) +
  geom_point( size=4, color="orange") +
  facet_grid(cols = vars(tipo)) +
  coord_flip()+
  theme_minimal() +
  labs(x= "", y= "", title = "Principais tópicos relacionados")
```

<img src="/post/2020-09-07-google-trends-no-r-covid-19.en_files/figure-html/unnamed-chunk-3-1.png" width="672" />
Interessante perceber que insonia e meditação são termos novos.

Buscas relacionadas


```r
search_all <- resultados_all$related_queries
search_all <- search_all %>% mutate(tipo = "2004-hoje")

search_tres <- resultados_tres$related_queries
search_tres <- search_tres %>% mutate(tipo = "Últimos 3 meses")

search <- rbind(search_all, search_tres)

search %>%
  mutate(peso = as.numeric(subject)) %>%
  top_n(peso, 10) %>%
  ggplot(aes(x=value, y=peso)) +
  geom_segment( aes(xend=value, yend=0)) +
  geom_point( size=4, color="orange") +
  facet_grid(cols = vars(tipo)) +
  coord_flip()+
  theme_minimal() +
  labs(x= "", y= "", title = "Principais buscas relacionadas")
```

<img src="/post/2020-09-07-google-trends-no-r-covid-19.en_files/figure-html/unnamed-chunk-4-1.png" width="672" />

Interessante notar as buscas novas nos ultimos meses de pandemia

* crise de ansiedade sintomas
* ansiedade o que é
* falta de ar ansiedade
* falta de ar
* depressão e ansiedade
* teste ansiedade



Vemos um pico de buscas sobre ansiedade ao longo do tempo


```r
resultados_all$interest_over_time %>%
  ggplot(aes(x=date, y=hits)) +
  geom_line() +
  theme_minimal() +
  labs(x= "", y= "", title = "Buscas no tempo")+
  gghighlight::gghighlight(hits %in% 83:100)
```

<img src="/post/2020-09-07-google-trends-no-r-covid-19.en_files/figure-html/unnamed-chunk-5-1.png" width="672" />
