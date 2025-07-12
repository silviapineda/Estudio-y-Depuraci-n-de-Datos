---
cover: >-
  ../.gitbook/assets/Introducing-Automated-Time-Series-Anomaly-Detection_blog_Image_v.1.0.webp
coverY: 0
---

# Detección y tratamiento de datos atípicos (univariante)

Los datos atípicos deben analizarse **teniendo en cuenta todas las variables disponibles**. Un valor que podría parecer inusual si se analiza de forma aislada (por ejemplo, una edad de 95 años) puede ser completamente coherente cuando se considera junto a otras variables (como el estado de salud, el entorno residencial, etc.). Por eso es importante **combinar el enfoque univariante con el enfoque multivariante**.

## Enfoque univariante&#x20;

Este enfoque analiza **una sola variable** a la vez para detectar valores que se alejan de lo esperado.

📐 **Regla de Tukey**

Para variables continuas, una de las reglas más comunes es la **regla de Tukey**, basada en el **rango intercuartílico (IQR)**, que mide la dispersión de los valores centrales de una distribución.

* El **IQR** se define como la diferencia entre el **tercer cuartil (Q3)** y el **primer cuartil (Q1)**:

$$
\text{IQR} = Q3 - Q1
$$

A partir de este valor, se definen dos umbrales:

* **Atípico (outlier):**\
  Un valor se considera atípico si está fuera del rango:

$$
[Q1−1.5×IQR,  Q3+1.5×IQR
$$

* **Extremo:**\
  Un valor se considera extremo si está aún más alejado:

$$
[Q1−3×IQR,  Q3+3×IQR]
$$

Esta regla se representa visualmente en el **diagrama de cajas** (_boxplot_), donde:

* Los "bigotes" del diagrama terminan en los límites definidos por ±1.5 × IQR.
* Los puntos fuera de los bigotes se marcan como atípicos (normalmente con un punto o un asterisco).

<figure><img src="../.gitbook/assets/image (79).png" alt=""><figcaption></figcaption></figure>

Vamos a utilizar la base de datos <mark style="color:orange;">**`ozone`**</mark> de R para ilustrar el tema

{% file src="../.gitbook/assets/ozone (5).csv" %}

Lo primero sería ver el tipo de datos y que no haya errores e incongruencia como hemos visto en el [Tema 1](broken-reference).

```r
data <- read.csv("ozone.csv")  # import data
str(data)
summary(data)

data$Month<-as.factor(data$Month)
data$Day_of_month<-as.factor(data$Day_of_month)
data$Day_of_week<-as.factor(data$Day_of_week)
```

**Vamos a ver los outliers**:&#x20;

Vamos a estudiar la variable <mark style="color:purple;">`Pressure_height`</mark> obteniendo los datos atípicos y extremos:

<pre class="language-r"><code class="lang-r">boxplot(data$Pressure_height)

ggplot(data, aes(y = Pressure_height)) +
  geom_boxplot(fill = "skyblue", outlier.color = "red", outlier.shape = 16)


###Los valores atípicos son:
outlier_values &#x3C;- boxplot.stats(data$Pressure_height)$out  # outlier values.
out_ind &#x3C;- which(data$Pressure_height %in% c(outlier_values))

###Los valores extremos son:
extreme_values &#x3C;- boxplot.stats(data$Pressure_height,coef=3)$out  # extreme values.
ext_ind &#x3C;- which(data$Pressure_height %in% c(extreme_values))
<strong>
</strong>
</code></pre>

<figure><img src="../.gitbook/assets/image (248).png" alt="" width="334"><figcaption></figcaption></figure>

La decisión de que hacer es más compleja, no siempre hay que borrar los datos atípicos o extremos, a veces basta con que seamos conscientes y en el posterior análisis tengamos en cuenta la influencia de los mismos. Otras veces dependerá del contexto del estudio y lo que queramos hacer después.&#x20;

Además tenemos que tener en cuenta la proporción de datos atípicos o extremos en la muestra, es decir, **si los datos representan menos de un 2-5% de la muestra**, podríamos considerar borrarlos, si son más entonces los consideramos datos "típicos".

```r
####Miramos la proporción de outliers
length(out_ind)/length(data$Pressure_height)*100
[1] 2.463054
```

**En este ejemplo nos salen que los datos atípicos de la variable pressure height corresponden a un 2.5% de los datos, por tanto los considerarenos atípicos y pasarán a ser estudiados de forma bivariante en el siguiente apartado.**

Además también es importante tener en cuenta si son datos aislados o son parte de una distribución un poco asimétrica. Si hacemos un histograma podemos ver un poco más si es solo la distribución que es asimétrica:

```r
hist(data$Pressure_height)
```

<figure><img src="../.gitbook/assets/image (250).png" alt=""><figcaption></figcaption></figure>

En este caso, vemos como los valores atípicos corresponden a una distribución asimétrica y seguramente en el estudio bivariante encontremos alguna asociación y terminemos no borrando los datos.&#x20;
