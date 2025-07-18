---
cover: ../../.gitbook/assets/missing (1).jpeg
coverY: 0
---

# Tratamiento de datos perdidos (Imputación Simple)

Tratar de forma adecuada los datos perdidos es muy importante para los análisis posteriores. Hay dos cosas que se pueden hacer:&#x20;

* Borrarlos
* Imputarlos

La decisión muchas veces vendrá dada por el número de datos perdidos, los análisis posteriores que se quieran realizar, si queremos hacer análisis univariante o multivariante, etc...

Por lo general, cuando tenemos un número de datos perdidos muy elevado, no será buena idea conservar la variable e intentar imputarla. Un umbral máximo seguro es el 5% del total para conjuntos de datos grandes. Si los datos faltantes para una variable u observación específica son más del 5%, probablemente deberías excluir esa variable u observación. **Pero esta dicisión va a depender de muchas cosas.**&#x20;

## Borrar filas con datos perdidos

Para filtrar filas con datos perdidos, podemos usar la función  <mark style="color:green;">**`complete.cases()`**</mark>

Pero cuidado como se filtran filas

El dataset completo tiene **5888 observaciones**

```r
load("linelist.Rdata")
dim(data)
[1] 5888   23
```

Y el dataset si quitamos todas las filas con algún valor missing se reduce a **1382 observaciones**

```r
data_complete<-data[complete.cases(data),]
dim(data_complete)
[1] 1382   26
```

Si queremos ajustar una recta de regresión para predecir los días desde la aparición de síntomas (<mark style="color:purple;">`days_onset_hosp`</mark>) con la temperatura (<mark style="color:purple;">`temp`</mark>) en el ejemplo de la **epidemia de Ebola** (**`linelist`**) usando los datos filtrados:

```r
summary(glm(days_onset_hosp ~ temp, data = data_complete, family = poisson))
```

Usamos regresión de Poisson por como se distribuye la variable <mark style="color:purple;">`days_onset_hosp`</mark>

```r
Call:
glm(formula = days_onset_hosp ~ temp, family = poisson, data = data_complete)

Coefficients:
             Estimate Std. Error z value Pr(>|z|)
(Intercept)  0.723321   0.773418   0.935    0.350
temp        -0.000152   0.020026  -0.008    0.994

(Dispersion parameter for poisson family taken to be 1)

    Null deviance: 2870  on 1381  degrees of freedom
Residual deviance: 2870  on 1380  degrees of freedom
AIC: 5784.3

Number of Fisher Scoring iterations: 5
```

Obtenemos un p-valor no significativo (asumiendo todos los supuestos para aplicar una regresión de Poisson)

Vemos como hemos borrado muchísimos datos y este modelo no nos sirve para nada, pero si solo borramos las filas correspondientes a esas dos variables:&#x20;

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

Ya podemos intuir que hay un ajuste un poco mejor, con lo que **cuidado borrar todos los datos perdidos cuando solo vas a trabajar con dos variables.**

## Imputación de datos perdidos

Aunque siempre puedes analizar un conjunto de datos después de eliminar todos los valores perdidos, esto puede causar varios problemas. Aquí tienes dos ejemplos:

* Al eliminar todas las observaciones con valores perdidos o variables con una gran cantidad de datos perdidos, puedes reducir tu poder o capacidad para realizar algunos tipos de análisis, como acabamos de ver.
* Dependiendo de por qué faltan tus datos, el análisis solo de datos no faltantes podría conducir a resultados **sesgados** o engañosos. Por ejemplo, como vimos anteriormente, nos faltan datos de algunos pacientes sobre si han tenido síntomas importantes como fiebre o tos. Pero, como posibilidad, tal vez esa información no se registró para personas que obviamente no estaban muy enfermas. En ese caso, si simplemente eliminamos esas observaciones, estaríamos excluyendo a algunas de las personas más saludables en nuestro conjunto de datos y eso podría sesgar realmente cualquier resultado.

<figure><img src="../../.gitbook/assets/image (34).png" alt="" width="563"><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Es importante pensar en por qué tus datos podrían faltar, además de ver cuánto falta. Hacer esto puede ayudarte a decidir qué tan importante podría ser imputar datos faltantes y también qué método de imputación de datos faltantes podría ser la mejor en tu situación.

### Tipos de datos perdidos

Aquí hay tres tipos generales de datos perdidos:

1.  **Missing Completely at Random (MCAR):** Son datos perdidos completamente aleatorios, es decir, la ausencia de valores en un conjunto de datos es **independiente** tanto **de otras variables** observadas como de las no observadas.&#x20;

    **Ejemplo**: la ausencia de la falta en la fecha que se produjo la infección no está relacionada con ninguna característica de los pacientes, la región, etc....sino que simplemente es un dato que no se sabe.
2.  **Missing at Random (MAR):** Son datos perdidos aleatorios, es decir, la probabilidad de que un valor falte puede **depender de otras variables observadas**, pero no de las no observadas. El nombre puede confundir porque no son realmente aleatorios.

    **Ejemplo**:  la información sobre la fiebre (si,no) falta en aquellos individuos que la temperatura (fiebre) está por debajo de 37, se puede pensar que fueron individuos más sanos. En este caso, la ausencia de la información sobre la fiebre no es completamente al azar, ya que está relacionada con la falta de fiebre.&#x20;

    A pesar de que la ausencia no es completamente aleatoria, los datos MAR son más manejables que los datos "Missing Not at Random" (MNAR), ya que, en teoría, se puede modelar y abordar la falta de manera sistemática si se conocen las variables relacionadas con la ausencia. Este es un tipo común de datos faltantes.
3.  **Missing not at Random (MNAR):** Datos perdidos de forma no aleatoria, es decir, la probabilidad de que falte un valor **depende de la variable no observada**. Esto puede introducir sesgos en los datos y es más complicado de manejar. En otras palabras, la falta de un valor está relacionada con la información que falta y no puede ser modelada únicamente en función de las variables observadas.&#x20;

    **Ejemplo**: la falta de información de un hospital es debida al nivel socioeconómico de la región o a la falta de acceso sanitario, etc... por lo que no podríamos tener acceso a esa información ni parece muy fácil de predecir con otras variables.

Tratar con datos **MNAR** (Missing not at Random) puede ser más complicado que tratar con datos **MCAR** (Missing Completely at Random) o **MAR** (Missing at Random) porque la falta de información está relacionada con la información que falta.&#x20;

### Tipos de missing en el ejemplo del Ébola:

**MCAR**

* Los datos faltantes de <mark style="color:purple;">`temp`</mark> y <mark style="color:purple;">`outcome`</mark> no parecen estar relacionados con nada

<pre class="language-r"><code class="lang-r"><strong>###Variable temp con v.cuantitativas
</strong><strong>ggplot(data = data, aes (x = temp, y = age)) + geom_miss_point()
</strong>ggplot(data = data, aes (x = temp, y = days_onset_hosp)) + geom_miss_point()


shadowed_data &#x3C;- data %>% 
  bind_shadow()
  
###Variable temp con v.cualitativas
ggplot(shadowed_data, aes(x = outcome, fill = temp_NA)) +
  geom_bar(position = "fill")
  
ggplot(shadowed_data, aes(x = gender, fill = temp_NA)) +
  geom_bar(position = "fill")
  
ggplot(shadowed_data, aes(x = hospital, fill = temp_NA)) +
 geom_bar(position = "fill")
 
 
###Variable outcome con v.cuantitativa
 ggplot (data = shadowed_data,mapping = aes(x = temp,colour = outcome_NA)) +  
        geom_density() 
 
 ggplot (data = shadowed_data,mapping = aes(x = age,colour = outcome_NA)) +  
        geom_density() 
        
###Variable outcome con v.cualitativa
ggplot(shadowed_data, aes(x = gender, fill = outcome_NA)) +
 geom_bar(position = "fill")
 
 ggplot(shadowed_data, aes(x = hospital, fill = outcome_NA)) +
 geom_bar(position = "fill")
</code></pre>



**MAR**&#x20;

* Los datos faltantes de <mark style="color:purple;">`fever`</mark> están relacionados con los que no tienen fiebre en la variable <mark style="color:purple;">`temp`</mark>

```r
ggplot (data = shadowed_data, mapping = aes(x = temp, colour = fever_NA)) +  
  geom_density() 
```

* Los datos  faltantes de <mark style="color:purple;">`age`</mark> y <mark style="color:purple;">`gender`</mark> están relacionados con fechas específicas

<pre class="language-r"><code class="lang-r">shadowed_data &#x3C;- data %>% 
  bind_shadow()

<strong>ggplot (data = shadowed_data,mapping = aes(x = date_hospitalisation,colour = age_NA)) +  
</strong>        geom_density() 
ggplot (data = shadowed_data,mapping = aes(x = date_outcome, colour = age_NA)) +
         geom_density()        
ggplot (data = shadowed_data, mapping = aes(x = date_onset, colour = age_NA)) + 
          geom_density() 
ggplot (data = shadowed_data, mapping = aes(x = date_hospitalisation,colour = gender_NA)) +            
              geom_density()  
ggplot (data = shadowed_data, mapping = aes(x = date_outcome, colour = gender_NA)) +   
                geom_density()               
ggplot (data = shadowed_data, mapping = aes(x = date_onset, colour = gender_NA)) + 
              geom_density() 
</code></pre>

**MNAR**:&#x20;

* Los datos faltantes de <mark style="color:purple;">`cough, chills, aches, vomit`</mark> están relacionado con temperaturas bajas, ya que son los mismos individuos, pero hay una diferencia sustancial y es que no todos los que tienen fever = No, corresponden con síntomas = No, por tanto hay algo no observado de los datos faltantes de los síntomas que no es solo tener baja temperatura.

```r
###Síntomas con v.cuantitativa
 ggplot (data = shadowed_data,mapping = aes(x = temp,colour = cough_NA)) +  
        geom_density() 
 
  ggplot (data = shadowed_data,mapping = aes(x = temp,colour = chills_NA)) +  
        geom_density() 
 
  ggplot (data = shadowed_data,mapping = aes(x = temp,colour = aches_NA)) +  
        geom_density() 
 
 ggplot (data = shadowed_data,mapping = aes(x = temp,colour = vomit_NA)) +  
        geom_density() 
        

table(data$fever,data$chills,useNA = "always")        
```

### Imputación simple:

#### Imputación usando la media (v.continuas) o moda (v.categóricas)&#x20;

Esta es la forma más sencilla de imputación cuando puedes asumir que los datos son **MCAR.** Si las variables son continuas puedes simplemente establecer los valores perdidos usando la media de esa variable. Por ejemplo, asumamos que las mediciones de temperatura (<mark style="color:purple;">`temp`</mark>) faltantes en nuestro conjunto de datos son MCAR y por tanto los podemos imputar con la media.&#x20;

¡Cuidado! en muchas situaciones reemplazar los datos con la media puede generar sesgos si el porcentaje de missing es elevado.&#x20;

La variable <mark style="color:purple;">`temp`</mark> tiene un 3% de missing, podría ser una candidata a imputar por la media

```r
data$temp_imp<-data$temp
mean(data$temp_imp, na.rm = TRUE) 
# 38.55829
data$temp_imp[is.na(data$temp_imp)] <- mean(data$temp_imp, na.rm = TRUE) 

##Gráfico para representar las diferencias  
ggplot(data, aes(x = temp, fill = "temp")) +  
geom_density(alpha = 0.5) +  
geom_density(aes(x = temp_imp, fill = "temp_imp"), alpha = 0.5) 
```

<figure><img src="../../.gitbook/assets/image (202).png" alt=""><figcaption></figcaption></figure>

pero vemos como la distribución se desplaza hacia el valor 38.5 que es el valor de la media.&#x20;

En el caso de las variables categóricas la forma de imputar valores perdidos de forma sencilla es usando la moda. Por ejemplo, los valores faltantes de la variable <mark style="color:purple;">`gender`</mark> (variable categórica) tienen un 5% de missing y se pueden representar por la categoría más frecuente ("_f_" en este caso):

<pre class="language-r"><code class="lang-r">#Comprobamos la categoría más frecuente
prop.table(table(data$gender,useNA = "always"))
<strong>         f          m       &#x3C;NA> 
</strong>0.47673234 0.47605299 0.04721467 

## Sustituímos los NA con la categoría más frecuente
data$gender_imp &#x3C;-data$gender
data$gender_imp[is.na(data$gender_imp)] &#x3C;- "f"


## Volvemos a comprobar
prop.table(table(data$gender_imp,useNA = "always"))
       f        m     &#x3C;NA> 
0.523947 0.476053 0.000000 

</code></pre>

#### Regresión lineal

Un método algo más avanzado es utilizar algún tipo de modelo estadístico para predecir cuál podría ser el valor perdido y reemplazarlo con el valor predicho. Aquí un ejemplo de cómo crear valores predichos para todas las observaciones donde la <mark style="color:purple;">`temp`</mark> tiene datos perdidos, pero <mark style="color:purple;">`fever`</mark> no, utilizando una regresión lineal simple usando la fiebre  como predictores.&#x20;

```r
vis_miss(select(data,temp,fever))

#Ajustar un modelo de regresión lineal de temperatura ~ fiebre
model1 <- lm(temp ~ fever, data = data)

#Predecir los valores solo para las observaciones faltantes 
predictions <- predict(model1,newdata = data [is.na(data$temp),]) 

##Crear una nueva variable de linelist con la temperatura imputada
data$temp_imp_model1 <- data$temp  
data$temp_imp_model1[is.na(data$temp)]<- predictions

#Hacer un gráfico para comparar las observaciones
ggplot(data, aes(x = temp, fill = "temp")) +  geom_density(alpha = 0.5) +  
geom_density(aes(x = temp_imp_model1, fill = "temp_imp_model1"), alpha = 0.5)

# Hacer un gráfico para comparar las observaciones con la media y la regresión
ggplot(data, aes(x = temp, fill = "temp")) +  geom_density(alpha = 0.5) +  
geom_density(aes(x = temp_imp_model1, fill = "temp_imp_model1"), alpha = 0.5) +  
geom_density(aes(x = temp_imp, fill = "temp_imp_mean"), alpha = 0.5)
```

<figure><img src="../../.gitbook/assets/image (31).png" alt="" width="563"><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (203).png" alt="" width="563"><figcaption></figcaption></figure>

#### Regresión lineal + error estocástico

A veces cuando los datos faltantes tienen mucha relación con algunos valores específicos de la variable que se va a usar para imputar, podemos querer añadir un error estocástico.&#x20;

Este error lo añadimos como un término de error estocástico a nuestras predicciones. Este error estocástico es una variable aleatoria que sigue una distribución normal con media cero y desviación estándar para la que podemos usar el error estándar de la propia regresión lineal.&#x20;

```r
summary(model1) ##Cogemos el residual standard error
set.seed(3)
rnorm(sum(is.na(data$temp)), 0, sd = 0.47) 

data$temp_imp_model1_inc <- data$temp  
data$temp_imp_model1_inc[is.na(data$temp)]<- predictions + rnorm(sum(is.na(data$temp)), 0, sd = 0.47) 

#Hacer un gráfico para comparar las observaciones con la media y la regresión
ggplot(data, aes(x = temp, fill = "temp")) +
  geom_density(alpha = 0.5) +
  geom_density(aes(x = temp_imp_model1, fill = "temp_imp_model1"), alpha = 0.5) +
  geom_density(aes(x = temp_imp_model1_inc, fill = "temp_imp_model1_inc"), alpha = 0.5) 

```

<figure><img src="../../.gitbook/assets/image (5) (1).png" alt="" width="563"><figcaption></figcaption></figure>

