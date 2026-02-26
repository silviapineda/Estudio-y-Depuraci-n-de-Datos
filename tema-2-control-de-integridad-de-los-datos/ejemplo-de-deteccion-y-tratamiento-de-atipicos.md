---
hidden: true
cover: >-
  ../.gitbook/assets/Introducing-Automated-Time-Series-Anomaly-Detection_blog_Image_v.1.0.webp
coverY: 0
---

# Ejemplo de detección y tratamiento de atípicos

Vamos a ver un ejemplo con la siguiente base de datos (CEREALES) que contiene datos sobre la composición de diferentes variedades de cereales:&#x20;

{% file src="../.gitbook/assets/CEREALES.csv" %}

**Diccionario de variables**

<table><thead><tr><th width="205.40625">Variable</th><th>Descripción</th></tr></thead><tbody><tr><td><mark style="color:purple;"><strong><code>VARIEDAD</code></strong></mark></td><td>Tipo de cereal: <code>AVENA</code>, <code>TRIGO</code> o <code>CEBADA</code>.</td></tr><tr><td><mark style="color:purple;"><strong><code>MANGANESO</code></strong></mark></td><td>Contenido de manganeso; normalmente se expresa en <strong>mg por 100 g</strong> de alimento.</td></tr><tr><td><mark style="color:purple;"><strong><code>CALORIAS</code></strong></mark></td><td>Valor energético en <strong>kcal por 100 g</strong>.</td></tr><tr><td><mark style="color:purple;"><strong><code>FIBRA</code></strong></mark></td><td>Fibra dietética total en <strong>g por 100 g</strong>.</td></tr><tr><td><mark style="color:purple;"><strong><code>SELENIO</code></strong></mark></td><td>Contenido de selenio (habitualmente <strong>µg por 100 g</strong>).</td></tr><tr><td><mark style="color:purple;"><strong><code>FOSFORO</code></strong></mark></td><td>Fósforo total, típico en <strong>mg por 100 g</strong>.</td></tr><tr><td><mark style="color:purple;"><strong><code>N_MUESTRA</code></strong></mark></td><td>Identificador secuencial de la muestra (1 – 173).</td></tr></tbody></table>

El objetivo del estudio es saber la composición entre los diferentes tipos de cereal.&#x20;

Importamos la base de datos y miramos si hay posibles errores e incongruencias o hay que cambiar algún tipo de variable para ello piensa primero en el tipo de variable:

```r
datos<-read.csv("CEREALES.csv")

## Miramos que el tipo de variable y nombre esté bien 

str(datos)
summary(datos)

## Declaramos como factor la variable cualitativa
datos$VARIEDAD<-factor(datos$VARIEDAD)
summary(datos)

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
#Cargamos las funciones propias diseñadas en el apartado de automatización
source("Funciones_propias.R")

# Aplicar la función a múltiples variables numéricas o enteras
numeric_integer_vars <- names(which(sapply(datos, is.numeric) | sapply(datos, is.integer)))
# Aplicar la función 'outliers' a cada una de las variables numéricas
outliers_results <- lapply(numeric_integer_vars, function(var) {
  outliers(datos, var)  # Llamar a la función pasando el nombre de la variable
})
```

```r
📌 Outliers identified in MANGANESO :  42 outliers
📊 Proportion (%) of outliers: 24.28 %

📌 Outliers identified in CALORIAS :  0 outliers
📊 Proportion (%) of outliers: 0 %

📌 Outliers identified in FIBRA :  14 outliers
📊 Proportion (%) of outliers: 8.09 %

📌 Outliers identified in SELENIO :  3 outliers
📊 Proportion (%) of outliers: 1.73 %

📌 Outliers identified in FOSFORO :  0 outliers
📊 Proportion (%) of outliers: 0 %

📌 Outliers identified in N_MUESTRA :  0 outliers
📊 Proportion (%) of outliers: 0 %
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
<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="FIBRA" %}
<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="SELENIO" %}
<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>
{% endtab %}
{% endtabs %}

En todos los gráficos se ve claramente como las variables están distribuidos de forma trimodal, posiblemente correspondiendo a los tres tipos de cereal. Esto lo veremos en el estudio bivariante. La gran cantidad de datos outliers en MANGANESO con valores pequeños, posiblemente correspondan a un tipo de cereal, siendo sólo posibles outliers los 3 valores extremos que se alejan con valores altos de la distribución. En el caso de FIBRA parece que el % de outlier corresponde a la distribución trimodal correspondiente a los tres tipos de cereales y en SELENIO, parece todos outliers.

2. **Estudio** **bivariante**:

Para el estudio bivariante hay que pensar en el objetivo principal, en este caso buscamos ver si hay diferencias de la composición entre las distintas variedades de cereal, por tanto la variable principal con la que miraremos los posibles outliers, será con la variable VARIEDAD. Además en el estudio univariante, se ha visto la distribución trimodal correspondiente seguramente a los tres cereales.

MANGANESO

```r
# Gráfico 1: Manganeso por variedad
p1 <- ggplot(datos, aes(x = VARIEDAD, y = MANGANESO)) +
  geom_boxplot(fill = "lightblue") 

p1
```

<figure><img src="../.gitbook/assets/image (6) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Los outliers son un 24.28 %, de los cuales un 1.73% (3 valores) son extremos. En el caso de los outliers, se ve que corresponden a la CEBADA y los 3 extremos  no pertenecen a ninguna de las 3 distribuciones parcadas por los 3 cereales, por tanto habrá que borrarlos. &#x20;

FIBRA

```r
# Gráfico 2: FIBRA por variedad
p2 <- ggplot(datos, aes(x = VARIEDAD, y = FIBRA)) +
  geom_boxplot(fill = "lightblue") 

p2
```

<figure><img src="../.gitbook/assets/image (7) (1).png" alt="" width="563"><figcaption></figcaption></figure>

El porcentaje de ouliers es de un 8% y además se ve que es debido a una distribución asimétrica ya que los outliers corresponden a la cebada, por tanto, no hay que borrarlos, no son outliers.

SELENIO

```r
# Gráfico 3: SELENIO por VARIEDAD
p3 <- ggplot(datos, aes(x = VARIEDAD, y = SELENIO)) +
  geom_boxplot(fill = "lightblue") 

p3
```

<figure><img src="../.gitbook/assets/image (9) (1).png" alt="" width="563"><figcaption></figcaption></figure>

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

datos <- read.csv("CEREALES.csv")  # import data
datos$VARIEDAD<-factor(datos$VARIEDAD)

####Aplicamos LOF
k<-round(log(nrow(datos))) ##calcular la k
datos_lof<-select(datos,-VARIEDAD,-N_MUESTRA)
lof_score<-lof(datos_lof,minPts = k) 

##Añadimos el score a la base de datos
datos$lof_score<-lof_score

##Representamos en un histograma el score
ggplot(datos, aes(x = lof_score)) +
  geom_histogram() +
  labs(title = "Distribución de LOF Scores", x = "LOF score", y = "Frecuencia")

```

<figure><img src="../.gitbook/assets/image (12).png" alt="" width="563"><figcaption></figcaption></figure>

Se ve como hay unos datos con score LOF muy elevado por encima de 5 que seguramente estén marcando los outliers que hemos visto en el estudio univariente/bivariante. Lo comprobamos:

```r
datos[lof_score>5,]

      VARIEDAD MANGANESO CALORIAS FIBRA SELENIO FOSFORO N_MUESTRA lof_score
23	TRIGO	1.11	 187.22	  7.95	55.22	126.64	23	  15.015167
69	CEBADA	0.60	 130.14	  13.19	66.00	228.88	68	  9.395902
142	AVENA	1.30	 150.18	  1.08	49.58	168.59	140	  18.772921

ggplot(datos, aes(x = MANGANESO, y = SELENIO, colour = lof_score)) +
  geom_point() +
  scale_color_gradient(low = "blue", high = "red", name = "LOF Score") +
  labs(title = "Detección de Valores Atípicos con LOF")
```

<figure><img src="../.gitbook/assets/image (13).png" alt="" width="563"><figcaption></figcaption></figure>

En el estudio multivariante se ve como hay 3 observaciones que tienen un LOF completamente elevado, estos corresponden a los outliers de la variable de SELENIO. No se ve en el resto de las variables que estas observaciones se comporten de forma rara, simplemente son tan atípicas en la variable SELENIO, que el algoritmo LOF las ha detectado. Sólo borraremos el valor de SELENIO.

