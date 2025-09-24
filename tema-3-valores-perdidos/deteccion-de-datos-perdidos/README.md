---
cover: ../../.gitbook/assets/missing (1).jpeg
coverY: 0
---

# Detección de datos perdidos

En este apartado vamos a ver como cuantificar y visualizar los datos ausentes para su detección, pero antes veamos como se representan los datos ausentes en R

## Datos ausentes en R

**`NA`**&#x20;

Es como se representa en R los datos ausentes o missing. Recuerda que en el [Tema 1 ](../../tema-1-analisis-exploratorio-de-datos/codificacion-y-transformacion/#deteccion-inicial-de-errores)vimos que los datos missing pueden estar representados de muchas formas (**"99", "Missing", "Unknown", " "**) y hay que detectarlos y convertirlos en `NA`&#x20;

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

**“NAs introduced by coercion” es un warning común en R. Por ejemplo, si intentas convertir en numérico un vector que tiene caracteres:**

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

{% file src="../../.gitbook/assets/linelist.Rdata" %}

y un paquete de R que se llama <mark style="color:orange;">**`naniar`**</mark>

<pre class="language-r"><code class="lang-r">#install.packages("naniar")
library(naniar)


load("linelist.Rdata") ## cargamos los datos

<strong>
</strong>
str(data) ## vemos las variables
'data.frame':	5888 obs. of  20 variables:
 $ case_id             : chr  "5fe599" "8689b7" "11f8ea" "b8812a" ...
 $ date_infection      : Date, format: "2014-05-08" NA NA "2014-05-04" ...
 $ date_onset          : Date, format: "2014-05-13" "2014-05-13" "2014-05-16" "2014-05-18" ...
 $ date_hospitalisation: Date, format: "2014-05-15" "2014-05-14" "2014-05-18" "2014-05-20" ...
 $ date_outcome        : Date, format: NA "2014-05-18" "2014-05-30" NA ...
 $ outcome             : Factor w/ 2 levels "Death","Recover": NA 2 2 NA 2 2 2 1 2 1 ...
 $ gender              : Factor w/ 2 levels "f","m": 2 1 2 1 2 1 1 1 2 1 ...
 $ age                 : num  2 3 56 18 3 16 16 0 61 27 ...
 $ hospital            : Factor w/ 5 levels "Central Hospital",..: 3 NA 5 4 2 4 NA NA NA NA ...
 $ lon                 : num  -13.2 -13.2 -13.2 -13.2 -13.2 ...
 $ lat                 : num  8.47 8.45 8.46 8.48 8.46 ...
 $ infector            : chr  "f547d6" NA NA "f90f5f" ...
 $ ct_blood            : int  22 22 21 23 23 21 21 22 22 22 ...
 $ fever               : Factor w/ 2 levels "no","yes": 1 NA NA 1 1 1 NA 1 1 1 ...
 $ chills              : Factor w/ 2 levels "no","yes": 1 NA NA 1 1 1 NA 1 1 1 ...
 $ cough               : Factor w/ 2 levels "no","yes": 2 NA NA 1 2 2 NA 2 2 2 ...
 $ aches               : Factor w/ 2 levels "no","yes": 1 NA NA 1 1 1 NA 1 1 1 ...
 $ vomit               : Factor w/ 2 levels "no","yes": 2 NA NA 1 2 2 NA 2 2 1 ...
 $ temp                : num  36.8 36.9 36.9 36.8 36.9 37.6 37.3 37 36.4 35.9 ...
 $ days_onset_hosp     : int  2 1 2 2 1 1 2 1 1 2 ...

</code></pre>

```r
summary(data) ## resumimos las variables
case_id          date_infection         date_onset         date_hospitalisation  date_outcome           outcome      gender    
 Length:5888        Min.   :2014-03-19   Min.   :2014-04-07   Min.   :2014-04-17   Min.   :2014-04-19   Death  :2582   f   :2807  
 Class :character   1st Qu.:2014-09-06   1st Qu.:2014-09-16   1st Qu.:2014-09-19   1st Qu.:2014-09-26   Recover:1983   m   :2803  
 Mode  :character   Median :2014-10-11   Median :2014-10-23   Median :2014-10-23   Median :2014-11-01   NA's   :1323   NA's: 278  
                    Mean   :2014-10-22   Mean   :2014-11-03   Mean   :2014-11-03   Mean   :2014-11-12                             
                    3rd Qu.:2014-12-05   3rd Qu.:2014-12-19   3rd Qu.:2014-12-17   3rd Qu.:2014-12-28                             
                    Max.   :2015-04-27   Max.   :2015-04-30   Max.   :2015-04-30   Max.   :2015-06-04                             
                    NA's   :2087         NA's   :256                               NA's   :936                                    
      age                                        hospital         lon              lat          infector            ct_blood      fever     
 Min.   : 0.00   Central Hospital                    : 454   Min.   :-13.27   Min.   :8.446   Length:5888        Min.   :16.00   no  :1090  
 1st Qu.: 6.00   Military Hospital                   : 896   1st Qu.:-13.25   1st Qu.:8.461   Class :character   1st Qu.:20.00   yes :4549  
 Median :13.00   Other                               : 885   Median :-13.23   Median :8.469   Mode  :character   Median :22.00   NA's: 249  
 Mean   :16.01   Port Hospital                       :1762   Mean   :-13.23   Mean   :8.470                      Mean   :21.21              
 3rd Qu.:23.00   St. Mark's Maternity Hospital (SMMH): 422   3rd Qu.:-13.22   3rd Qu.:8.480                      3rd Qu.:22.00              
 Max.   :84.00   NA's                                :1469   Max.   :-13.21   Max.   :8.492                      Max.   :26.00              
 NA's   :85                                                                                                                                 
  chills      cough       aches       vomit           temp       days_onset_hosp 
 no  :4540   no  : 773   no  :5095   no  :2836   Min.   :35.20   Min.   : 0.000  
 yes :1099   yes :4866   yes : 544   yes :2803   1st Qu.:38.20   1st Qu.: 1.000  
 NA's: 249   NA's: 249   NA's: 249   NA's: 249   Median :38.80   Median : 1.000  
                                                 Mean   :38.56   Mean   : 2.059  
                                                 3rd Qu.:39.20   3rd Qu.: 3.000  
                                                 Max.   :40.80   Max.   :22.000  
                                                 NA's   :149     NA's   :256     
```

Vemos que el dataframe contiene 5888 observaciones y  20 variables, con númerosos `NA's` en muchas de sus variables. En este caso, asumimos que no hay ni errores ni datos atípicos.&#x20;

### Cuantificación&#x20;

El primer interés es saber cuántos datos missing tiene la base de datos, tanto por variable como global.

El número de datos missing por variable lo podemos ver con  <mark style="color:green;">**`summary()`**</mark>

Para saber el **número** y **porcentaje** de valores que son missing a nivel global usamos las funciones <mark style="color:green;">**`pct_miss()`**</mark> , <mark style="color:green;">**`n_miss()`**</mark> y <mark style="color:green;">**`pct_complete_case()`**</mark>

```r
# Porcentaje de datos missing en todo el dataset
pct_miss(data)
8.637908

# Número de datos missing en todo el dataset
n_miss(data)
10172

# Porcentaje de filas completas si borramos todas las filas que contienen al menos un datos missing
pct_complete_case(data) 
26.81726

# Número de filas completas si borramos todas las filas que contienen al menos un datos missing
n_case_complete(data)
1579
```

En este caso, vemos que el porcentaje de datos missing global no es demasiado (un 8.64%), pero si no los tratamos y solo los borramos, nos quedaríamos con sólo el 27% de la base de datos, que corresponden a **1579** observaciones de las **5888** de las que partíamos, por eso, tratar de forma adecuada los datos perdidos es muy importante para los análisis posteriores. Hay dos cosas que se pueden hacer:&#x20;

* Borrarlos
* Imputarlos

La decisión muchas veces vendrá dada por el número de datos perdidos, los análisis posteriores que se quieran realizar, si queremos hacer análisis univariante o multivariante, etc.

La decisión de borrarlos puede tener mucho impacto en nuestros análisis posteriores. Imagina que queremos ajustar una recta de regresión para predecir los días desde la aparición de síntomas hasta la hospitalización (<mark style="color:purple;">`days_onset_hosp`</mark>) con la temperatura (fiebre) (<mark style="color:purple;">`temp`</mark>) en el ejemplo de la **epidemia de Ebola** (**`linelist`**)&#x20;

1\) Borrar todas aquellas filas que tienen al menos un missing (n=1579):

```r
data_complete<-data[complete.cases(data),]
dim(data_complete)
[1] 1579   20
```

```r
summary(glm(days_onset_hosp ~ temp, data = data_complete, family = poisson))
```

Usamos regresión de Poisson por como se distribuye la variable <mark style="color:purple;">`days_onset_hosp`</mark>

```r
Call:
glm(formula = days_onset_hosp ~ temp, family = poisson, data = data_complete)

Coefficients:
            Estimate Std. Error z value Pr(>|z|)
(Intercept)  0.21307    0.72794   0.293     0.77
temp         0.01302    0.01884   0.691     0.49

(Dispersion parameter for poisson family taken to be 1)

    Null deviance: 3258.9  on 1578  degrees of freedom
Residual deviance: 3258.4  on 1577  degrees of freedom
AIC: 6582.8

Number of Fisher Scoring iterations: 5
```

En este caso, vemos como la variable <mark style="color:purple;">`temp`</mark> no está asociada con la variable <mark style="color:purple;">`days_onset_hosp`</mark>  (p-valor = 0.49) y por tanto no podríamos decir que un aumento en la temperatura corporal (fiebre) provoca más días de hospitalización.&#x20;

2\) No borramos nada (n=5888)

```r
summary(glm(days_onset_hosp ~ temp, data = data, family = poisson))

```

```r
Call:
glm(formula = days_onset_hosp ~ temp, family = poisson, data = data)

Coefficients:
            Estimate Std. Error z value Pr(>|z|)    
(Intercept) -1.71045    0.38854  -4.402 1.07e-05 ***
temp         0.06243    0.01006   6.208 5.36e-10 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

(Dispersion parameter for poisson family taken to be 1)

    Null deviance: 11496  on 5482  degrees of freedom
Residual deviance: 11457  on 5481  degrees of freedom
  (405 observations deleted due to missingness)
AIC: 22812

Number of Fisher Scoring iterations: 5

```

En este caso, si podemos decir que la variable <mark style="color:purple;">`temp`</mark>  está asociada con la variable <mark style="color:purple;">`days_onset_hosp`</mark>  (p-valor = 5.36e-10) y por tanto podemos decir que un aumento en la temperatura corporal (fiebre) provoca más días de hospitalización.&#x20;

**Por tanto es muy importante detectar y tratar de forma adecuada los datos faltantes sino quieres resultados sesgados o incluso falsos.**&#x20;

Para hacer un buen tratamiento es importante pensar en por qué tus datos podrían faltar, además de cuantificarlos. Hacer esto puede ayudarte a decidir qué tan importante podría ser imputar datos faltantes y también qué método de imputación de datos faltantes podría ser la mejor en tu situación.

### Tipos de datos missing

En estadística y ciencia de datos hablamos de tres tipos de datos mmissing:

1.  **Missing Completely at Random (MCAR):** Son datos perdidos completamente aleatorios, es decir, la ausencia de valores en un conjunto de datos es **independiente** tanto **de otras variables** observadas como de las no observadas.&#x20;

    **Ejemplo**: en una encuesta online, algunos participantes olvidan responder una pregunta sobre el **color favorito** porque el sistema falla en un 5% de los casos al guardar la respuesta. El error ocurre de manera aleatoria y no depende de ninguna variable como la edad, género, ni del propio color elegido.
2.  **Missing at Random (MAR):** Son datos perdidos aleatorios, es decir, la probabilidad de que un valor falte puede **depender de otras variables observadas**, pero no de las no observadas. El nombre puede confundir porque no son realmente aleatorios.

    **Ejemplo**: en un estudio psicológico, las personas más **mayores** son menos propensas a responder a preguntas sobre **uso de redes sociales**. Aquí, la ausencia está relacionada con la variable observada _edad y &#x63;_&#x6F;mo la conocemos, podemos modelar y compensar esta ausencia.
3. **Missing not at Random (MNAR):** Datos perdidos de forma no aleatoria, es decir, la probabilidad de que falte un valor **depende de una variable no observada**. Esto puede introducir sesgos en los datos y es más complicado de manejar. En otras palabras, la falta de un valor está relacionada con la información que falta y no puede ser modelada únicamente en función de las variables observadas. **Ejemplo**: en una encuesta sobre **ingresos anuales**, los participantes con sueldos muy **altos** tienden a no contestar esa pregunta. La probabilidad de ausencia está directamente relacionada con el valor perdido (el ingreso), por lo que es mucho más difícil de corregir.

Tratar con datos **MNAR** (Missing not at Random) puede ser más complicado que tratar con datos **MCAR** (Missing Completely at Random) o **MAR** (Missing at Random) porque la falta de información está relacionada con la información que falta.&#x20;

### Visualización&#x20;

Antes de imputar o eliminar, es esencial **ver** dónde y cómo faltan los datos. Con <mark style="color:green;">**`gg_miss_var()`**</mark> cuantificamos el porcentaje de `NA` por variable (y por subgrupos con <mark style="color:green;">**`facet =`**</mark>). Con <mark style="color:green;">**`vis_miss()`**</mark> inspeccionamos patrones fila–columna, y con <mark style="color:green;">**`geom_miss_point()`**</mark> detectamos relaciones entre dos cuantitativas cuando una falta. Las columnas `sombra` creadas con <mark style="color:green;">**`bind_shadow()`**</mark> permiten tratar la ausencia `(NA/no_NA)` como una variable más y estudiar su relación con covariables o con el tiempo.&#x20;

1\) **Panorama global por variable**&#x20;

```r
gg_miss_var(data, show_pct = TRUE)
```

<figure><img src="../../.gitbook/assets/image (264).png" alt=""><figcaption></figcaption></figure>

Aquí vemos que <mark style="color:purple;">`infector`</mark> y <mark style="color:purple;">`date_infection`</mark> tienen alrededor de 35% de datos ausentes. <mark style="color:purple;">`hospital`</mark>, <mark style="color:purple;">`outcome`</mark> y <mark style="color:purple;">`date_outcome`</mark> tienen entre 20–25% de datos ausentes y el resto menos del 5%. Esto es importante porque si el número de datos ausente supera el 5% en las variables, estas pueden ser muy poroblemáyicas y, por tanto, habrá que ver como de relevantes son para el análisis.

* La ausencia **no es uniforme**: unas pocas variables concentran la mayoría de los NA.\
  → Esto ya **descarta MCAR global** (completamente aleatorio).

También podemos visulizar el dataframe como un heatmap donde se muestra en forma de matriz de datos si cada valor está ausente o no con <mark style="color:green;">**`vis_miss()`**</mark>. Se puede usar <mark style="color:green;">**`select()`**</mark> para elegir ciertas columnas del dataframe y proporcionar solo esas columnas a la función.&#x20;

El orden en el que aparecen las observaciones es el mismo que en la base de datos, pero con la opción <mark style="color:green;">**`cluster = TRUE`**</mark>, se pueden identificar grupos de variables con patrones similares.

```r
vis_miss(data,cluster=TRUE)  + 
  theme(axis.text.x = element_text(angle = 90))
```

<figure><img src="../../.gitbook/assets/image (266).png" alt=""><figcaption></figcaption></figure>

En este gráfico vemos una **matriz de observaciones (filas) por variables (columnas)**:

* **Gris** = dato presente.
* **Negro** = valor perdido.

En este caso, vemos que algunos **bloques verticales** (columnas) concentran los NA.&#x20;

Cuando observamos variables como puntos negros dispersos, normalmente pensamos que son MACR, y cuando hay patrones muy claros, habrá que investigar si hay posibles MAR o MNAR.&#x20;

En este caso, vemos que los datos faltantes de todos los síntomas faltan en los mismos individuos, esto habrá que ver si la ausencia puede estar ligada a algo observable (MAR). Se podría pensar que los NA en _outcome_ podrían depender de la variable _date\_outcome_ o de si el paciente sigue hospitalizado (MAR). La falta de _date\_infection_ y _infector_ podría depender de la calidad del registro por hospital o del periodo de la epidemia, por tanto ausencias ligadas a otras variables observadas (MAR). Si los casos más graves o sin desenlace son precisamente los que no tienen _outcome_, entonces la ausencia depende del propio valor no observado (ejemplo clásico de MNAR). La edad y gender se podrían deber simplemente a un fallo del registro aleatorio (MCAR).

Para visualizar esto, habría que hacer una visualizació por subgrupos

**2) Visualizar por subgrupos**

```
```

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
