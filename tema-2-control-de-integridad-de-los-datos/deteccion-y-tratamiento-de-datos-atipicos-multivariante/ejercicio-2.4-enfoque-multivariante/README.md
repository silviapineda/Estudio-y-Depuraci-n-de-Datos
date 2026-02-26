# Ejercicio 2.4:

1. Ejecuta el algoritmo LOF usando el ejemplo de la base de datos iris, esta vez sin introducir el outlier. ¿Hay atípicos multivariantes?
2. Realiza el estudio multivariante con la misma base de datos <mark style="color:orange;">**`ozone`**</mark> que hemos usado en los ejercicios anteriores y comprueba si hay datos atípicos de forma multivariante

{% file src="../../../.gitbook/assets/ozone (6).csv" %}

```r
ibrary(dbscan)
library(class)
library(ggplot2)
library(patchwork)
library(tidyverse)

data <- read.csv("ozone.csv")  # import data
```
