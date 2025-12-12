---
cover: >-
  ../../.gitbook/assets/Introducing-Automated-Time-Series-Anomaly-Detection_blog_Image_v.1.0.webp
coverY: 0
---

# Introducción

Una vez que conocida la estructura del conjunto de datos y corregido los problemas básicos de tipos y codificación, el siguiente paso es comprobar su integridad, es decir, si los valores y registros son coherentes con el fenómeno que estamos estudiando. En esta fase suelen aparecer dos problemas muy comunes: **valores atípicos** que distorsionan los resultados y **duplicados** que inflan artificialmente la muestra y sesgan estimaciones y modelos.

Empecemos por definir un **dato atípico**, llamado _outlier_ en inglés. Un dato atípico es un valor de una de las variables que se observan en un conjunto de datos, que se diferencia de forma notable de los valores que toma dicha variable. También podemos considerar que los datos atípicos son observaciones cuyo comportamiento es distinto del comportamiento medio del resto de observaciones.&#x20;

El problema de los datos atípicos es que distorsionan de forma importante los análisis en los que interviene la variable que contiene dichos datos.

Es muy importante no confundir un dato atípico con un dato erróneo. Por ejemplo una persona que mida 2.1 metros, aunque poco frecuente, no tiene porque tratarse de valor erróneo, pues se trata de un valor válido, y será un dato atípico si se compara con otros individuos de menor altura, pero no si forma parte de un conjunto de datos de jugadores de baloncesto.  La diferencia no la decide un umbral automático, sino la combinación de **contexto, exploración gráfica y criterios estadísticos**. Por eso, en este tema trabajaremos con una lógica de decisión: detectar → interpretar → actuar (corregir, excluir, o conservar justificadamente).

Es importante tratar primero los errores como hemos visto en el [Tema 1](/broken/pages/otB3m10mPPVd8kmdfZ1t) y luego los datos atípicos como vamos a ver en este tema. En este tema abordaremos:

1. Detección y tratamiento de atípicos **univariante y bivariante** (gráficos + reglas cuantitativas).
2. Detección de atípicos **multivariante** (cuando lo “raro” aparece en la combinación de variables, por ejemplo con LOF).
3. Identificación y tratamiento de **duplicados**, diferenciando duplicados exactos y duplicados “por clave” (IDs).

<mark style="color:orange;">**Ejemplo 1**</mark>

Supongamos que en una pequeña empresa hay 6 empleados con los siguientes salarios mensuales (en euros):

1,800, 1,900, 2,000, 2,100, 2,200, 2,300

#### Si calculamos la media obtenemos:

$$
\text{Media} = \frac{1800 + 1900 + 2000 + 2100 + 2200 + 2300}{6} = \frac{12300}{6} = 2050
$$

#### Si calculamos su mediana

$$
\text{Mediana} = \frac{2000 + 2100}{2} = 2050
$$

Hasta aquí, la media y la mediana coinciden.

#### **Introducimos un dato atípico**

Ahora supongamos que el dueño de la empresa también incluye su propio salario de **20,000€** en la lista:

1,800, 1,900, 2,000, 2,100, 2,200, 2,300, **20,000**

#### **Nueva media**:

$$
\text{Media} = \frac{1800 + 1900 + 2000 + 2100 + 2200 + 2300 + 20000}{7} = \frac{32200}{7} \approx 4600
$$

#### **Nueva mediana**:

$$
\text{Mediana} = 2100
$$

La **media** ha aumentado drásticamente de **2050** a **4600**, lo que da una impresión errónea de los salarios reales de los empleados.

La **mediana** apenas ha cambiado (de **2050** a **2100**), lo que indica que la mayoría de los empleados sigue ganando un salario similar.

Este ejemplo muestra que la **media es muy sensible a los datos atípicos**, mientras que la **mediana es más robusta** y representa mejor la tendencia central en presencia de valores extremos.

<mark style="color:orange;">**Ejemplo 2**</mark>

No siempre vamos a observar unos datos tan dispares, pero aun así podemos ver que un dato atípico  puede tener un gran impacto en nuestros estudios.

Imagínate que queremos predecir el tiempo que tarda en recorrer una persona 1.5 millas según la cantidad de oxígeno que absorve por minuto.&#x20;

En un grupo de 10 personas, se observan para cada una de ellas las siguientes variables:

<mark style="color:purple;">`Tiempo`</mark>: tiempo que tarda en recorrer 1.5 millas (en minutos)

<mark style="color:purple;">`Oxigeno`</mark>: cantidad de oxígeno que absorve por minuto (en ml. por Kg de peso)

```r
Tiempo=c(11.37,10.07,8.65,8.17,9.22,11.63,11.95,10.85,13.08,8.63)
Oxigeno=c(44.609,45.313,54.297,59.571,49.874,44.811,45.681,49.091,39.442,60.055)
data<-data.frame(Tiempo,Oxigeno)
```

Ajustamos una recta de regresión a estos datos

```r
summary(lm(data$Tiempo~data$Oxigeno))
```

```r
Call:
lm(formula = data$Tiempo ~ data$Oxigeno)

Residuals:
    Min      1Q  Median      3Q     Max 
-1.1729 -0.4537  0.1866  0.5104  0.7889 

Coefficients:
             Estimate Std. Error t value Pr(>|t|)    
(Intercept)  21.31982    1.80557  11.808 2.42e-06 ***
data$Oxigeno -0.22238    0.03633  -6.121 0.000283 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.74 on 8 degrees of freedom
Multiple R-squared:  0.824,	Adjusted R-squared:  0.802 
F-statistic: 37.46 on 1 and 8 DF,  p-value: 0.000283
```

$$
Tiempo = 21.32 - 0.2224*Oxigeno
$$

Y lo representamos gráficamente

```r
library(ggplot2)
ggp <- ggplot(data,aes(Oxigeno, Tiempo)) + geom_point()
ggp + stat_smooth(method = "lm",formula = y ~ x, geom = "smooth")
```

<figure><img src="../../.gitbook/assets/image (254).png" alt="" width="563"><figcaption></figcaption></figure>

Supongamos que en algún momento se ha producido un error y que en la observacion 10, el Tiempo = 18:

```r
Tiempo=c(11.37,10.07,8.65,8.17,9.22,11.63,11.95,10.85,13.08,18)
Oxigeno=c(44.609,45.313,54.297,59.571,49.874,44.811,45.681,49.091,39.442,60.055)
data_outlier<-data.frame(Tiempo,Oxigeno)
```

Y volvemos a ajustar una recta de regresión y la volvemos a dibujar

```r
summary(lm(data_outlier$Tiempo~data_outlier$Oxigeno))
```

```r
Call:
lm(formula = data_outlier$Tiempo ~ data_outlier$Oxigeno)

Residuals:
    Min      1Q  Median      3Q     Max 
-3.3465 -1.8551 -0.1378  0.6515  6.4733 

Coefficients:
                     Estimate Std. Error t value Pr(>|t|)
(Intercept)          10.25814    7.28242   1.409    0.197
data_outlier$Oxigeno  0.02112    0.14655   0.144    0.889

Residual standard error: 2.985 on 8 degrees of freedom
Multiple R-squared:  0.00259,	Adjusted R-squared:  -0.1221 
F-statistic: 0.02078 on 1 and 8 DF,  p-value: 0.889
```

$$
Tiempo=10.26+0.02112*Oxigeno
$$

```r
ggp <- ggplot(data,aes(Oxigeno, Tiempo)) + geom_point()
ggp + stat_smooth(method = "lm",
                  formula = y ~ x,
                  geom = "smooth")

```

<figure><img src="../../.gitbook/assets/image (255).png" alt="" width="563"><figcaption></figcaption></figure>

Los cambios producidos por una única observación son catastróficos. Para evitar estas situaciones, necesitamos estudiar la causa de estos valores y realizar un tratamiento de los mismos.&#x20;

Vamos a realizar los siguientes ejercicios:

{% content-ref url="ejercicio-2.1/" %}
[ejercicio-2.1](ejercicio-2.1/)
{% endcontent-ref %}

Antes de ver cómo tratar los datos atípcio, nos preguntamos como los podemos clasificar&#x20;

## Clases de datos atípicos

| Tipos                                                                                                               | Soluciones                                                                                                                                                |
| ------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Datos que son errores en la toma de datos o en la codificación de los mismos y no los hemos detectado como erróneos | Subsanar el error y si no es posible eliminar el dato y considerarlo “_missing_”                                                                          |
| Datos causados por acontecimientos extraordinarios, pero su presencia está justificada                              | Mantenerlos salvo que su relevancia sea anecdótica                                                                                                        |
| Observaciones extrañas para las que no nos sirve ningún tipo de justificación                                       | Eliminarlos cuando no es posible encontrar una explicación                                                                                                |
| Datos con valores extremos                                                                                          | El investigador decidirá si debe eliminarse o considerarlo representativo de una parte minoritaria. Si se elimina, debe quedar constancia y ser informada |

Como **criterio general**, la decisión de los atípicos a eliminar, debe ser en función de sus características y de los **objetivos del análisis a realizar**.&#x20;

A veces se **analizan los datos con los atípicos y sin los atípicos** para ver dicha influencia (**Análisis de Sensibilidad**). Si los resultados son similares entonces se convierte en una decisión intranscendente lo que se haga con los atípicos. Pero si no los son, unos pocos valores atípicos no eliminados de un análisis pueden provocar que el resto de los datos resulten inútiles para llegar a conclusiones correctas.&#x20;

Para la detección y tratamiento de los datos atípicos vamos a seguir tres enfoques que veremos a continuación, enfoque univariante, ennfoque bivariante y enfoque multivariante, que podemos ver resumiedo en el siguiente gráfico:

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>
