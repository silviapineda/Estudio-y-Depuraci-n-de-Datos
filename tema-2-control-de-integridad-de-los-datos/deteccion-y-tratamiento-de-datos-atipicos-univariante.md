---
cover: >-
  ../.gitbook/assets/Introducing-Automated-Time-Series-Anomaly-Detection_blog_Image_v.1.0.webp
coverY: 0
---

# Detección y tratamiento de datos atípicos (univariante)

Los datos atípicos los estudiaremos dentro del contexto de todas las variables. A veces, un valor extremo no es significativo dentro de un conjunto de variables. Por lo tanto, debemos abordar el problema desde el punto de vista **univariante y multivariante**.&#x20;

## Enfoque univariante&#x20;

Para una variable continua, un **dato atípico** se determina por aquellas observaciones que caigan fuera de 1.5\*IQR y un **dato extremo** para los que esta distancia sea superior a 3 veces el IQR, siendo IQR el Rango Intercuartílico que es la diferencia entre el cuartil tercero y el cuartil primero.&#x20;

$$
IQR=Q3-Q1
$$

Es decir, aquellos puntos que están fuera de los bigotes de un diagrama de cajas. Esto se le llama **regla de Tukey**.

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
<strong>
</strong>
###Los valores atípicos son:
outlier_values &#x3C;- boxplot.stats(data$Pressure_height)$out  # outlier values.
out_ind &#x3C;- which(data$Pressure_height %in% c(outlier_values))

###Los valores extremos son:
extreme_values &#x3C;- boxplot.stats(data$Pressure_height,coef=3)$out  # extreme values.
ext_ind &#x3C;- which(data$Pressure_height %in% c(extreme_values))
<strong>
</strong>
</code></pre>

<figure><img src="../.gitbook/assets/image (248).png" alt="" width="501"><figcaption></figcaption></figure>

La decisión de que hacer es más compleja, no siempre hay que borrar los datos atípicos o extremos, a veces basta con que seamos conscientes y en el posterior análisis tengamos en cuenta la influencia de los mismos. Otras veces dependerá del contexto del estudio y lo que queramos hacer después.&#x20;

Además tenemos que tener en cuenta la proporción de datos atípicos o extremos en la muestra, es decir, **si los datos representan menos de un 2-5% de la muestra**, podríamos considerar borrarlos, si son más entonces los consideramos datos "típicos".

```r
####Miramos la proporción de outliers
length(out_ind)/length(data$Pressure_height)*100
[1] 2.463054
```

**En este ejemplo nos salen que los datos atípicos de la variable pressure height corresponden a un 2.5% de los datos, por tanto ya que son sólo atípicos y no extremos y están por encima del 2% vamos a dejarlos sin borrar pero siendo conscientes en futuros análisis de su existencia.**&#x20;

Además también es importante tener en cuenta si son datos aislados o son parte de una distribución un poco asimétrica. Si hacemos un histograma podemos ver un poco más si es solo la distribución que es asimétrica

```r
hist(data$Pressure_height)
```

<figure><img src="../.gitbook/assets/image (250).png" alt=""><figcaption></figcaption></figure>

Empieza a parecer que más bien corresponde a una distribución asimétrica

