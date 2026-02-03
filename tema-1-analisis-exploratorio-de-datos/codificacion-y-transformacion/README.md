---
cover: ../../.gitbook/assets/image (132).png
coverY: 0
---

# Codificación y Transformación

Como ya hemos visto brevemente en el capítulo de tipos de variables, normalmente los datos de un archivo necesitarán ser cuidadosamente preparados antes de poder aplicar, con las mínimas garantías, cualquier análisis estadístico.&#x20;

La preparación del archivo de datos incluye desde la simple detección y corrección de los posibles errores cometidos al introducir los datos, hasta sofisticadas transformaciones, pasando por la recodificación de los códigos utilizados para los valores de una o más variables, o la creación de nuevas variables a partir de otras ya existentes.&#x20;

Para ilustrar esta parte, vamos a leer la siguiente base de datos:

{% code fullWidth="false" %}
```r
data <- read.csv("https://raw.githubusercontent.com/hadley/r4ds/main/data/students.csv")
str(data)
```
{% endcode %}

```r
'data.frame':	6 obs. of  5 variables:
 $ Student.ID    : int  1 2 3 4 5 6
 $ Full.Name     : chr  "Sunil Huffmann" "Barclay Lynn" "Jayendra Lyne" "Leon Rossini" ...
 $ favourite.food: chr  "Strawberry yoghurt" "French fries" "N/A" "Anchovies" ...
 $ mealPlan      : chr  "Lunch only" "Lunch only" "Breakfast and lunch" "Lunch only" ...
 $ AGE           : chr  "4" "5" "7" "" ...

```

Ahora tendremos que comprobar que los datos se han leído correctamente y no tienen errores

## Detección inicial de errores más comunes

En esta detección inicial lo que queremos ver es si:

1. El nombre de las variables son consistentes
2. Las variables están bien declarados
3. No hay valores incorrectos
4. Los NAs se han leído de forma correcta

Vamos a verlo de forma muy sencilla primero de forma manual para entender las cosas que hay que detectar y luego veremos como detectarlo de forma automática.

### **El nombre de las columnas debe tener un nombre consistente.**&#x20;

Las dos palabaras que corresponden a la columnas: <mark style="color:purple;">`Student.ID`</mark>, <mark style="color:purple;">`Full.Name`</mark> y <mark style="color:purple;">`favourite.food`</mark> están separadas por un . y <mark style="color:purple;">`mealPlan`</mark> se diferencia por una mayúscula. Además todas están en minúsculas y <mark style="color:purple;">`AGE`</mark> en mayúsculas. Para evitar futuros errores, los nombres deberán ser consistentes.

```r
colnames(data)<-
  c("student_id","full_name","favourite_food","meal_plan","age")
colnames(data)

#Con tidyverse
library(tidyverse)
data <-janitor::clean_names(data)
##Con la opción de clean_names recuerda verificar que los nombres se han completado de forma correcta
```

```r
[1] "student_id"     "full_name"      "favourite_food" "meal_plan"      "age"
```

### **Clasificar las variables en cuantitativas y cualitativas y declararlas de forma correcta**

```r
data$meal_plan<-as.factor(data$meal_plan)
str(data)
data$age<-as.numeric(data$age) ###Cuidado cunando salta este warning 
```

{% hint style="info" %}
Cuidado con los warnings, en este caso ha convertido un "five" en NA cuando deberíamos convertirlo previamente a 5
{% endhint %}

### **Asegurarnos que no hay valores incorrectos:**&#x20;

En la variable <mark style="color:purple;">`age`</mark>, una de las observaciones tiene "five" en vez del número, esto nos causará problemas a la hora de usar dicha variable como variable numérica como hemos visto al declararla con <mark style="color:green;">**`as.numeric()`**</mark>.

```r
data$age<-replace(data$age,data$age=="five",5)
data$age<-as.numeric(data$age)
```

### **Asegurarnos que los NA se han leído correctamente**

Columna <mark style="color:purple;">`favourite_food`</mark> tiene un `N/A` que no está detectado como dato "_missing_" y un vacío en <mark style="color:purple;">`age`</mark>

Para buscar posibles errores o NA mal declarados podemos hacer uso de sentencias sencillas como <mark style="color:green;">**`summary()`**</mark> o <mark style="color:green;">**`table()`**</mark>

```r
summary(data)
table(data$favourite_food)
table(data$meal_plan)

data$favourite_food<-replace(data$favourite_food, data$favourite_food=="N/A",NA)
data[data == ""] <- NA ##Esto reemplaza todos los valores vacíos a NA

data
```



Así quedaría la base de datos corregida

```r
  student_id        full_name     favourite_food           meal_plan age
1          1   Sunil Huffmann Strawberry yoghurt          Lunch only   4
2          2     Barclay Lynn       French fries          Lunch only   5
3          3    Jayendra Lyne               <NA> Breakfast and lunch   7
4          4     Leon Rossini          Anchovies          Lunch only  NA
5          5 Chidiegwu Dunkel              Pizza Breakfast and lunch   5
6          6    Güvenç Attila          Ice cream          Lunch only   6
```

En el siguiente recuadro puedes ver el resumen de las coas a verificar en esta primera parte:

<figure><img src="../../.gitbook/assets/image (307).png" alt="" width="563"><figcaption></figcaption></figure>

## Transformación de variables

En ocasiones nos puede interesar incorporar nuevas variables a nuestra base de datos, como ya vimos con la creación de la variable edad

<mark style="color:orange;">**Ejemplo**</mark>&#x20;

Para este ejemplo vamos a utilizar el dataset <mark style="color:orange;">**TiposDatos.csv**</mark>

{% file src="../../.gitbook/assets/TiposDatos.csv" %}

Aprovechamos para hacer un breve repaso de los tipos de variables y control de errores

```r
datos <- read.csv("TiposDatos.csv")
str(datos)

## 1. Declaramos de forma consistente el nombre de las variables
datos <- datos |> janitor::clean_names()
## Cuidado id debe ir en mayúscula porque es una abreviatura
datos <- rename(datos, ID = id)

## 2. Transformamos las variables que no están bien leídas
datos$fecha_de_nacimiento<-as.Date(datos$fecha_de_nacimiento,format="%d/%m/%y")
datos$estado_civil<-as.factor(datos$estado_civil)
str(datos)

## 3. No hay valores missing
any(is.na(datos))

## 4. valores correctos
summary(datos)
```

Cuando una base de datos contiene la fecha de nacimiento, es mucho más útil generar la edad, más fácil de interpretar, analizar como variable en si misma y manejar numéricamente:

```r
##Calcular la edad
datos$edad<-as.numeric(floor((Sys.Date()-datos$fecha_de_nacimiento)/365.25))
str(datos)
```

Podemos querer crear una variable en la base de datos  <mark style="color:orange;">**TiposDatos.csv**</mark> que se llame **índice de masa corporal** que se obtiene de la siguiente forma:&#x20;

$$
IMC=peso(kg)/altura(m)^2
$$

<pre class="language-r"><code class="lang-r">##Crear IMC
datos$IMC  &#x3C;- datos$peso / (datos$altura/100)^2

<strong>##IMC lo definimos con mayusculas porque son siglas. Cuidado con el uso de janitor
</strong>
</code></pre>

## Categorizar una variable cuantitativa en una cualitativa

Esta opción suele ser muy habitual ya que muchas veces queremos trabajar con las categorías de una variable en vez de la variable cuantitativa.&#x20;

<mark style="color:orange;">**Ejemplo**</mark>&#x20;

La variable cuantitativa IMC que acabamos de crear se suele usar en cuatro grupos:

* **bajo peso**: IMC <= 18.50
* **normal**: 18.5 < IMC <=25
* **sobrepeso**: 25 < IMC <= 30
* **obesidad**: IMC >30&#x20;

En realidad buscamos los siguientes tramos de IMC: (minimo, 18.5], (18.5, 25], (25, 30], (30, máximo]

```r
datos$IMC_grupos <- cut(datos$IMC, breaks = c(-Inf,18.5,25,30,Inf), 
                        labels = c("bajo peso", "normal", "sobrepreso", "obesidad"))
table(datos$IMC_grupos)

##Con tidyverse
datos <- datos |>
  mutate(
    IMC_grupos = case_when(
      IMC <= 18.5 ~ "bajo peso",           
      IMC > 18.5 & IMC <= 25 ~ "normal",   
      IMC > 25 & IMC <=30  ~ "sobrepeso",
      IMC > 30 ~ "obesidad"
    ),
    IMC_grupos = factor(IMC_grupos, levels = c("bajo peso", "normal", "sobrepeso", "obesidad"))
  )
```

¿Cuántas personas tenemos en cada uno de los grupos?

```r
table(datos$IMC_grupos)
```

```r
bajo peso     normal sobrepreso   obesidad 
         0          3          1          1 
```

## Recodificación de variables

Nos puede interesar trabajar de forma diferente sobre una de las variables que ya tenemos&#x20;

<mark style="color:orange;">**Ejemplo**</mark>

Queremos ver la probabilidad de estar casado o de tener un determinado peso si la persona tiene más de 40 años o menos y queremos poder predecir dicha variable, para aplicar en este caso una regresión logística necesitaremos tener una variable codificada como: 0 (<40), 1 (>=40). Esto con el comando <mark style="color:green;">**`if_else()`**</mark> del paquete **`dplyr`** es muy fácil:

```r
##Recodificación de variables
table(datos$edad)
datos$edad2cat<- if_else(datos$edad < 40, 0, 1)

```

**¿Qué tipo de variable es edad2Cat?**

Es una variable cualitativa que toma dos categorías: 0 (<40), 1 (>=40)

Es una buena práctica recodificar siempre en distintas variables para no perder información.

Ahora es tu turno: Realiza los siguientes ejercicios:



{% content-ref url="ejercicio-1.2-codificacion-y-transformacion/" %}
[ejercicio-1.2-codificacion-y-transformacion](ejercicio-1.2-codificacion-y-transformacion/)
{% endcontent-ref %}
