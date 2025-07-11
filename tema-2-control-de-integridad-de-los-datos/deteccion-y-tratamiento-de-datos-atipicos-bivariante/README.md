---
cover: >-
  ../../.gitbook/assets/Introducing-Automated-Time-Series-Anomaly-Detection_blog_Image_v.1.0.webp
coverY: 0
---

# Detección y tratamiento de datos atípicos (bivariante)

Seguimos con la misma base de datos <mark style="color:orange;">**`ozone`**</mark> de R para ilustrar el tema

{% file src="../../.gitbook/assets/ozone (5).csv" %}

## Enfoque bivariante

El caso bivariado, nos permite representar una variable en función de otra. Por ejemplo, en el caso de una variable continua en función de una variable categórica, volvemos a hacer uso del diagrama de cajas.

### **Variable continua con variable categórica**

Ahora vamos a ver si la variable <mark style="color:purple;">`Pressure_height`</mark>  toma valores atípicos de la misma forma que antes al representarla por meses (<mark style="color:purple;">`Month`</mark>) y días de la semana (<mark style="color:purple;">`Day_of_week`</mark>):

<pre class="language-r"><code class="lang-r">library(ggplot2)
library(patchwork)

<strong># Gráfico 1: Pressure Height por mes
</strong>p1 &#x3C;- ggplot(data, aes(x = as.factor(Month), y = Pressure_height)) +
  geom_boxplot(fill = "lightblue") +
  labs(title = "Pressure Height across months", x = "Month", y = "Pressure Height") 

# Gráfico 2: Pressure Height por día de la semana
p2 &#x3C;- ggplot(data, aes(x = as.factor(Day_of_week), y = Pressure_height)) +
  geom_boxplot(fill = "lightgreen") +
  labs(title = "Pressure Height for days of week", x = "Day of Week", y = "Pressure Height") 

# Combinar ambos gráficos en una fila
p1 + p2

</code></pre>

<figure><img src="../../.gitbook/assets/image (252).png" alt=""><figcaption></figcaption></figure>

En el gráfico primero, donde se representa la variable <mark style="color:purple;">`Pressure_height`</mark> por la variable <mark style="color:purple;">`Month`</mark>, sólo 3 atípicos son detectados que no son los mismos que se detectaban en la variable en cuestión, y en el caso de la representación por la variable <mark style="color:purple;">`Day_of_week`</mark>, se detectan unos cuantos más, aquí si coincidiendo con la variable en cuestión. El problema aquí es que además de los datos atípicos que nos muestran los diagramas de cajas, tenemos que ver si existe relación entre dichas variables. En este caso, parece que la variable <mark style="color:purple;">`Pressure_height`</mark> tiene relación con la variable <mark style="color:purple;">`Month`</mark>, siendo los meses de invierno los que toman valores más bajos y los de verano más altos y por tanto ya no queda claro si realmente los datos atípicos de la variable <mark style="color:purple;">`Pressure_heigt`</mark> eran realmente atípicos o simplemente son parte de la asociación observada con <mark style="color:purple;">`Month`</mark>. &#x20;

### **Variable continua con variable continua**

Cuando tenemos dos variables continuas, lo que podemos hacer es dibujar un diagrama de dispersión y ver como veíamos en el ejemplo ilustrativo si dichas variables podrían tener datos atípicos

Para este caso vamos a ver <mark style="color:purple;">`Pressure_height`</mark> con las variables de temperatura <mark style="color:purple;">`Temperature_Sandburg`</mark> y <mark style="color:purple;">`Temperature_ElMonte`</mark>

<pre class="language-r"><code class="lang-r"><strong>ggp &#x3C;- ggplot(data,aes(Pressure_height, Temperature_Sandburg)) + geom_point()
</strong>ggp + stat_smooth(method = "lm",
                  formula = y ~ x,
                  geom = "smooth")

summary(lm(data$Pressure_height~data$Temperature_Sandburg))

</code></pre>

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

```r
Call:
lm(formula = data$Pressure_height ~ data$Temperature_Sandburg)

Residuals:
     Min       1Q   Median       3Q      Max 
-196.559  -41.846    1.171   39.099  175.891 

Coefficients:
                           Estimate Std. Error t value Pr(>|t|)    
(Intercept)               5354.1021    20.8228  257.13   <2e-16 ***
data$Temperature_Sandburg    6.4152     0.3319   19.33   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 67.02 on 201 degrees of freedom
Multiple R-squared:  0.6502,	Adjusted R-squared:  0.6484 
F-statistic: 373.6 on 1 and 201 DF,  p-value: < 2.2e-16

```

Aquí volvemos a ver los posibles outliers de la variable <mark style="color:purple;">`Pressure_height`</mark>, pero también vemos como esta variable se asocia con las variables de temperatura siendo que los valores que se veían como atípicos en el diagrama de cajas primero, se relacionan con niveles muy bajitos de temperatura como podíamos pensar de los meses de inviernos, por tanto, **¿Son datos realmente atípicos?**

**Considerando lo ya visto en el caso univariante y esto, vamos a dejar estos datos sin tocar, pero si en futuros análisis nos dan problemas o detectamos cosas "raras" recordaremos que estos datos podrían ser atípicos.**&#x20;

### Resumen para la detección de atípicos

1. Representar el diagrama de cajas de la variable en cuestión y calcular los valores atípicos y extremos como valores que se alejan de  1.5\*IQR (atípico) y de 3\*IQR (extremo).&#x20;
2. Para poder ser considerados datos atípicos o extremos deben representar menos del 2-5% del conjunto de datos porque sino se trataría de datos "típicos".
3. Se debe realizar un estudio bivariado para ver si esos datos atípicos son parte de una asociación estadística o no. Si lo son, no los consideraremos atípicos.&#x20;

