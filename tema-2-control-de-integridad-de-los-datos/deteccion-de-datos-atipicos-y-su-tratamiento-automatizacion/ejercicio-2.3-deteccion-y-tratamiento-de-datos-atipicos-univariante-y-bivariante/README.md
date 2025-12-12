# Ejercicio I

Con la misma base de datos <mark style="color:orange;">**`ozone`**</mark> de R  comprueba si el resto de variables tienen datos atípicos

{% file src="../../../.gitbook/assets/ozone (5).csv" %}

```r
data <- read.csv("ozone.csv")  # import data
```

Puedes ejecutar los siguientes pasos:

1. Comprueba si hay datos atípicos de forma univariante en cada una de las variables
   1. Usa gráficos para tomar la decisión
   2. Cuantifica los atípicos para tomar la decisión
2. Haz un estudio bivariante para decidir qué outliers quitarías y hazlo si la decisión es que si.