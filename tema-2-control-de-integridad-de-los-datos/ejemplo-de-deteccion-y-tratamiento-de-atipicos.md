---
hidden: true
cover: >-
  ../.gitbook/assets/Introducing-Automated-Time-Series-Anomaly-Detection_blog_Image_v.1.0.webp
coverY: 0
---

# Ejemplo de detección y tratamiento de atípicos

Se quiere diseñar una dieta para deportistas de alto rendimiento, para ellos se van a analizar 173 muestras de 3 cereales (Avena, Trigo y Cebada) con el **objetivo** de ver si existen diferencias reales en la densidad de los distintos componentes.&#x20;

El objetivo final es determinar qué cereal recomendar según las necesidades específicas de un atleta.

Para ello vamos a trabajar con la siguiente base de datos (CEREALES) que contiene datos sobre la composición de diferentes variedades de cereales:&#x20;

{% file src="../.gitbook/assets/CEREALES.csv" %}

**Diccionario de variables**

<table><thead><tr><th width="205.40625">Variable</th><th>Descripción</th></tr></thead><tbody><tr><td><mark style="color:purple;"><strong><code>VARIEDAD</code></strong></mark></td><td>Tipo de cereal: <code>AVENA</code>, <code>TRIGO</code> o <code>CEBADA</code>.</td></tr><tr><td><mark style="color:purple;"><strong><code>MANGANESO</code></strong></mark></td><td>Contenido de manganeso; normalmente se expresa en <strong>mg por 100 g</strong> de alimento.</td></tr><tr><td><mark style="color:purple;"><strong><code>CALORIAS</code></strong></mark></td><td>Valor energético en <strong>kcal por 100 g</strong>.</td></tr><tr><td><mark style="color:purple;"><strong><code>FIBRA</code></strong></mark></td><td>Fibra dietética total en <strong>g por 100 g</strong>.</td></tr><tr><td><mark style="color:purple;"><strong><code>SELENIO</code></strong></mark></td><td>Contenido de selenio (habitualmente <strong>µg por 100 g</strong>).</td></tr><tr><td><mark style="color:purple;"><strong><code>FOSFORO</code></strong></mark></td><td>Fósforo total, típico en <strong>mg por 100 g</strong>.</td></tr><tr><td><mark style="color:purple;"><strong><code>N_MUESTRA</code></strong></mark></td><td>Identificador secuencial de la muestra (1 – 173).</td></tr></tbody></table>

Importamos la base de datos y miramos si hay posibles errores e incongruencias o hay que cambiar algún tipo de variable para ello piensa primero en el tipo de variable:

```r
library(DataExplorer)
library(ggplot2)
library(patchwork)
library(tidyverse)

datos<-read.csv("CEREALES.csv")

### Tipo de datos
str(datos)
datos$VARIEDAD<-as.factor(datos$VARIEDAD)

summary(datos)
plot_histogram(datos)
plot_bar(datos)
```

```r
VARIEDAD    MANGANESO       CALORIAS         FIBRA           SELENIO         FOSFORO     
  AVENA:61   Min.   :0.58   Min.   :118.1   Min.   : 0.720   Min.   :18.20   Min.   :105.9  
 CEBADA:40   1st Qu.:1.04   1st Qu.:148.1   1st Qu.: 4.180   1st Qu.:19.18   1st Qu.:121.9  
 TRIGO :72   Median :1.11   Median :150.8   Median : 7.970   Median :24.77   Median :164.5  
             Mean   :1.10   Mean   :163.6   Mean   : 7.931   Mean   :25.98   Mean   :162.3  
             3rd Qu.:1.29   3rd Qu.:195.2   3rd Qu.: 8.190   3rd Qu.:26.78   3rd Qu.:177.4  
             Max.   :3.84   Max.   :200.4   Max.   :15.210   Max.   :66.00   Max.   :255.2  
   N_MUESTRA    
 Min.   :  1.0  
 1st Qu.: 43.0  
 Median : 85.0  
 Mean   : 85.3  
 3rd Qu.:128.0  
 Max.   :170.0  
```

Ahora estudiaremos paso a paso la detección y tratamiento de los datos atípicos:

1. **Estudio univariante**&#x20;

```r
source("Funciones_propias.R")

# 1. Obtenemos los nombres de las columnas numéricas
numeric_vars <- names(datos)[sapply(datos, is.numeric)]

# 2. Usamos una función anónima para pasar 'data' y el 'nombre'
outliers_results <- lapply(numeric_vars, function(v) outliers_extreme(datos, v))
```

```r
🟠 Atípicos (Outliers) en MANGANESO :  42 
   🔸 Proporción (%): 24.28 %

🔴 Extremos (Extreme values) en MANGANESO :  3 
   🔻 Proporción (%): 1.73 %

🟠 Atípicos (Outliers) en CALORIAS :  0 
   🔸 Proporción (%): 0 %

🔴 Extremos (Extreme values) en CALORIAS :  0 
   🔻 Proporción (%): 0 %

🟠 Atípicos (Outliers) en FIBRA :  14 
   🔸 Proporción (%): 8.09 %

🔴 Extremos (Extreme values) en FIBRA :  0 
   🔻 Proporción (%): 0 %

🟠 Atípicos (Outliers) en SELENIO :  3 
   🔸 Proporción (%): 1.73 %

🔴 Extremos (Extreme values) en SELENIO :  2 
   🔻 Proporción (%): 1.16 %

🟠 Atípicos (Outliers) en FOSFORO :  0 
   🔸 Proporción (%): 0 %

🔴 Extremos (Extreme values) en FOSFORO :  0 
   🔻 Proporción (%): 0 %

🟠 Atípicos (Outliers) en N_MUESTRA :  0 
   🔸 Proporción (%): 0 %

🔴 Extremos (Extreme values) en N_MUESTRA :  0 
   🔻 Proporción (%): 0 %
```

```r
extreme_results <- lapply(numeric_integer_vars, function(var) {
  extreme(datos, var)  # Llamar a la función pasando el nombre de la variable
})
```

```r
📌 Outliers identified in MANGANESO :  3 extreme values
📊 Proportion (%) of extreme values: 1.73 %

📌 Outliers identified in CALORIAS :  0 extreme values
📊 Proportion (%) of extreme values: 0 %

📌 Outliers identified in FIBRA :  0 extreme values
📊 Proportion (%) of extreme values: 0 %

📌 Outliers identified in SELENIO :  2 extreme values
📊 Proportion (%) of extreme values: 1.16 %

📌 Outliers identified in FOSFORO :  0 extreme values
📊 Proportion (%) of extreme values: 0 %

📌 Outliers identified in N_MUESTRA :  0 extreme values
📊 Proportion (%) of extreme values: 0 %
```

Las variables con datos atípicos son MANGANESO 24.28% de outliers y un 1.73% de extremos. FIBRA 8.09% de outliers y SELENIO 1.73% de outliers y 1.16% de extremos.&#x20;

Veremos los gráficos correspondientes a estas variables:

{% tabs %}
{% tab title="MANGANESO" %}
<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="FIBRA" %}
<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="SELENIO" %}
<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>
{% endtab %}
{% endtabs %}

En todos los gráficos se ve claramente como las variables están distribuidos de forma trimodal, posiblemente correspondiendo a los tres tipos de cereal. Esto lo veremos en el estudio bivariante. La gran cantidad de datos outliers en MANGANESO con valores pequeños, posiblemente correspondan a un tipo de cereal, siendo sólo posibles outliers los 3 valores extremos que se alejan con valores altos de la distribución. En el caso de FIBRA parece que el % de outlier corresponde a la distribución trimodal correspondiente a los tres tipos de cereales y en SELENIO, parece todos son outliers.

2. **Estudio** **bivariante**:

Para el estudio bivariante hay que pensar en el objetivo principal, en este caso buscamos ver si hay diferencias de la composición entre las distintas variedades de cereal, por tanto la variable principal con la que miraremos los posibles outliers, será con la variable VARIEDAD. Además en el estudio univariante, se ha visto la distribución trimodal correspondiente seguramente a los tres cereales.

MANGANESO

```r
# Gráfico 1: Manganeso 
# Gráfico 2: Manganeso por variedad
p1 <- ggplot(datos, aes(y = MANGANESO)) +
  geom_boxplot(fill = "lightblue") 
p2 <- ggplot(datos, aes(x = VARIEDAD, y = MANGANESO)) +
  geom_boxplot(fill = "lightblue") 

p1 + p2
```

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Los outliers son un 24.28 %, de los cuales un 1.73% (3 valores) son extremos. En el caso de los outliers, se ve que corresponden a la CEBADA y los 3 extremos no pertenecen a ninguna de las 3 distribuciones de los cereales, por tanto habrá que borrarlos.

FIBRA

```r
# Gráfico 1: Fibra 
# Gráfico 2: Fibra por variedad
p1 <- ggplot(datos, aes(y = FIBRA)) +
  geom_boxplot(fill = "lightgreen") 
p2 <- ggplot(datos, aes(x = VARIEDAD, y = FIBRA)) +
  geom_boxplot(fill = "lightgreen") 
p1 + p2
```

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

El porcentaje de ouliers es de un 8% y además se ve que es debido a una distribución asimétrica ya que los outliers corresponden a la cebada, por tanto, no hay que borrarlos, no son outliers.

​Lo que aparece como "outliers" en avena no parecen realmente outliers, simplemente hay algunos datos con una baja concentración que se desvían de la mediana y lo que podrían mostrar es una mayor variabilidad en esa variedad.

SELENIO

```r
# Gráfico 1: Selenio 
# Gráfico 2: Selenio por variedad
p1 <- ggplot(datos, aes(y = SELENIO)) +
  geom_boxplot(fill = "orange") 
p2 <- ggplot(datos, aes(x = VARIEDAD, y = SELENIO)) +
  geom_boxplot(fill = "orange") 
p1 + p2
```

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Esta situación es complicada porque los 3 cereales tiene un extremo que realmente se corresponde con cada uno de los cerelaes y serían parte de la propia distribución. Quizás una cosa que podría haber pasado es que la persona que enviara esa medición lo hiciera en otras unidades, pero tampoco lo podemos saber, así que la mejor opción sería borrarlos ya que esa medición nos puede distorsionar los modelos.

CONCLUSIÓN:

Borrar los outliers de SELENIO y los extremos de MANGANESO

```r
## MANGANESO
extreme_values <- boxplot.stats(datos$MANGANESO,coef=3)$out  # extreme values.
ext_ind <- which(datos$MANGANESO %in% c(extreme_values))
datos$MANGANESO[ext_ind]<-NA

## SELENIO
outlier_values <- boxplot.stats(datos$SELENIO)$out  # outlier values.
out_ind <- which(datos$SELENIO %in% c(outlier_values))
datos$SELENIO[out_ind]<-NA
```

**Análisis multivariante con LOF**

```r
library(dbscan)
library(class)
library(ggplot2)

library(dbscan)
library(class)

## Leemos los datos de nuevo ya que hemos borrado en la fase anterior los outliers
datos <- read.csv("CEREALES.csv")  # import data
datos$VARIEDAD<-factor(datos$VARIEDAD)

####Aplicamos LOF
k<-round(log(nrow(datos))) ##calcular la k
datos_lof<-scale(select(datos,-VARIEDAD,-N_MUESTRA))
lof_score<-lof(datos_lof,minPts = k) 
datos$lof<-lof_score

##Miramos el boxplot
ggplot(datos, aes(y = lof)) +
  geom_boxplot(fill = "skyblue", outlier.color = "red", outlier.shape = 16) +
  theme_minimal() +
  labs(title = "Distribución de LOF Scores")

#Imprimimos los que son >10
datos[which(datos$lof>10),]

```

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

Se ve como hay unos datos con score LOF muy elevado por encima de 10 que seguramente estén marcando los outliers que hemos visto en el estudio univariente/bivariante. Lo comprobamos:

```r
    VARIEDAD MANGANESO CALORIAS FIBRA SELENIO FOSFORO N_MUESTRA      lof
23     TRIGO      1.11   187.22  7.95   55.22  126.64        23 14.86866
61    CEBADA      3.09   130.02 15.04   36.14  232.83        60 18.02926
69    CEBADA      0.60   130.14 13.19   66.00  228.88        68 17.63122
112    AVENA      3.76   150.18  0.83   18.99  162.01       110 22.90691
127    AVENA      3.84   150.51  0.72   18.67  167.37       125 23.09780
142    AVENA      1.30   150.18  1.08   49.58  168.59       140 20.77175

```

```r
###Comprobamos las cuantitativas
ggplot(datos, aes(x = SELENIO, y = MANGANESO, colour = lof)) +
  geom_point(aes(size = lof)) +
  scale_color_gradient(low = "blue", high = "red", name = "LOF Score") +
  labs(title = "Detección de Valores Atípicos con LOF")

ggplot(datos, aes(x = SELENIO, y = FIBRA, colour = lof)) +
  geom_point(aes(size = lof)) +
  scale_color_gradient(low = "blue", high = "red", name = "LOF Score") +
  labs(title = "Detección de Valores Atípicos con LOF")

ggplot(datos, aes(x = MANGANESO, y = FIBRA, colour = lof)) +
  geom_point(aes(size = lof)) +
  scale_color_gradient(low = "blue", high = "red", name = "LOF Score") +
  labs(title = "Detección de Valores Atípicos con LOF")

ggplot(datos, aes(x = FOSFORO, y = CALORIAS, colour = lof)) +
  geom_point(aes(size = lof)) +
  scale_color_gradient(low = "blue", high = "red", name = "LOF Score") +
  labs(title = "Detección de Valores Atípicos con LOF")

```

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Aquí vemos como los 6 puntos con el LOF muy elevado corresponden a los 3 extremos de MANGANESO y los 3 extremos de SELENIO.&#x20;

Además cuando los representamos con FIBRA se ve como los 3 "outliers" que se veían en  AVENA coinciden con un extremo de Selenio y 2 de MANGANESO.

Para las otras dos no parece que se alejen de la nube de puntos.

```r
# Ejecutamos un PCA rápido sobre las variables numéricas
pca_res <- prcomp(scale(select(datos,-VARIEDAD,-N_MUESTRA)), center = TRUE)

# Creamos un dataframe para graficar
df_pca <- as.data.frame(pca_res$x)
df_pca$lof <- datos$lof

ggplot(df_pca, aes(x = PC1, y = PC2, color = lof)) +
  geom_point(aes(size = lof)) +
  scale_color_gradient(low = "lightgrey", high = "darkorange") +
  labs(title = "Proyección PCA y Puntuación LOF",
       subtitle = "Los outliers se alejan del 
       centro de la masa de datos en el espacio latente") +
  theme_bw()
```



<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

Al representar la PCA se ve como las 6 observaciones están completamente alejadas del resto.&#x20;

Quitar 6 observaciones enteras podría ser mucho, lo ideal sería en este caso añadir a las 3 de manganeso y 3 de selenio que ya habíamos corregido antes, las 3 de FIBRA que no eran outliers en el estudio univariante, pero que si aparecían outliers en AVENA.&#x20;
