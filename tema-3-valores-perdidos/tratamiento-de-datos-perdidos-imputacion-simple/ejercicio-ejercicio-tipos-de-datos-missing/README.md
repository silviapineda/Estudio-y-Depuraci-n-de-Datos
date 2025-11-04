---
hidden: true
---

# Ejercicio: Ejercicio tipos de datos missing

Estos datos ejemplifican un escenario en donde se seleccionan candidatos a un empleo, y los candidatos completan un test de IQ, durante su entrevista de trabajo y posteriormente un supervisor evalua su rendimiento después de un periodo de 6 meses. En el ejemplo hay 4 columnas de Rendimiento, una que sería la completa y otras tres que se han borrado los datos considerando los 3 tipos de datos missing que hemos estudiado (MCAR, MAR, MNAR).&#x20;

{% file src="../../../.gitbook/assets/EjemploTiposMissing.csv" %}

```r
library(naniar)
library(ggplot2)
datos<-read.csv("EjemploTiposMissing.csv")
```

1. Averigua a qué columna corresponde los datos MCAR, MAR y MNAR y explica por qué. Después cambia el nombre de las columnas por <mark style="color:purple;">`Rendimiento_MCAR, Rendimiento_MAR y Rendimiento_MNAR`</mark>.
2. Compara la media entre las 3 variables de <mark style="color:purple;">`Rendimiento_MCAR, Rendimiento_MAR y Rendimiento_MNAR`</mark> con <mark style="color:purple;">`Rendimiento_completo`</mark>. ¿Qué observas?
3. Si calculamos los coeficientes de correlación y una regresión simple de cada una de las 3 variables <mark style="color:purple;">`Rendimiento_MCAR, Rendimiento_MAR y Rendimiento_MNAR`</mark> para predecir el <mark style="color:purple;">`IQ`</mark>. ¿Qué diferencias observas en comparación con <mark style="color:purple;">`Rendimiento_completo`</mark>?&#x20;
4. Haz un gráfico para ver como se ajusta la recta de regresión en cada uno de los casos.
5. Imputa por la media y por el modelo de regresión simple las tres variables rendimiento y compara los resultados.&#x20;