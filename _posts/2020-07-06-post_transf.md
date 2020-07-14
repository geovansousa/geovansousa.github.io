---
title: "Transforming variables with the transf function"
author: "Geovan"
date: "06/07/2020"
---



As I commented in a [previous post](https://geovanjr.github.io/2020-06-19-A-function-for-easy-assessment-of-variables-normality/), I am an R beginner with beginner needs. In that post I presented a function (`whos_norm`) that evaluates the normality of a set of variables on a data, based on Shapiro-Wilk test. Now, I will show you a function, somewhat complementary to `whos_norm`, called `transf`, that apply a transformation on a chosen variable and evaluates its normality after the transformation.

I know transformation may be a *baby thing* for some people, but sometimes it is not for who is starting. Actually, the interesting in using `transf` is the time saving.

The `transf` function allows for several transformations, including <img src="https://render.githubusercontent.com/render/math?math=log"> (<img src="https://render.githubusercontent.com/render/math?math=log_{2}, log_{10}, log_{e}">), z-score, square and cube roots, square, inverse and arcsine of the square root.

So, assuming a variable <img src="https://render.githubusercontent.com/render/math?math=\mathbf{x}">, what `transf` actually does is:

* For $log$-transform, it calculates <img src="https://render.githubusercontent.com/render/math?math=log_{b}(x)">, where <img src="https://render.githubusercontent.com/render/math?math=b"> can be either 2, 10 or <img src="https://render.githubusercontent.com/render/math?math=e"> (i.e., <img src="https://render.githubusercontent.com/render/math?math=ln(x)">).

* For square root transformation, it calculates <img src="https://render.githubusercontent.com/render/math?math=\sqrt{x}">, while for cube root, <img src="https://render.githubusercontent.com/render/math?math=\sqrt[3]{x}">.

* For square transforming, just <img src="https://render.githubusercontent.com/render/math?math=x^2">.

* For inverse transformation, <img src="https://render.githubusercontent.com/render/math?math=\frac{1}{x}">.

* Finally, for centering, it just subtracts the mean of <img src="https://render.githubusercontent.com/render/math?math=\mathbf{x}"> (<img src="https://render.githubusercontent.com/render/math?math=\mu">) for each of its value, <img src="https://render.githubusercontent.com/render/math?math=x_i-\mu">. To standardize instead of centering, it just divides the centered <img src="https://render.githubusercontent.com/render/math?math=\mathbf{x}"> by its standard deviation (<img src="https://render.githubusercontent.com/render/math?math=\sigma">), scaling to z-score:<img src="https://render.githubusercontent.com/render/math?math=(x_i-\mu)/\sigma">. 



This said, we again will use the `iris` dataset for illustrate the function usage. Recalling the previous post, we saw that 3 variables from this data were not normally distributed:



```r
whos_norm(iris)$not_normal
```


|variable     |     W|      p|signif |
|:------------|-----:|------:|:------|
|Sepal.Length | 0.976| 0.0102|*      |
|Petal.Length | 0.876| 0.0000|*      |
|Petal.Width  | 0.902| 0.0000|*      |



Let's apply a log-transformation on `Sepal.Length` using `transf`. At first, obviously, you have to load the function:


```r
source("https://github.com/geovanjr/stats/raw/master/transf.R")
```

If you check the function, you will notice that it has 4 arguments: ``` transf(x, trans, data, plot) ```.

* In the `x` argument you have to specify the variable you want to transform, exactly how it is named on your dataset.

* In the `trans` argument, you will specify the transformation type as a string/character. It allows for `'log'` (<img src="https://render.githubusercontent.com/render/math?math=ln(x)">), `'log2'`, `'log10'` (logarithm transformation), `'sqrt'` for square root, `'cuberoot'` for cubic root, `'sq'` for square, `'inverse'` for inverse, `'center'` for centering and `'z-score'` for z-score.

* In the `data` argument, the dataset must be specified (`tibble` or `data.frame`).

* The `plot` argument is logical and optional. By default, a two Q-Q plot (one of the raw value and another of the variable transformed) with the corresponding Shapiro-Wilk statistics evaluation is ploted after running the function. You can set `plot` to `FALSE` if you do not want it to print. Even if you choose not to print it, the plot will be saved and can be assessed anytime.


All right, now let's transform `Sepal.Length`.


```r
transf(x = Sepal.Length, trans = 'log', data = iris)
```

<p align="center"><img src="/assets/img/post_transf_files/figure-html/unnamed-chunk-4-1.png" style="display: block; margin: auto;" width="600"/></p>

So, we can see that the log-transforming `Sepal.Length` makes its Shapiro-Wilk p-value jump to 0.054.


If we look to the density plot before and after transformation (I standardized the curves dividing by the sum to make it fall in a same scale and turns it easy to compare):

<p align="center"><img src="/assets/img/post_transf_files/figure-html/unnamed-chunk-5-1.png" style="display: block; margin: auto;" width="600"/></p>

we realize that although the log-curve is not a perfect sine, it is nearer to a gaussian shape than the raw curve.

If you want a silent evaluation, just set plot to `FALSE` and save the output into an object, as follows:


```r
sl_transf <- transf(Sepal.Length, 'log', iris, plot = F)
```

`sl_transf` is a list comprised of 6 elements:


```r
names(sl_transf)
```

```
## [1] "x"          "W"          "p"          "plot_raw"   "plot_trans"
## [6] "plot_grid"
```

Where `x` is the value of the transformed variable, `W` and `p` refers to the `shapiro.test`, `plot_raw` is the Q-Q plot of the untransformed `x`, `plot_trans` is the Q-Q plot of `x` after transformation and `plot_grid` is a grid containing both raw and transformed Q-Q plots. We can assess any of these elements with the dollar sign ($).

Maybe you want to save the transformed values on your dataset:


```r
iris_Sepal.Length_log <- sl_transf$x
```

Or see the Q-Q plots:


```r
sl_transf$plot_grid
```


<p align="center"><img src="/assets/img/post_transf_files/figure-html/unnamed-chunk-9-1.png" style="display: block; margin: auto;" width="600"/></p>


You can also apply the function to a subset of the data anytime:


```r
transf(Petal.Length, 'sq', iris %>% subset(Species == 'versicolor'))
```


<p align="center"><img src="/assets/img/post_transf_files/figure-html/unnamed-chunk-10-1.png" style="display: block; margin: auto;" width="600"/></p>


To use the function with a vector already saved in your Global Environment, just omit the `data` argument.


```r
v <- rgamma(100,6)

transf(v, 'log2')
```

<p align="center"><img src="/assets/img/post_transf_files/figure-html/unnamed-chunk-11-1.png" style="display: block; margin: auto;" width="600"/></p>

```r
v[25:40] <- rnorm(length(25:40))*5

transf(abs(v), 'sqrt')
```


<p align="center"><img src="/assets/img/post_transf_files/figure-html/unnamed-chunk-11-2.png" style="display: block; margin: auto;" width="600"/></p>


#### Some considerations

* Except for z-score, cube root and square transformations, negative values in `x` are not allowed.

* When 0 is an element of `x` (<img src="https://render.githubusercontent.com/render/math?math=0 ~ \epsilon ~ \mathbf{x}">), some mathematical operations get impossible, such as <img src="https://render.githubusercontent.com/render/math?math=log"> and <img src="https://render.githubusercontent.com/render/math?math=1/x">. If detected any 0, these transformations will be performed adding 0.5 to the actual value of `x` vector (<img src="https://render.githubusercontent.com/render/math?math=\mathbf{x’} = \mathbf{x} + 0.5">). It still can run the transformation, but some `NaN` may be produced and the data may be distorted (figure below). So, if you still want, use it for your own risk.


```r
v0 <- runif(100, min = 0, max = 2)

transf(v0, 'inverse')
```


<p align="center"><img src="/assets/img/post_transf_files/figure-html/unnamed-chunk-12-1.png" style="display: block; margin: auto;" width="600"/></p>


So, this is all, I guess.


<script type="text/javascript" async

src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML">
