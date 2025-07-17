---
cover: >-
  ../../.gitbook/assets/Introducing-Automated-Time-Series-Anomaly-Detection_blog_Image_v.1.0.webp
coverY: 0
---

# Ejemplo de detección y tratamiento de atípicos y duplicados

Vam<mark style="background-color:yellow;">os a ver un ejemplo con la siguiente base de datos (CEREALES) que contiene datos sobre la composición de diferentes variedades de cereales:</mark>&#x20;

{% file src="../../.gitbook/assets/CEREALES.csv" %}

**Diccionario de variables**

<table><thead><tr><th width="205.40625">Variable</th><th>Descripción</th></tr></thead><tbody><tr><td><mark style="color:purple;"><strong><code>VARIEDAD</code></strong></mark></td><td>Tipo de cereal: <code>AVENA</code>, <code>TRIGO</code> o <code>CEBADA</code>.</td></tr><tr><td><mark style="color:purple;"><strong><code>MANGANESO</code></strong></mark></td><td>Contenido de manganeso; normalmente se expresa en <strong>mg por 100 g</strong> de alimento.</td></tr><tr><td><mark style="color:purple;"><strong><code>CALORIAS</code></strong></mark></td><td>Valor energético en <strong>kcal por 100 g</strong>.</td></tr><tr><td><mark style="color:purple;"><strong><code>FIBRA</code></strong></mark></td><td>Fibra dietética total en <strong>g por 100 g</strong>.</td></tr><tr><td><mark style="color:purple;"><strong><code>SELENIO</code></strong></mark></td><td>Contenido de selenio (habitualmente <strong>µg por 100 g</strong>).</td></tr><tr><td><mark style="color:purple;"><strong><code>FOSFORO</code></strong></mark></td><td>Fósforo total, típico en <strong>mg por 100 g</strong>.</td></tr><tr><td><mark style="color:purple;"><strong><code>N_MUESTRA</code></strong></mark></td><td>Identificador secuencial de la muestra (1 – 173).</td></tr></tbody></table>

El objetivo del estudio es saber la composición entre los diferentes tipos de cereal.&#x20;

Importa la base de datos y mira si hay posibles errores e incongruencias o hay que cambiar algún tipo de variable para ello piensa primero en el tipo de variable:

```r
datos<-read.csv("CEREALES.csv")

## Miramos que el tipo de variable y nombre esté bien 

str(datos)
summary(datos)

## Declaramos como factor la variable cualitativa
datos$VARIEDAD<-factor(datos$VARIEDAD)
table(datos$VARIEDAD)

```





1. Estudia de forma univariada los datos atípicos mediante unos gráficos y la obtención de los datos atípicos y extremos.&#x20;



1. Estudia de forma bivariada si son realmente atípicos considerando que en esta base de datos lo más seguro es que busquemos ver si hay diferencias de la composición entre las distintas variedades de cereal.&#x20;
2. Si así lo consideras, borra los datos atípicos y justifica por qué quitas o no los datos atípicos.
3. Con la base de datos completa, aplica el algoritmo LOF para el estudio de datos atípicos multivariante.&#x20;

**Lectura y comprobación de errores y declaración de variables**

```r
datos<-read.csv("CEREALES.csv")

str(datos)
summary(datos)
datos$VARIEDAD<-factor(datos$VARIEDAD)
str(datos)
table(datos$VARIEDAD)

```

