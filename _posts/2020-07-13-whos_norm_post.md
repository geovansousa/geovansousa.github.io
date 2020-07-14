---
title: A funtion for easy assessment of variables normality
author: "Geovan"
date: "19/06/2020"
---




So, I am an enthusiastic of R programming since about one year ago, when I started to use it for statistical purposes. Since then, I've been trying to spread the benefits of using R for hypothesis testing and data visualization. 

Not rarely, we have to test our variables for normality, specially to make a decision about what inferential test to use (parametric or nonparametric). Attempting to help other R beginners who are aiming to check the normality of their data, I decided to make available a simple function I had built due to a lab demand. This function is a way to easily know which variables from a given dataset have a normal (or not normal) distribution. 
In this post I will show you how it works and how to use it.

* **Loading the function**

The function is called `whos_norm` and can be found on my [Github](https://github.com/geovanjr/stats).
Alternatively, you can load the function to your environment, simply running:


```r
source('https://github.com/geovanjr/stats/raw/master/whos_norm.R')
```

Function loaded, we can start to play.

At first, you can notice that `whos_norm` has just only one argument, `data`, what make its use quite easy. Literally, you just have to put your dataset and run.

To illustrate, I'll use the `iris` dataset. But before we use the function itself, let's take a look on the `iris` data.


```r
library(tidyverse)

glimpse(iris)
```

```
## Rows: 150
## Columns: 5
## $ Sepal.Length <dbl> 5.1, 4.9, 4.7, 4.6, 5.0, 5.4, 4.6, 5.0, 4.4, 4.9, 5.4,...
## $ Sepal.Width  <dbl> 3.5, 3.0, 3.2, 3.1, 3.6, 3.9, 3.4, 3.4, 2.9, 3.1, 3.7,...
## $ Petal.Length <dbl> 1.4, 1.4, 1.3, 1.5, 1.4, 1.7, 1.4, 1.5, 1.4, 1.5, 1.5,...
## $ Petal.Width  <dbl> 0.2, 0.2, 0.2, 0.2, 0.2, 0.4, 0.3, 0.2, 0.2, 0.1, 0.2,...
## $ Species      <fct> setosa, setosa, setosa, setosa, setosa, setosa, setosa...
```

We can see that `iris` data has 150 observations regarding 4 numeric variables (`Sepal.Length`, `Sepal.Width`, `Petal.Length` and `Petal.Width`) and one factor (`Species`), which levels stand for the iris species.


* **Using `whos_norm`**

Now, using the function, we can look for the normality of `iris`' variables:


```r
whos_norm(iris)
```

```
## $all
##       variable     W      p signif
## 1 Sepal.Length 0.976 0.0102      *
## 2  Sepal.Width 0.985 0.1012 normal
## 3 Petal.Length 0.876 0.0000      *
## 4  Petal.Width 0.902 0.0000      *
## 
## $normal
##      variable     W      p signif
## 1 Sepal.Width 0.985 0.1012 normal
## 
## $not_normal
##       variable     W      p signif
## 1 Sepal.Length 0.976 0.0102      *
## 2 Petal.Length 0.876 0.0000      *
## 3  Petal.Width 0.902 0.0000      *
```

The output of `whos_norm` comprise a list of three elements: `all`, `normal` and `not_normal`. As the names suggest, the first one shows all the variables assessed, the second one shows only normal while the third shows only non-normal variables. Both shows the variables name and the related statistic. At this point, I have to say that the assessment of normality is based on the Shapiro-Wilk test (`shapiro.test`), so the `W` and the `p`-value is referent to this test. In a near future I should add an argument to choose an evaluation based on Shapiro-Wilk or Kolmogorov-Smirnov test. 

Interpreting the output, we can easily see that only `Sepal.Width` has a normal distribution, while `Sepal.Length`, `Petal.Length` and `Petal.Width` both do not. We can check this looking for its density and Q-Q plot:

<p align="center"><img src="/assets/img/whos_norm_post_files/figure-html/unnamed-chunk-5-1.png" style="display: block; margin: auto;" width= "500"/></p>


* **Dealing with factor variables**

You may have noticed that `Species` is not outputed, as expected, since it is a factor. It is because `whos_norm` identify which variables are factors and exclude it from `shapiro.test`, so you don't have to worry about subsetting your data to numerical variables.

* **Assessing output elements**

Maybe you are a person who like quiet outputs. In this case, you can save `whos_norm` output into an object and assess only the element you want:


```r
normality_assess <- whos_norm(iris)

normality_assess$not_normal # seeing just non-normal variables
```

<div class="kable-table">

|variable     |     W|      p|signif |
|:------------|-----:|------:|:------|
|Sepal.Length | 0.976| 0.0102|*      |
|Petal.Length | 0.876| 0.0000|*      |
|Petal.Width  | 0.902| 0.0000|*      |

</div>

Still, if you do not like your environment full of objects, you can call the output element whithout the need of saving it:


```r
whos_norm(iris)$normal
```

<div class="kable-table">

|variable    |     W|      p|signif |
|:-----------|-----:|------:|:------|
|Sepal.Width | 0.985| 0.1012|normal |

</div>


* **Data structure compatibilities**

Regarding data structure, `whos_norm` works with `tibble` and `data.frame`, but not with `matrix`.


* **Requirements**

To finish my brief post, I must say something about the requirements of `whos_norm`. Actually, only `dplyr` package is required, once `shapiro.test` function is a built-in. You can load `dplyr` or `tidyverse` before running the `whos_norm` or, if you already have one of these packages installed, the function will call for them when running. 

That's all, folks.

