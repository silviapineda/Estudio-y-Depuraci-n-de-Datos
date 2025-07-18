---
cover: ../../.gitbook/assets/missing (1).jpeg
coverY: 0
---

# Detección de datos perdidos

El problema de los datos perdidos (missing data)

Uno de los problemas que aparece en el análisis de datos con frecuencia es la existencia de **datos** **faltantes** o **ausentes**, también llamados **datos missing** (en inglés).

Se producen cuando las observaciones de nuestros datos no están completas. La presencia de datos missing en una muestra puede ser debida a numerosas causas:

* Ausencia natural de la información que se busca
* Una observación errónea o dato atípico que habremos convertido en missing en el proceso de detección y tratamiento
* Una falta de respuesta, por ejemplo en una encuesta

Será importante un buen diseño de la muestra para impedir en lo posible esta información faltante. Por ejemplo, si se trabaja con cuestionarios, es fundamental además de elaborarlos adecuadamente, enseñar al personal para que todas las preguntas sean respondidas.&#x20;

## Datos missing en R

**`NA`**&#x20;

Es como se representa en R los datos perdidos o missing. Recuerda que en el [Tema 1 ](../../tema-1-analisis-exploratorio-de-datos/codificacion-y-transformacion/#deteccion-inicial-de-errores)vimos que los datos missing pueden estar representados de muchas formas (**"99", "Missing", "Unknown", " "**) y hay que detectarlos y convertirlos en `NA`&#x20;

**Versiones especiales de `NA:`**

**`NULL`**

Es un valor especial que se utiliza para representar la ausencia de un valor o la falta de datos. Se trata de un objeto nulo, y se puede utilizar en diversas situaciones para indicar que una variable o expresión no tiene un valor asignado.

**`NaN`**

Son valores imposibles, por ejemplo cuando fuerzas a R a dividir 0 por 0.

**`Inf`**

Representa un valor infinito, como por ejemplo si fuerzas a R a dividir un número por 0&#x20;

Ejemplo ilustrativo de como esto puede afectar a tus análisis:

```r
z <- c(1,7, 22, NA, Inf, NaN, 5, 8, 10, 15, NA, 12)
max(z)                           # returns NA
max(z, na.rm=T)                  # returns Inf
max(z[is.finite(z)])             # returns 22
```

“NAs introduced by coercion” es un warning común en R. Por ejemplo, si intentas convertir en numérico un vector que tiene caracteres:

```r
as.numeric(c("10", "20", "thirty", "40"))
## Warning message: NAs introduced by coercion
## [1] 10 20 NA 40
```

## Funciones útiles en R

#### <mark style="color:green;">`is.na()`</mark> y <mark style="color:green;">`!is.na()`</mark>

Estas funciones devuelven un valor lógico (**`TRUE`** or **`FALSE`**)

```r
my_vector <- c(1, 4, 56, NA, 5, NA, 22)
is.na(my_vector)
## [1] FALSE FALSE FALSE  TRUE FALSE  TRUE FALSE

!is.na(my_vector)
## [1]  TRUE  TRUE  TRUE FALSE  TRUE FALSE  TRUE

sum(is.na(my_vector))
## [1] 2
```

#### <mark style="color:green;">`na.omit()`</mark>

Esta función quitará los datos _missing_, si lo aplicas a un vector quitará los datos `NA` y si es sobre un data frame quitará las filas donde haya un valor _missing_.

```r
na.omit (my_vector)
## [1]  1  4 56  5 22
## attr(,"na.action")
## [1] 4 6
## attr(,"class")
## [1] "omit"
```

#### <mark style="color:green;">`na.rm = TRUE`</mark>

Cuando se utilizan funciones matemáticas como <mark style="color:green;">**`max()`**</mark><mark style="color:green;">**,**</mark><mark style="color:green;">**&#x20;**</mark><mark style="color:green;">**`min()`**</mark><mark style="color:green;">**,**</mark><mark style="color:green;">**&#x20;**</mark><mark style="color:green;">**`sum()`**</mark> <mark style="color:green;"></mark><mark style="color:green;">or</mark> <mark style="color:green;"></mark><mark style="color:green;">**`mean()`**</mark>si hay valores NA los eliminara para hacer el cálculo

```r
my_vector <- c (1, 4, 56, NA, 5, NA, 22)

mean(my_vector)     
## [1] NA

mean(my_vector, na.rm = TRUE)
## [1] 17.6
```

## Cuantificar y visualizar datos missing en una base de datos&#x20;

Para ello vamos a usar una base de datos de simulación de una **epidemia de Ebola**&#x20;

{% file src="../../.gitbook/assets/linelist (1).Rdata" %}

y un paquete de R que se llama <mark style="color:orange;">**`naniar`**</mark>

```r
#install.packages("naniar")
library(naniar)

load("Tema_3/linelist.Rdata")
head(data)
str(data)
summary(data)

```

Con estos simples comando podemos ver y entender la base de de datos. Asumimos que no hay ni errores ni datos atípicos.&#x20;

### Cuantificación&#x20;

El número de datos missing por variable lo podemos ver con un simple <mark style="color:green;">**`summary()`**</mark>

Para saber el **número** y **porcentaje** de valores que son missing a nivel global usamos las funciones <mark style="color:green;">**`pct_miss()`**</mark> , <mark style="color:green;">**`n_miss()`**</mark> y <mark style="color:green;">**`pct_complete_case()`**</mark>

```r
# percent of ALL data frame values that are missing
pct_miss(data)

#Number of missing data
n_miss(data)

# Percent of rows that are complete (no values missing)  
pct_complete_case(data) # use n_complete() for counts

```

### Visualización&#x20;

Para visualizar los datos missing podemos hacer uso de la función <mark style="color:green;">**`gg_miss_var()  o vis_miss()`**</mark>que nos muestra el % de valores missing que hay en cada columna. Con esta función se puede:

```r
gg_miss_var(data, show_pct = TRUE)
```

<figure><img src="../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

Si queremos visualizar los datos missing por sexo haríamos uso del arguemnto <mark style="color:green;">**`facet =`**</mark>

```r
gg_miss_var(data, show_pct = TRUE,facet = gender)
```

<figure><img src="../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>

Una cosa interesante que vemos al clasificar los datos por sexo, es que los valores perdidos de la variable edad corresponden a los valores perdidos de la variable sexo.&#x20;

Puedes utilizar <mark style="color:green;">**`vis_miss()`**</mark> para visualizar el dataframe como un heatmap donde se muestra en forma de matriz de datos si cada valor está ausente o no. También puedes usar <mark style="color:green;">**`select()`**</mark> para elegir ciertas columnas del dataframe y proporcionar solo esas columnas a la función.

El orden en el que aparecen las observaciones es el mismo que en la base de datos, pero con la opción <mark style="color:green;">**`cluster = TRUE`**</mark>, se agrupan las filas con patrones similares de valores faltantes mediante clustering jerárquico.

```r
vis_miss(data)
help("vis_miss")
vis_miss(data)  + 
  theme(axis.text.x = element_text(angle = 90))
vis_miss(select(data,outcome,gender,age))

vis_miss(data, cluster=TRUE)
```

<figure><img src="../../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (261).png" alt=""><figcaption></figcaption></figure>

## **Exploración y visualización de datos missing entre dos variables**

### **Dos variables cuantitativas**

¿Cómo visualizamos algo que está perdido? <mark style="color:green;">**`ggplot()`**</mark>por ejemplo elimina las observaciones con valores missing.&#x20;

Si creo un gráfico de dispersión entre dos variables cuantitativas, por ejemplo <mark style="color:purple;">age</mark> y <mark style="color:purple;">temp</mark> con <mark style="color:green;">**`ggplot()`**</mark>

```r
library(ggplot2)
ggplot(data = data, aes (x = age, y = temp)) +  geom_point()
```

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

El paquete <mark style="color:green;">**`naniar`**</mark> ofrece una solución con <mark style="color:green;">**`geom_miss_point()`**</mark>. Al crear un gráfico de dispersión de dos columnas, los registros con uno de los valores faltantes y el otro valor presente se muestran configurando los valores faltantes en un 10% menos que el valor más bajo en la columna, y se les asigna un color distintivo.

En el gráfico de dispersión a continuación, los puntos rojos representan registros donde el valor de una columna está presente, pero el valor de la otra columna falta. Esto permite visualizar la distribución de los valores faltantes en relación con los valores no faltantes.

```r

ggplot(data = data, aes (x = age, y = temp)) + geom_miss_point()
```

<figure><img src="../../.gitbook/assets/image (196).png" alt=""><figcaption></figcaption></figure>

### **Estratificado por una variable categórica**

Para evaluar la ausencia de datos estratificados por otra variable, podemos utilizar <mark style="color:green;">**`gg_miss_fct()`**</mark>, que devuelve un heatmap del porcentaje de datos faltantes según la variable categórica que le digamos:

```r
gg_miss_fct(data, age_cat5)
```

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

Esta función también la podemos usar para ver la evolución de los datos missing en el tiempo si tenemos variables que nos indiquen fechas:

```
gg_miss_fct(data, date_onset)
```

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

También se puede hacer uso de la función<mark style="color:green;">**`bind_shadow()`**</mark> para ver la proporción de missing de una variable respecto a la distribución de otra variable. Esta función crea una columna binaria `NA/no_NA` para cada columna existente y une todas estas nuevas columnas al conjunto de datos original con el apéndice `"_NA"`.

```r
shadowed_data <- data %>% bind_shadow()
names(shadowed_data)
```

```r
 [1] "case_id"                 "date_infection"          "date_onset"             
 [4] "date_hospitalisation"    "date_outcome"            "outcome"                
 [7] "gender"                  "age"                     "age_cat"                
[10] "age_cat5"                "hospital"                "lon"                    
[13] "lat"                     "infector"                "ct_blood"               
[16] "fever"                   "chills"                  "cough"                  
[19] "aches"                   "vomit"                   "temp"                   
[22] "time_admission"          "days_onset_hosp"         "case_id_NA"             
[25] "date_infection_NA"       "date_onset_NA"           "date_hospitalisation_NA"
[28] "date_outcome_NA"         "outcome_NA"              "gender_NA"              
[31] "age_NA"                  "age_cat_NA"              "age_cat5_NA"            
[34] "hospital_NA"             "lon_NA"                  "lat_NA"                 
[37] "infector_NA"             "ct_blood_NA"             "fever_NA"               
[40] "chills_NA"               "cough_NA"                "aches_NA"               
[43] "vomit_NA"                "temp_NA"                 "time_admission_NA"      
[46] "days_onset_hosp_NA"     
```

Estas variables "shadow" se pueden utilizar para representar la proporción de valores que faltan en relación con otra columna.

Por ejemplo, el gráfico a continuación muestra la proporción de registros que faltan según el valor del registro del día de hospitalización (<mark style="color:purple;">date\_hospitalisation)</mark> estratificando los resultados (<mark style="color:green;">**`color =`**</mark>) según una columna sombra de interés. En este caso la edad de los pacientes (<mark style="color:purple;">age</mark>).&#x20;

```r
ggplot (data = shadowed_data, 
        mapping = aes(x = date_hospitalisation,       
        colour = age_NA)) +   
geom_density()                  
```

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

En este caso observamos que los datos missing de la variable edad se corresponde a fechas mas recientes.&#x20;

También se pueden usar variables categóricas para esta representación, por ejemplo si los missing de los síntomas (<mark style="color:purple;">`fever_NA`</mark>) están relacionados con la variable <mark style="color:purple;">`outcome`</mark>.

```r
ggplot(shadowed_data, aes(x = outcome, fill = fever_NA)) +
  geom_bar(position = "fill")
```

<figure><img src="../../.gitbook/assets/image (262).png" alt=""><figcaption></figcaption></figure>
