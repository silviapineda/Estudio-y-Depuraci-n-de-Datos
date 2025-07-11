---
cover: ../../.gitbook/assets/Untitled.png
coverY: 0
---

# Pruebas de homogeneidad de varianzas (homocedasticidad)

El supuesto de **homogeneidad de varianzas**, también conocido como supuesto de **homocedasticidad**, considera que la varianza es constante (no varía) en los diferentes niveles de un factor, es decir, entre diferentes grupos.

A la hora de realizar contrastes de hipótesis o intervalos de confianza, cuando los tamaños de cada grupo son muy distintos ocurre que:

* **Si los grupos con tamaños muestrales pequeños son los que tienen mayor varianza**, la probabilidad real de cometer un error de tipo I en los contrastes de hipótesis será menor de lo que se obtiene al hacer el test. En los intervalos, los límites superior e inferior reales son menores que los que se obtienen. **La inferencia será por lo general más conservadora**.
* Si por el contrario, son **los grupos con tamaños muestrales grandes los que tienen mayor varianza**, entonces se tendrá el efecto contrario y l**as pruebas serán más liberales**. Es decir, la probabilidad real de cometer un error de tipo I es mayor que la devuelta por el test y los intervalos de confianza verdaderos serán más amplios que los calculados.

<table data-header-hidden><thead><tr><th width="232"></th><th></th><th></th></tr></thead><tbody><tr><td></td><td><span class="math">H_0</span> es cierta</td><td><span class="math">H_0</span> es falsa</td></tr><tr><td>Se rechaza <span class="math">H_0</span> </td><td>Error de tipo I (<span class="math">\alpha</span>)</td><td>Decisión correcta</td></tr><tr><td>No se rechaza <span class="math">H_0</span> </td><td>Decisión correcta</td><td>Error de tipo II (<span class="math">\beta</span>)</td></tr></tbody></table>

Existen diferentes test que permiten evaluar la distribución de la varianza. Todos ellos consideran el siguiente contraste de hipótesis:

$$
H_0: \text{La varianza entre los grupos es igual.}  \\  H_1: \text{La varianza entre los grupos no es igual.}
$$

La diferencia entre ellos es el estadístico de centralidad que utilizan:

* Los test que trabajan con la media de la varianza son los más potentes cuando las poblaciones que se comparan se distribuyen de forma normal.
* Utilizar la media truncada mejora el test cuando los datos siguen una distribución de Cauchy (colas grandes).
* La mediana consigue mejorarlo cuando los datos siguen una distribución asimétrica.

Por lo general, si no se puede alcanzar cierta seguridad de que las poblaciones que se comparan son de tipo normal, es recomendable recurrir a test que comparen la mediana de la varianza.

## F-test (razón de varianzas)

El F-test, también conocido como contraste de la razón de varianzas, contrasta la hipótesis nula de que dos poblaciones normales tienen la misma varianza.&#x20;

Es muy potente, detecta diferencias muy sutiles, pero es muy sensible a violaciones de la normalidad de las poblaciones. Por esta razón, no es un test recomendable si no se tiene mucha certeza de que las poblaciones se distribuyen de forma normal.

El F-test estudia el cociente de varianzas $$σ^2_1/σ^2_2$$, que en caso de que sean iguales, toma el valor 1.

El estadístico _F_ empleado sigue una distribución de _F-Snedecor_:\
$$F = \frac{\sigma^2_2}{\sigma^2_1} \times \frac{S^2_1}{S^2_2} \sim F_{n_1-1, n_2-1}$$

* $$σ_1^2​$$ y $$σ_2^2​$$: Las varianzas poblacionales de las dos muestras independientes.
* $$S_1^2​$$ y $$S_2^2​$$: Las varianzas muestrales de las dos muestras independientes.
* $$n_1​$$ y $$n_2​$$: Los tamaños de muestra de las dos muestras independientes.

En R usaremos la función <mark style="color:green;">**`var.test()`**</mark>&#x20;

```r
#Cargamos los datos de iris
data("iris")
#Filtramos para tener solo dos grupos por facilidad

data <- filter(.data = iris, Species %in% c("versicolor", "virginica"))
data$Species<-factor(data$Species)

##Vamos a estudiar la variable Petal.Length
#Primero ver NORMALIDAD
hist(data$Petal.Length)
shapiro.test(data$Petal.Length)

##Segundo ver su comportamiento por la variable factor
boxplot(data$Petal.Length~data$Species)
hist(data$Petal.Length[which(data$Species=="versicolor")])
hist(data$Petal.Length[which(data$Species=="virginica")])
by(data$Petal.Length, data$Species, var)

##Finalmentet aplicamos el test de la razón de varianzas
var.test(x = data[data$Species == "versicolor", "Petal.Length"],         
         y = data[data$Species == "virginica", "Petal.Length"] )
```

<figure><img src="../../.gitbook/assets/image (234).png" alt=""><figcaption></figcaption></figure>

El test no encuentra diferencias significativas entre las varianzas de los dos grupos.&#x20;

## Test de Levene

El test de Levene se basa en comparar las desviaciones absolutas de las observaciones de cada grupo con su respectiva media o mediana, dependiendo de la variante del test.&#x20;

El estadístico de prueba se calcula como la relación de la media de las desviaciones absolutas de cada observación respecto a la media o mediana del grupo, dependiendo de la variante del test.

* Para la versión basada en la media, el estadístico se calcula como la suma de las desviaciones absolutas respecto a la media de cada grupo.
* Para la versión basada en la mediana, el estadístico se calcula como la suma de las desviaciones absolutas respecto a la mediana de cada grupo.

El estadístico de prueba sigue aproximadamente una distribución $$χ^2$$ con $$k−1$$ grados de libertad bajo la hipótesis nula de igualdad de varianzas entre los grupos.

El test de Levene se puede aplicar con la función <mark style="color:green;">**`leveneTest()`**</mark> del paquete `car`. Se caracteriza, además de por poder comparar 2 o más poblaciones, por permitir elegir entre diferentes estadísticos de centralidad: mediana (por defecto), media, media truncada. Esto es importante a la hora de contrastar la homocedasticidad dependiendo de si los grupos se distribuyen de forma normal o no.

```r
library(car)
leveneTest(y = data$Petal.Length, group = data$Species, center = "median")
```

<figure><img src="../../.gitbook/assets/image (235).png" alt="" width="563"><figcaption></figcaption></figure>

## Test de Bartlett

Permite contrastar la igualdad de varianza en 2 o más poblaciones sin necesidad de que el tamaño de los grupos sea el mismo. Es más sensible que el test de _Levene_ a la falta de normalidad, pero si se está seguro de que los datos provienen de una distribución normal, es la mejor opción.

```r
data("iris")
a <- iris[iris$Species == "versicolor", "Petal.Length"]
b <- iris[iris$Species == "virginica", "Petal.Length"]
bartlett.test(list(a,b))
```

El test no encuentra diferencias significativas entre las varianzas de los dos grupos.

Si se aplica a los 3 grupos a la vez, sí hay evidencias de que la varianza no es la misma en todos ellos. Idea que se puede intuir a partir del tamaño de la caja y bigotes del boxplot.

```r
##Ploteamos las 3 
boxplot(iris$Petal.Length~iris$Species)
by(iris$Petal.Length, iris$Species, var)
bartlett.test(iris$Sepal.Length ~ iris$Species)
```

<figure><img src="../../.gitbook/assets/image (236).png" alt="" width="563"><figcaption></figcaption></figure>

{% hint style="info" %}
Si se tiene seguridad de que las muestras a comparar proceden de poblaciones que siguen una distribución normal, son recomendables el _F-test_ y el _test de Bartlet_, pareciendo ser el segundo más recomendable ya que el primero es muy potente pero extremadamente sensible a desviaciones de la normal. Si no se tiene la seguridad de que las poblaciones de origen son normales, se recomiendan el _test de Leven_ utilizando la mediana.
{% endhint %}
