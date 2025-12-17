---
cover: >-
  ../.gitbook/assets/Introducing-Automated-Time-Series-Anomaly-Detection_blog_Image_v.1.0.webp
coverY: 0
---

# Detección y tratamiento de datos atípicos (bivariante)

Seguimos con la misma base de datos <mark style="color:orange;">**`ozone`**</mark> de R para ilustrar el tema.

## Enfoque bivariante

El caso bivariado, nos permite representar una variable en función de otra. La idea es ver si los "outliers" encontrados en el estudio univariante son parte de una asociación (no serán outliers) o en cambio son datos aislados (si serán outliers).&#x20;

Lo vamos a hacer de forma gráfica teniendo en cuenta el tipo de variables:

* Variable Cuantitativa con Variable Cualitativa ⇒ Diagrama de cajas (boxplot)
* Variable Cuantitativa con Variable Cuantitativa ⇒ Diagrama de dispersión (scatterplot)

### **Variable cuantitativa con variable cualitativa**

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

<figure><img src="../.gitbook/assets/image (272).png" alt=""><figcaption></figcaption></figure>

En el gráfico de la izquierda se representa la variable <mark style="color:purple;">`Pressure_height`</mark> por la variable <mark style="color:purple;">`Month`</mark>,  y en el de la derecha por la variable <mark style="color:purple;">`Day_of_week`</mark>. En este caso, vemos que la variable <mark style="color:purple;">`Pressure_height`</mark> tiene relación con la variable <mark style="color:purple;">`Month`</mark>, a diferencia de la variable <mark style="color:purple;">`Day_of_week`</mark> con la que no se observa ninguna relación.  Con la primera, vemos como los meses de invierno son los que toman valores más bajos y los de verano más altos, siendo los outliers que detectamos en el estudio univariante parte de esta asociación.  Conclusión,  ya no queda claro si realmente los datos atípicos de la variable <mark style="color:purple;">`Pressure_heigt`</mark> eran realmente atípicos o simplemente son parte de la asociación observada con <mark style="color:purple;">`Month`</mark>. &#x20;

### **Variable cuantitativa con variable cuantitativa**

Cuando tenemos dos variables continuas, lo que podemos hacer es dibujar un diagrama de dispersión y ver si dichas variables podrían tener datos atípicos

Para este caso vamos a ver si la variable objetivo que estamos estudiando (Y)  <mark style="color:purple;">`Pressure_height`</mark>  tiene relación con las variables  explicativas (X) de temperatura <mark style="color:purple;">`Temperature_Sandburg`</mark> y <mark style="color:purple;">`Temperature_ElMonte`</mark>

```r
ggp <- ggplot(data,aes(y = Pressure_height, x = Temperature_Sandburg)) + geom_point()
ggp + stat_smooth(method = "lm",
                  formula = y ~ x,
                  geom = "smooth")

summary(lm(data$Pressure_height~data$Temperature_Sandburg))

```

<figure><img src="../.gitbook/assets/image (19).png" alt="" width="563"><figcaption></figcaption></figure>

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

En este diagrama de dispersión con un ajuste de una recta de regresión lineal, vemos como los posibles outliers de la variable <mark style="color:purple;">`Pressure_height`</mark> están relacionados con la temperatura, de forma que los valores  bajos de <mark style="color:purple;">`Pressure_heigth`</mark> (incluídos los valores atípicos) se relacionan con niveles muy bajitos de temperatura como veáimos también con los meses de inviernos, por tanto, **¿Son datos realmente atípicos?**

**Considerando lo ya visto en el caso univariante y esto,  estos datos no los vamos a considerar como atípicos, ya que son parte de una asociación estadística en la que vemos con los valores atípicos de la variable&#x20;**<mark style="color:purple;">**`pressure_height`**</mark>**&#x20;se relacionan con los meses de inviernos donde la temperatura en inferior. Por tanto, no vamos a hacer nada y no los borraremos.**&#x20;

### Resumen para la detección de atípicos

1. Representar el diagrama de cajas de la variable en cuestión y calcular los valores atípicos y extremos como valores que se alejan de  1.5\*IQR (atípico) y de 3\*IQR (extremo).
2. Representar su histograma para ver la distribución de la variable en cuestión, si es asimétrica o son datos realmente diferenciados de la distribución general. &#x20;
3. Para poder ser considerados datos atípicos o extremos deben representar menos del 2-5% del conjunto de datos porque sino se trataría de datos "típicos".
4. Se debe realizar un estudio bivariado para ver si esos datos atípicos son parte de una asociación estadística o no. Si lo son, no los consideraremos atípicos.&#x20;

