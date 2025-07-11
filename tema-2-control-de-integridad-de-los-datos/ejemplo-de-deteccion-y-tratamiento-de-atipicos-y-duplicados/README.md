---
cover: >-
  ../../.gitbook/assets/Introducing-Automated-Time-Series-Anomaly-Detection_blog_Image_v.1.0.webp
coverY: 0
---

# Ejemplo de detección y tratamiento de atípicos y duplicados

Vamos a ver un ejemplo con la siguiente base de datos (CEREALES) que contiene datos sobre la composición de diferentes variedades de cereales:

{% file src="../../.gitbook/assets/CEREALES.csv" %}

Las variables miden la composición de MANGANESO, CALORIAS, FIBRA, SELENIO y FOSFORO siendo variables medidas en escala continua que toman valores positivos y la variabe VARIEDAD que es una variable que mide el tipo de cereal.&#x20;

Importa la base de datos y mira si hay posibles errores e incongruencias o hay que cambiar algún tipo de variable para ello piensa primero en el tipo de variable:

```r
datos<-read.csv("CEREALES.csv")
str(datos)
summary(datos)

```

Después realiza un estudio para ver si hay datos atípicos, puedes seguir los siguientes pasos:

1. Estudia de forma univariada los datos atípicos mediante unos gráficos y la obtención de los datos atípicos y extremos.&#x20;
2. Estudia de forma bivariada si son realmente atípicos considerando que en esta base de datos lo más seguro es que busquemos ver si hay diferencias de la composición entre las distintas variedades de cereal.&#x20;
3. Si así lo consideras, borra los datos atípicos y justifica por qué quitas o no los datos atípicos.
4. Con la base de datos completa, aplica el algoritmo LOF para el estudio de datos atípicos multivariante.&#x20;

**Lectura y comprobación de errores y declaración de variables**

```r
datos<-read.csv("CEREALES.csv")

str(datos)
summary(datos)
datos$VARIEDAD<-factor(datos$VARIEDAD)
str(datos)
table(datos$VARIEDAD)

```

