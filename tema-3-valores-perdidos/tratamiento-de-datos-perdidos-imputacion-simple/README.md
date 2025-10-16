---
cover: ../../.gitbook/assets/missing (1).jpeg
coverY: 0
---

# Tratamiento de datos perdidos (Imputación Simple)

En este apartado vamos a ver como realizar una imputación simple de los datos perdidos

## Imputación de datos perdidos

Aunque siempre se puede analizar un conjunto de datos después de eliminar todos los valores perdidos, esto puede causar varios problemas. Aquí tienes dos ejemplos:

* Al eliminar todas las observaciones con valores perdidos o variables con una gran cantidad de datos perdidos, puedes reducir tu poder o capacidad para realizar algunos tipos de análisis, como se ha visto en el apartado anterior.
* Dependiendo de por qué faltan tus datos, el análisis solo de datos no faltantes podría conducir a resultados **sesgados** o engañosos. Por ejemplo, como vimos anteriormente, nos faltan datos de algunos pacientes sobre si han tenido síntomas importantes como fiebre o tos. Pero, como posibilidad, tal vez esa información no se registró para personas que obviamente no estaban muy enfermas (**MAR/MNAR**). En ese caso, si simplemente eliminamos esas observaciones, estaríamos excluyendo a algunas de las personas más saludables en nuestro conjunto de datos y eso podría sesgar realmente cualquier resultado.

<figure><img src="../../.gitbook/assets/image (34).png" alt="" width="563"><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Es importante pensar en por qué tus datos podrían faltar y clasificarlos en el tipo de datos faltantes (MCAR/MAR o MNAR), además de ver cuánto falta. Hacer esto puede ayudarte a decidir qué tan importante podría ser imputar datos faltantes y también qué método de imputación de datos faltantes podría ser la mejor en tu situación.

### Tipos de missing en el ejemplo del Ébola:

**MCAR**

* Los datos faltantes de <mark style="color:purple;">`temp`</mark> y <mark style="color:purple;">`outcome`</mark> no parecen estar relacionados con nada

```r
###Variable temp con v.cuantitativas
ggplot(data = data, aes (x = temp, y = age)) + geom_miss_point()
ggplot(data = data, aes (x = temp, y = days_onset_hosp)) + geom_miss_point()


shadowed_data <- data %>% 
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
```



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

#### Imputación usando la media (v.continuas) o moda (v.categóricas o v.discretas)&#x20;

Esta es la forma más sencilla de imputación cuando puedes asumir que los datos son **MCAR y el % de datos faltante es peqieño.** Si las variables son continuas puedes simplemente establecer los valores perdidos usando la media de esa variable.&#x20;

Vamos a asumir que las mediciones de temperatura (<mark style="color:purple;">`temp`</mark>) faltantes en nuestro conjunto de datos son **MCAR** y que por tanto los podemos imputar con la media.&#x20;

¡Cuidado! en muchas situaciones reemplazar los datos con la media puede generar sesgos si el porcentaje de missing es elevado.&#x20;

**Imputación por la media en variable continuas:**&#x20;

La variable <mark style="color:purple;">`temp`</mark> tiene un 3% de missing, podría ser una candidata a imputar por la media y comprobar gráficamente la distribución.&#x20;

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

Tras imputar por la media se ve como la distribución se desplaza hacia el valor 38.5 que es el valor de la media. Quizás esta forma no sea la mejor, puesto que hay claramente una distribución bimodal con un pico en 37 seguramente correspondiente a los que no tienen fiebre y un pico en 39 correspondiente a aquellos que si tienen fiebre. En el sioguiente apartado veremos como mejorar esta imputación.&#x20;

**Imputación por la moda en variables categóricas o discretas:**&#x20;

En el caso de las variables categóricas la forma de imputar valores perdidos de forma sencilla es usando la moda. Por ejemplo, los valores faltantes de la variable <mark style="color:purple;">`gender`</mark> (variable categórica) tienen un 5% de missing y se pueden representar por la categoría más frecuente ("_f_" en este caso):

```r
#Comprobamos la categoría más frecuente
prop.table(table(data$gender,useNA = "always"))
         f          m       <NA> 
0.47673234 0.47605299 0.04721467 

## Sustituímos los NA con la categoría más frecuente
data$gender_imp <-data$gender
data$gender_imp[is.na(data$gender_imp)] <- "f"


## Volvemos a comprobar
prop.table(table(data$gender_imp,useNA = "always"))
       f        m     <NA> 
0.523947 0.476053 0.000000 

```

<mark style="background-color:yellow;">Añadir gráfico</mark>

#### Regresión lineal para variables continuas

Un método algo más avanzado es utilizar algún tipo de modelo estadístico para predecir cuál podría ser el valor perdido y reemplazarlo con el valor predicho.&#x20;

Vamos a ver como imputar la variable <mark style="color:purple;">`temp`</mark> con valores predichos usando una regresión lineal con la variable <mark style="color:purple;">`fever`</mark>  como predictora.

Lo primero será comprobar que los valores faltantes de la variable temp no lo son en la variable fever, si no, esos valores no se podrán imputar:

```r
vis_miss(select(data,temp,fever))
```

Después ajustamos un modelo de regresión lineal de la forma `temp ~ fever`

```r
#Ajustar un modelo de regresión lineal de temperatura ~ fiebre
model1 <- lm(temp ~ fever, data = data)

#Predecir los valores solo para las observaciones faltantes 
predictions <- predict(model1,newdata = data [is.na(data$temp),]) 

##Crear una nueva variable de linelist con la temperatura imputada
data$temp_imp_model1 <- data$temp  
data$temp_imp_model1[is.na(data$temp)]<- predictions


```

<mark style="background-color:yellow;">Poner el resultado de la regresion</mark>

```r
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

