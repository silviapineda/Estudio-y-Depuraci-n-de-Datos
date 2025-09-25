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

Para ello vamos a usar una base de datos de pacientes que acuden a una clínica universitaria por síntoma gripales.&#x20;

{% file src="../../.gitbook/assets/cliniclinelist.Rdata" %}

y un paquete de R que se llama <mark style="color:orange;">**`naniar`**</mark>

<pre class="language-r"><code class="lang-r">#install.packages("naniar")
library(naniar)


load("cliniclinelist.Rdata") ## cargamos los datos
str(data)
'data.frame':	6500 obs. of  16 variables: 
$ id              : chr  "P00001" "P00002" "P00003" "P00004" ... 
$ date_onset      : Date, format: "2025-03-16" "2025-03-08" "2025-01-22" "2025-02-04" ... 
$ date_visit      : Date, format: "2025-03-17" "2025-03-09" "2025-01-24" "2025-02-10" ... 
$ outcome         : Factor w/ 2 levels "Death","Recover": 2 2 2 2 2 2 2 2 2 2 ... 
$ gender          : Factor w/ 2 levels "f","m": 2 1 2 1 2 1 1 2 1 2 ... 
$ age             : num  26 22 27 31 25 21 25 21 20 27 ... 
$ hospital        : Factor w/ 3 levels "Campus","City",..: 1 1 2 2 2 1 1 1 1 1 ... 
$ shift           : Factor w/ 3 levels "Afternoon","Morning",..: 2 2 NA 1 2 3 1 2 1 3 ... 
$ temp            : num  37 38.4 39.2 38.5 39.7 36.9 36.8 37.8 38.3 36.6 ... 
$ spo2            : num  96.9 97.4 95.7 98 96.5 99.2 97.3 96.7 95 95.9 ... 
$ wbc             : num  7 8 10.6 9.4 9.2 7.1 3 9.5 8 10.2 ... 
$ fever           : Factor w/ 2 levels "no","yes": 1 1 1 1 1 NA 1 NA 1 1 ... 
$ chills          : Factor w/ 2 levels "no","yes": 1 1 1 1 1 1 NA 1 1 NA ... 
$ cough           : Factor w/ 2 levels "no","yes": 1 1 2 1 2 NA NA 1 1 1 ... 
$ pain_score      : num  3.72 3.29 8.3 7.82 10 ... 
$ days_onset_visit: int  1 1 2 6 2 2 1 1 0 0 ... 
<strong>
</strong>



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

Este dataframe contiene 5,888 observaciones y  20 variables, con númerosos `NA's` en muchas de sus variables. En este caso, se asume que no hay ni errores ni datos atípicos.&#x20;

### Cuantificación&#x20;

El primer interés es saber cuántos datos missing tiene la base de datos, tanto por variable como global.

El número de datos missing por variable se puede ver con  <mark style="color:green;">**`summary()`**</mark>

Para saber el **número** y **porcentaje** de valores que son missing a nivel global se pueden usar las funciones <mark style="color:green;">**`pct_miss()`**</mark> , <mark style="color:green;">**`n_miss()`**</mark> y <mark style="color:green;">**`pct_complete_case()`**</mark>

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

En este caso, vemos que el porcentaje de datos missing global no es demasiado (un 8.64%), pero si no se trata y solo se borran, nos quedaríamos con sólo el 27% de la base de datos, que corresponden a **1,579** observaciones de las **5,888** de las que se partía, por eso, tratar de forma adecuada los datos perdidos es muy importante para los análisis posteriores. Hay dos cosas que se pueden hacer:&#x20;

* Borrarlos
* Imputarlos

La decisión muchas veces vendrá dada por el número de datos perdidos, los análisis posteriores que se quieran realizar, si queremos hacer análisis univariante o multivariante, etc.

La decisión de borrarlos puede tener mucho impacto en los análisis posteriores. Imagina que queremos ajustar una recta de regresión para predecir los días desde la aparición de síntomas hasta la hospitalización (<mark style="color:purple;">`days_onset_hosp`</mark>) con la temperatura (fiebre) (<mark style="color:purple;">`temp`</mark>) en el ejemplo de la **epidemia de Ebola** (**`linelist`**)&#x20;

1\) Si se borran todas aquellas filas que tienen al menos un missing (n=1579):

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

En este caso, se ve como la variable <mark style="color:purple;">`temp`</mark> no está asociada con la variable <mark style="color:purple;">`days_onset_hosp`</mark>  (p-valor = 0.49) y por tanto no se podría concluir que un aumento en la temperatura corporal (fiebre) provoca más días de hospitalización.&#x20;

2\) Si no se borra nada (n=5888)

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

En este caso, si se puede decir que la variable <mark style="color:purple;">`temp`</mark>  está asociada con la variable <mark style="color:purple;">`days_onset_hosp`</mark>  (p-valor = 5.36e-10) y por tanto se puede concluir que un aumento en la temperatura corporal (fiebre) provoca más días de hospitalización.&#x20;

**Por tanto es muy importante detectar y tratar de forma adecuada los datos faltantes sino quieres resultados sesgados o incluso falsos.**&#x20;

Para hacer un buen tratamiento es importante pensar en por qué tus datos podrían faltar, además de cuantificarlos. Hacer esto puede ayudarte a decidir qué tan importante podría ser imputar datos faltantes y también qué método de imputación de datos faltantes podría ser el mejor.&#x20;

### Tipos de datos missing

En estadística y ciencia de datos hablamos de tres tipos de datos missing:

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

Aquí se ve que <mark style="color:purple;">`infector`</mark> y <mark style="color:purple;">`date_infection`</mark> tienen alrededor de un 35% de datos ausentes. <mark style="color:purple;">`hospital`</mark>, <mark style="color:purple;">`outcome`</mark> y <mark style="color:purple;">`date_outcome`</mark> tienen entre 20–25% de datos ausentes y el resto  de variables menos del 5%. Esto es importante porque si el número de datos ausentes supera el 5% en las variables, estas pueden ser muy poroblemáticas y, por tanto, habrá que ver como de relevantes son para el análisis.

En este caso, la ausencia **no es uniforme**: unas pocas variables concentran la mayoría de los NA. Esto ya **descarta MCAR global** (completamente aleatorio).

También se puede visulizar el dataframe como un heatmap donde se muestra en forma de matriz de datos si cada valor está ausente o no con <mark style="color:green;">**`vis_miss()`**</mark>. Se puede usar <mark style="color:green;">**`select()`**</mark> para elegir ciertas columnas del dataframe y proporcionar solo esas columnas a la función.&#x20;

El orden en el que aparecen las observaciones es el mismo que en la base de datos, pero con la opción <mark style="color:green;">**`cluster = TRUE`**</mark>, se pueden identificar grupos de variables con patrones similares.

```r
vis_miss(data,cluster=TRUE)  + 
  theme(axis.text.x = element_text(angle = 90))
```

<figure><img src="../../.gitbook/assets/image (266).png" alt=""><figcaption></figcaption></figure>

En este gráfico se ve una **matriz de observaciones (filas) por variables (columnas)**:

* **Gris** = dato presente.
* **Negro** = valor perdido.

En este caso, se ve que algunos **bloques verticales** (columnas) concentran los NA.&#x20;

Cuando se observan variables como puntos negros dispersos, normalmente se puede pensar que son MCAR, y cuando hay patrones muy claros, habrá que investigar si hay posibles MAR o MNAR.&#x20;

En este caso, vemos que:

* Los datos faltantes de todos los síntomas (<mark style="color:purple;">`fever, chills, cough, chaes, vomit`</mark>) faltan en los mismos individuos, esto habrá que ver si la ausencia puede estar ligada a algo observable (MAR).&#x20;
* Se podría pensar que los NA en <mark style="color:purple;">`outcome`</mark> podrían depender de la variable <mark style="color:purple;">`date_outcome`</mark> ya que parece que los NAs de esta variable corresponden a individuos que no o de si el paciente sigue hospitalizado (MAR).&#x20;
* La falta de <mark style="color:purple;">`date_infection`</mark> y <mark style="color:purple;">`infector`</mark> podría depender de la calidad del registro por hospital o del periodo de la epidemia, por tanto ausencias ligadas a otras variables observadas (MAR).&#x20;
* Si los casos más graves o sin desenlace son precisamente los que no tienen <mark style="color:purple;">`outcome`</mark>, entonces la ausencia depende del propio valor no observado (MNAR).&#x20;
* La variable <mark style="color:purple;">`age`</mark> y <mark style="color:purple;">`gender`</mark> se podrían deber simplemente a un fallo del registro aleatorio (MCAR).

Para ver si los datos faltantes de las diferentes variables son de un tipo u otro ahora que ya tenemos una idea global de lo que pasa, podríamos hacer anaálisis por subgrupos.&#x20;

**2) Visualizar por subgrupos**

Si queremos visualizar los datos missing por <mark style="color:purple;">`gender`</mark> o <mark style="color:purple;">`outcome`</mark> haríamos uso del arguemento <mark style="color:green;">**`facet =`**</mark>

```r
gg_miss_var(data, show_pct = TRUE,facet = gender)
```

<figure><img src="../../.gitbook/assets/image (267).png" alt=""><figcaption></figcaption></figure>

Una cosa interesante que vemos al clasificar los datos por la variable <mark style="color:purple;">`gender`</mark>, es que para <mark style="color:purple;">`age`</mark> también falta en ≈30% de esos casos, por tanto hay dependencia entre la falta de género y de edad.

## **Exploración y visualización de datos missing entre dos variables**

Una vez que tenemos sospechas de posibles dependencias entre los datos missing, podemos explorar y visualizar las relaciones entre ellas:

### **Dos variables cuantitativas**

¿Cómo visualizamos algo que está perdido? <mark style="color:green;">**`ggplot()`**</mark>por ejemplo elimina las observaciones con valores missing.&#x20;

Si creo un gráfico de dispersión entre dos variables cuantitativas, por ejemplo <mark style="color:purple;">`age`</mark> y <mark style="color:purple;">`temp`</mark> con <mark style="color:green;">**`ggplot()`**</mark>

```r
library(ggplot2)
ggplot(data = data, aes (x = age, y = temp)) +  geom_point()
```

<figure><img src="../../.gitbook/assets/image (268).png" alt=""><figcaption></figcaption></figure>

Vemos con los valores missing de ambas variables no están representados en el gráfico, pero si queremos ver dependencias entre variables, podemos usar la función <mark style="color:green;">**`geom_miss_point()`**</mark>. Al crear un gráfico de dispersión de dos columnas, los registros con uno de los valores faltantes y el otro valor presente se muestran configurando los valores faltantes en un 10% menos que el valor más bajo en la columna, y se les asigna un color distintivo.

```r

ggplot(data = data, aes (x = age, y = temp)) + geom_miss_point()

```

<figure><img src="../../.gitbook/assets/image (270).png" alt=""><figcaption></figcaption></figure>

En este gráfico de dispersión, los puntos rojos representan registros donde el valor de una columna está presente, pero el valor de la otra columna falta. Esto permite visualizar la distribución de los valores faltantes en relación con los valores no faltantes. En este caso, no vemos que haya ninguna dependencia entre los valores missing de las variables con la variable observada.&#x20;

### **Estratificado por una variable categórica**

Para evaluar la ausencia de datos estratificados por otra variable, podemos usar la función<mark style="color:green;">**`bind_shadow()`**</mark> para ver la proporción de missing de una variable respecto a la distribución de otra variable. Esta función crea una columna binaria `NA/no_NA` para cada columna existente y une todas estas nuevas columnas al conjunto de datos original con el apéndice `"_NA"`.

```r
shadowed_data <- data %>% bind_shadow()
```

```r
 names(shadowed_data)
 [1] "case_id"                 "date_infection"          "date_onset"             
 [4] "date_hospitalisation"    "date_outcome"            "outcome"                
 [7] "gender"                  "age"                     "hospital"               
[10] "lon"                     "lat"                     "infector"               
[13] "ct_blood"                "fever"                   "chills"                 
[16] "cough"                   "aches"                   "vomit"                  
[19] "temp"                    "days_onset_hosp"         "case_id_NA"             
[22] "date_infection_NA"       "date_onset_NA"           "date_hospitalisation_NA"
[25] "date_outcome_NA"         "outcome_NA"              "gender_NA"              
[28] "age_NA"                  "hospital_NA"             "lon_NA"                 
[31] "lat_NA"                  "infector_NA"             "ct_blood_NA"            
[34] "fever_NA"                "chills_NA"               "cough_NA"               
[37] "aches_NA"                "vomit_NA"                "temp_NA"                
[40] "days_onset_hosp_NA"     
```

Estas variables <mark style="color:purple;">`shadow`</mark> se pueden utilizar para representar la proporción de valores que faltan en relación con otra columna y luego se pueden utilizar para ver dependencias de los valores missing con variables tanto cuantitativas como cualitativas:

#### Variables Cuantitativas

Si queremos ver si los síntomas están relacionados con alguna de las variables observadas, se puede representar la variable <mark style="color:purple;">`chills_NA`</mark> con <mark style="color:purple;">`temp`</mark>.&#x20;

```r
 ggplot (data = shadowed_data, 
        mapping = aes(x = temp,       
                      colour = chills_NA)) + geom_density()                  
```

<figure><img src="../../.gitbook/assets/image (273).png" alt=""><figcaption></figcaption></figure>

Y en este caso se observa que los NA de la variable <mark style="color:purple;">`chills`</mark>, corresponde a aquellos individuos que tienen una temperatura corporal por debajo de 38. Lo mismo pasa con el resto de síntomas, ya que todos los NA corresponden a los mismos individuos, por tanto se puede decir que hay una relación entre los NA de los síntomas con temperatura baja.&#x20;

Si queremos ver la proporción de pacientes a los que les falta la variable <mark style="color:purple;">`age`</mark> según el valor del registro del día de hospitalización <mark style="color:purple;">`date_hospitalisation`</mark> podemos estratificarla usando la variable shadow en la opción: <mark style="color:green;">**`color =`**</mark>.

```r
ggplot (data = shadowed_data, 
        mapping = aes(x = date_hospitalisation,       
        colour = age_NA)) +   
geom_density()                  
```

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

En este caso observamos que los datos missing de la variable edad se corresponde a fechas mas recientes, por lo tanto podríamos sospechar de una relación, aunque el hecho de que no todos los valores faltantes correspondan a esa dependecia, nos hace pensar que sea un MNAR.&#x20;

#### Variables Categóricas

También se pueden usar variables categóricas para esta representación, por ejemplo para ver si los missing de los síntomas <mark style="color:purple;">`fever_NA`</mark> están relacionados con la variable <mark style="color:purple;">`outcome`</mark>.

```r
ggplot(shadowed_data, aes(x = outcome, fill = fever_NA)) +
  geom_bar(position = "fill")
```

<figure><img src="../../.gitbook/assets/image (262).png" alt=""><figcaption></figcaption></figure>

En este caso, no observamos ninguna relación. En cambio si representamos los NA de cualquiera de los síntomas (<mark style="color:purple;">`chills_NA`</mark>)

```r
  ggplot (data = shadowed_data, 
        mapping = aes(x = temp,       
                      colour = chills_NA)) + geom_density()                  
```

<figure><img src="../../.gitbook/assets/image (273).png" alt=""><figcaption></figcaption></figure>

Podemos observar que los NA de la variable <mark style="color:purple;">`chills`</mark>, corresponden a aquellos individuos que tienen una temperatura corporal por debajo de 38. Lo mismo pasa con el resto de síntomas, ya que todos los NA correspondian a los mismos individuos, por tanto podemos decir que hay una relación entre los NA de los síntomas con temperatura baja, correspondiendo a quizás individuos más sanos.&#x20;
