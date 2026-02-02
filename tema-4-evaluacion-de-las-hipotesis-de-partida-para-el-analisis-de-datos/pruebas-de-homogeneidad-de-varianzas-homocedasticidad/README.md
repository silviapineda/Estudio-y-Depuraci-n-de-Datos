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

### <mark style="color:orange;">Ejemplo</mark>

Vamos a ver un ejemplo con los datos de R <mark style="color:orange;">**`iris`**</mark>

```r
#Cargamos los datos de iris
data("iris")

#Filtramos para tener solo dos grupos por facilidad
library(dplyr)
data <- filter(iris, Species %in% c("versicolor", "virginica"))
data$Species<-factor(data$Species)
```

Estudiaremos la variable Petal.Length

```r
#Primero ver NORMALIDAD
hist(data$Petal.Length)
shapiro.test(data$Petal.Length)
```

<figure><img src="../../.gitbook/assets/image (4).png" alt="" width="375"><figcaption></figcaption></figure>

```r
##Segundo ver su comportamiento por la variable factor
boxplot(data$Petal.Length~data$Species)
hist(data$Petal.Length[which(data$Species=="versicolor")])
hist(data$Petal.Length[which(data$Species=="virginica")])
by(data$Petal.Length, data$Species, var)
```

```r
data$Species: versicolor
[1] 0.2208163
---------------------------------------------------------------------------
data$Species: virginica
[1] 0.3045878
```

A nivel gráfico y según el test de Shapiro, la variable Petal.Length muestra un comportamiento compatible con normalidad dentro de cada grupo, lo que permite aplicar el F-test para comparar varianzas.

```r
##Finalmentet aplicamos el test de la razón de varianzas
var.test(x = data[data$Species == "versicolor", "Petal.Length"],         
         y = data[data$Species == "virginica", "Petal.Length"] )
```

```r
	F test to compare two variances

data:  data[data$Species == "versicolor", "Petal.Length"] and data[data$Species == "virginica", "Petal.Length"]
F = 0.72497, num df = 49, denom df = 49, p-value = 0.2637
alternative hypothesis: true ratio of variances is not equal to 1
95 percent confidence interval:
 0.411402 1.277530
sample estimates:
ratio of variances 
         0.7249678 
```

El F-test no detecta diferencias significativas entre las varianzas (p > 0.05), por lo que podemos asumir homogeneidad de varianza entre los dos grupos.

## Test de Levene

El test de Levene se basa en comparar las desviaciones absolutas de las observaciones de cada grupo con su respectiva media o mediana, dependiendo de la variante del test.&#x20;

El estadístico de prueba se calcula como la relación de la media de las desviaciones absolutas de cada observación respecto a la media o mediana del grupo, dependiendo de la variante del test.

* **Para la versión basada en la medi**a, el estadístico se calcula como la suma de las desviaciones absolutas respecto a la media de cada grupo.
* **Para la versión basada en la mediana**, el estadístico se calcula como la suma de las desviaciones absolutas respecto a la mediana de cada grupo.

El estadístico de prueba sigue aproximadamente una distribución $$χ^2$$ con $$k−1$$ grados de libertad bajo la hipótesis nula de igualdad de varianzas entre los grupos.

El test de Levene se puede aplicar con la función <mark style="color:green;">**`leveneTest()`**</mark> del paquete <mark style="color:green;">**`car`**</mark>. Se caracteriza, por poder comparar 2 o más poblaciones y por permitir elegir entre diferentes estadísticos de centralidad: mediana (por defecto), media, media truncada. Esto es importante a la hora de contrastar la homocedasticidad dependiendo de si los grupos se distribuyen de forma normal o no.

```r
library(car)
leveneTest(y = data$Petal.Length, group = data$Species, center = "median")
```

```r
Levene's Test for Homogeneity of Variance (center = "median")
      Df F value Pr(>F)
group  1  1.0674 0.3041
      98               
```

El test de Levene no detecta diferencias significativas entre varianzas (p = 0.3041); se asume homogeneidad de varianza.

## Test de Bartlett

Permite contrastar la igualdad de varianza en 2 o más poblaciones sin necesidad de que el tamaño de los grupos sea el mismo. Es más sensible que el test de _Levene_ a la falta de normalidad, pero si se está seguro de que los datos provienen de una distribución normal, es la mejor opción.

```r
a <- iris[iris$Species == "versicolor", "Petal.Length"]
b <- iris[iris$Species == "virginica", "Petal.Length"]
bartlett.test(list(a,b))
```

```r
	Bartlett test of homogeneity of variances

data:  list(a, b)
Bartlett's K-squared = 1.249, df = 1, p-value = 0.2637
```

Bartlett no detecta diferencias en las varianzas (p = 0.2637); se asume homogeneidad de varianza

Ahora lo aplicamos a los 3 grupos a la vez

```r
##Ploteamos las 3 
boxplot(iris$Petal.Length~iris$Species)
by(iris$Petal.Length, iris$Species, var)
bartlett.test(iris$Sepal.Length ~ iris$Species)
```

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

```r

	Bartlett test of homogeneity of variances

data:  iris$Sepal.Length by iris$Species
Bartlett's K-squared = 16.006, df = 2, p-value = 0.0003345
```

El boxplot sugiere diferencias de variabilidad entre especies y el test de Bartlett rechaza claramente la hipótesis de igualdad de varianzas (p < 0.001). Esto indica que _<mark style="color:purple;">`Sepal.Length`</mark>_ presenta **varianzas significativamente distintas entre las tres especies**, por lo que no se puede asumir homogeneidad de varianzas.

{% hint style="info" %}
Si se tiene la seguridad de que las muestras proceden de poblaciones normalmente distribuidas, pueden emplearse el **F-test** y el **test de Bartlett**, siendo este último más recomendable porque el F-test es muy potente pero demasiado sensible a pequeñas desviaciones de la normalidad.<br>

En cambio, si no se puede asumir normalidad en las poblaciones de origen, es preferible utilizar el **test de Levene** (especialmente en su versión basada en la mediana), ya que es más robusto frente a distribuciones no normales.
{% endhint %}
