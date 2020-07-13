---
title: "testing"
author: "Geovan"
date: "13/07/2020"
---


```r
t <- seq(1e-3,3,1e-3)

s <- sin(2*pi*30*t)
s <- s + abs(rnorm(1))*sin(2*pi*15*t)

plot(t,s, type = 'l', ylim=c(-3,3))
lines(t[1e3:2e3], s[1e3:2e3], col='tomato')
```
