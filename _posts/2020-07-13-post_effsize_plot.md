---
title: 'Calculate and plot effect size with effsize_plot'
author: ''
date: "12/07/2020"
bibliography: packages.bib
---



### Description
Calculate and plot r and Cohen's d effect size and its confidence intervals.

### Usage

```r
effsize_plot(x, g, method = 'r', paired = F, 
             color = 'black', size = 2,
             width = .05, 
             line.col = 'grey', lwd = 1, lty = 2, 
             ggtheme = theme_minimal(),
             par_r = list(conf = .95, type = 'perc', R = 1000), 
             par_d = list(pooled = T, hedges.correction = F, conf.level = .95),
             plot = T, show = F)
```

### Arguments
`x`, `g` dataframe and vector with numerical and factor variables, respectively.


`method` specify what effect size to compute: Wilcoxon r (`'r'`) or Cohen's d (`'d'`).


`paired` logical. Wether the samples are paired.


`color`, `size` character or hexa specifying the errorbar and point colors, and size of points, respectively.


`width` width of errorbar brackets.


`line.col`, `lwd`, `lty` color, width and type of the zero-crossing line, respectively.


`ggtheme` a ggplot2 theme function.


`par_r` list of parameters to be passed to `WilcoxonR` or `WilcoxonPairedR` functions. Specify as `list(conf, type, R)`, where `conf` is the level of confidence interval, `type` is the type of bootstrap estimation for confidence interval ('norm' for ..., 'basic' for ..., 'perc' for ..., 'bca' for ...) and `R` is the number of bootstrap resampling.


`par_d` list of parameters to be passed to `cohen.d` function. Specify as `list(pooled, hedges.correction, conf.level)`, where `pooled` is a logical indicating whether should be used the pooled standard deviation (`TRUE`, default) or the whole sample standard deviation (`FALSE`), `hedges.correction` is a logical indicating if Hedges correction should be applied (`TRUE`) or not (`FALSE`, default) and `conf.level` is the level of confidence inteval.


`plot` logical indicating whether to plot (`TRUE`, default) or not (`FALSE`)


`show` logical indicating whether to print the estimate, p-value, effect size and its confidence intervals (`TRUE`) or not (`FALSE`, default).


### Details
The packages `rcompanion` [@R-rcompanion] and `effsize` [@R-effsize] are required to calculate r and d/g effect size, respectively.


For more details about how each effect size is calculated, read the documentation of [`rcompanion`](https://cran.r-project.org/web/packages/rcompanion/rcompanion.pdf) and [`effsize`](https://cran.r-project.org/web/packages/effsize/effsize.pdf). 


### Value

A list comprised of:

`es_plot` the plot output.


`metrics` a `data.frame` with variable names, statistic (t or W/V), p-value, effect size (r or d/g) and lower and upper limits of confidence interval. 


### Examples


Generating dataset

```r
x <- factor(rep(c('A','B'), each = 50))

y1 <- abs(rnorm(100, mean = 8.4, sd = 2)) %>% round(.,0)
y1[51:100] <- abs(rnorm(50, 10, 4)) %>% round(.,0)

y2 <- abs(rnorm(100, 25, 5)) %>% round(.,0)
y2[51:100] <- abs(rnorm(50, 23.3, 4)) %>% round(.,0)

y3 <- abs(rnorm(100, 8.5, 4)) %>% round(.,0)
y3[51:100] <- abs(rnorm(50, 11.2, 5.5)) %>% round(.,0)

df <- data.frame(x,y1,y2,y3)
```


Loading the function

```r
source('https://github.com/geovanjr/stats/raw/master/effsize_plot.R')
```


Calculating r effect size 

```r
effsize_plot(df[,2:4], df[,1], method = 'r', paired = F, plot = T, show = T,
             color = 'forestgreen',
             par_r = list(R = 500))
```

![](/assets/img/post_effsize_plot_files/figure-html/unnamed-chunk-5-1.png)

```
##   variable      W      p        r   lower   upper
## 1       y1 1254.5 0.9778  0.00313 -0.2050  0.2210
## 2       y2 1656.5 0.0050  0.28100  0.0893  0.4700
## 3       y3  907.5 0.0180 -0.23700 -0.4290 -0.0232
```


Calculating d effect size 

```r
effsize_plot(df[,2:4], df[,1], method = 'd', paired = F, plot = T, show = T,
             color = 'forestgreen', size = 3, lty = 3, ggtheme = theme_light())
```

![](/assets/img/post_effsize_plot_files/figure-html/unnamed-chunk-6-1.png)

```
##   variable          t      p           d      lower      upper
## 1       y1 -0.4888161 0.6264 -0.09776321 -0.4948937  0.2993673
## 2       y2  2.7845548 0.0065  0.55691096  0.1523971  0.9614248
## 3       y3 -2.5415534 0.0128 -0.50831068 -0.9115626 -0.1050588
```


Calculating g effect size 

```r
effsize_plot(df[,2:4], df[,1], method = 'd', paired = F, plot = T, show = T,
             color = 'forestgreen', size = 3, lty = 3, 
             par_d = list(hedges.correction = TRUE))
```

![](/assets/img/post_effsize_plot_files/figure-html/unnamed-chunk-7-1.png)

```
##   variable          t      p           g      lower       upper
## 1       y1 -0.4888161 0.6264 -0.07815062 -0.4721492  0.31584796
## 2       y2  2.7845548 0.0065  0.66301660  0.2584923  1.06754094
## 3       y3 -2.5415534 0.0128 -0.44189054 -0.8405164 -0.04326464
```


Changing `es_plot` aesthetics

```r
es <- effsize_plot(df[,2:4], df[,1], method = 'd', paired = F, plot = F, show = F,
             color = 'forestgreen', size = 3, lty = 3, 
             par_d = list(hedges.correction = TRUE))

es$es_plot +
  theme(axis.text.y = element_blank()) +
  annotate('text', 1+.2, es$metrics[1,4], 
           label = paste0(es$metrics[1,1]), 
           size = 4, color = 'forestgreen') +
  annotate('text', 2+.2, es$metrics[2,4], 
           label = paste0(es$metrics[2,1]), 
           size = 4, color = 'forestgreen') +
  annotate('text', 3+.2, es$metrics[3,4], 
           label = paste0(es$metrics[3,1]), 
           size = 4, color = 'forestgreen')
```

![](/assets/img/post_effsize_plot_files/figure-html/unnamed-chunk-8-1.png)



Getting `metrics`

```r
es$metrics
```

<div class="kable-table">

|variable |          t|      p|          g|      lower|      upper|
|:--------|----------:|------:|----------:|----------:|----------:|
|y1       | -0.4888161| 0.6264| -0.0781506| -0.4721492|  0.3158480|
|y2       |  2.7845548| 0.0065|  0.6630166|  0.2584923|  1.0675409|
|y3       | -2.5415534| 0.0128| -0.4418905| -0.8405164| -0.0432646|

</div>


### References

