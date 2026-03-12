# Ejercicio 3.1: Detección de datos perdidos

La siguiente base de datos corresponde al rendimiento académico de alumnado que cursa una FP:&#x20;

{% file src="../../../.gitbook/assets/students_FP.csv" %}

```r
library(naniar)
library(tidyverse)
data <- read.csv("students_FP.csv",
na.strings = c("", "NA", "NaN", "NULL"),
stringsAsFactors = TRUE
)
```

Realiza lo siguiente:

1. Cuantifica el número de datos perdidos que hay en la base de datos tanto a nivel global como de forma individual, piensa en estos números y qué significan.&#x20;
2. Visualiza los datos perdidos de forma global y comenta los gráficos.&#x20;
3. Clasifica en MAR, MNAR y MCAR los datos perdidos. Para ello usa la siguiente guía:
   1. Visualiza por subgrupos de las variables cualitativas.
   2. Visualiza por las variables cuantitativas.
   3. Crea el dataset de shadow y visualiza por cuantitativas y cualitativas para verificar lo observado anteriormente.

