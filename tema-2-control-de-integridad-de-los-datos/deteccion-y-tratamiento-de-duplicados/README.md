---
hidden: true
cover: >-
  ../../.gitbook/assets/Introducing-Automated-Time-Series-Anomaly-Detection_blog_Image_v.1.0.webp
coverY: 0
---

# Detección y tratamiento de duplicados

Los datos **duplicados** se refieren a observaciones o registros en un conjunto de datos que tienen los mismos valores en todas o en la mayoría de sus variables. Es decir, dos o más filas en un conjunto de datos son idénticas o muy similares en términos de los valores que contienen.

La presencia de datos duplicados puede afectar la calidad y la validez de los análisis estadísticos y los modelos predictivos, ya que pueden introducir sesgos y distorsiones en los resultados. Por lo tanto, es común realizar la detección y el tratamiento de datos duplicados al limpiar y preparar los datos antes de realizar análisis más avanzados.

Los datos duplicados pueden ocurrir por diversas razones:&#x20;

1. Como errores de entrada de datos,
2. Problemas en la recolección de datos,
3. O simplemente porque las mismas observaciones se han registrado más de una vez.

Identificar y abordar estos duplicados es esencial para asegurar la precisión y la confiabilidad de los resultados de cualquier análisis o modelado que se realice con esos datos.

**Ejemplo de duplicados en R:**

```r
df <- data.frame(ID = c(1, 2, 3, 4, 2, 5),  
                Name = c("John", "Jane", "Bob", "Alice", "Jane", "Charlie"),  
                Age = c(25, 30, 22, 28, 30, 35),  
                Score = c(85, 92, 78, 90, 92, 80))
```

La función <mark style="color:green;">**`duplicated()`**</mark> te permite identificar las filas duplicadas en un dataframe y junto con <mark style="color:green;">**`!`**</mark> los puedes eliminar y conservar solo las primeras ocurrencias:

```r
###Identificación de los duplicados
duplicated(df)

# Fijaos que sólo te da uno de los dos duplicados
[1] FALSE FALSE FALSE FALSE  TRUE FALSE

# Eliminar duplicados y conservar solo las primeras ocurrencias
df_no_duplicates <- df[!duplicated(df), ]
df_no_duplicates
```

La función <mark style="color:green;">**`unique()`**</mark> se puede utilizar para obtener las filas únicas de un dataframe, lo que puede también ayudar a identificar y tratar los duplicados, **la diferencia es que con esta no sabes cuáles han sido los duplicados ni cuantos:**

```r
# Obtener filas únicas
unique(df)
```

Por otro lado la librería **`dplyr`** proporciona la función <mark style="color:green;">**`distinct()`**</mark> para obtener filas únicas de un dataframe muy similar a unique. También se puede utilizar para eliminar duplicados:

```r
library(dplyr)
# Eliminar duplicados usando distinct
distinct(df)
```

y finalmente la función <mark style="color:green;">**`subset()`**</mark> se puede utilizar para eliminar duplicados basándose en una condición:

```r
# Eliminar duplicados basados en la columna 'ID'
df_no_duplicates <- subset(df, !duplicated(ID)) 
```
