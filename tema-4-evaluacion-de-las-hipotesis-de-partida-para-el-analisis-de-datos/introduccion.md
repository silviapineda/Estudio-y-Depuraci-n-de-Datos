---
cover: ../.gitbook/assets/Untitled.png
coverY: 0
coverHeight: 293
---

# Introducción

En los temas anteriores nos hemos centrado en dejar los datos “en condiciones” para poder analizarlos: primero describiendo y explorando la información disponible, después detectando y tratando valores atípicos, duplicados y errores, y por último abordando el problema de los valores perdidos y su imputación. Sin embargo, incluso cuando los datos parecen limpios, muchos métodos estadísticos clásicos (contrastes de hipótesis, modelos de regresión, ANOVA, etc.) se apoyan en una serie de **hipótesis de partida** que no siempre se cumplen de forma automática.

Llamamos **hipótesis de partida** (o supuestos del modelo) a las condiciones que asumimos sobre cómo se han generado los datos: distribución aproximada de las variables, comportamiento de las varianzas, independencia entre observaciones, linealidad en ciertas relaciones, etc. Si las hipótesis de partida no se cumplen de forma razonable, los resultados (p-valores, intervalos de confianza, conclusiones) pueden ser engañosos.

En la práctica, cuando usamos métodos paramétricos habituales como:

* la **t de Student** para comparar medias,
* el **ANOVA** para comparar varios grupos,
* la **regresión lineal** para estudiar la relación entre una variable respuesta y una o varias explicativas,

estamos asumiendo, entre otras cosas, que las variables cuantitativas siguen una distribución aproximadamente normal y que las varianzas de los grupos son similares (homocedasticidad). En muestras pequeñas o moderadas, estas hipótesis tienen un papel especialmente importante.

En este tema nos centraremos en dos de las hipótesis de partida más frecuentes en el análisis de datos con variables cuantitativas:

1. **Normalidad** de las distribuciones (o, al menos, ausencia de desviaciones extremas respecto a la normalidad).
2. **Homogeneidad de varianzas** entre grupos (homocedasticidad).

