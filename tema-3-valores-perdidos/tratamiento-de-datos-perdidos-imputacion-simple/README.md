---
hidden: true
cover: ../../.gitbook/assets/missing (1).jpeg
coverY: 0
---

# Tratamiento de datos perdidos (Imputación Simple)

En este apartado vamos a ver como realizar una imputación simple de los datos perdidos

## Imputación de datos perdidos

Aunque siempre se puede analizar un conjunto de datos después de eliminar todos los valores perdidos, esto puede causar varios problemas. Aquí tienes dos ejemplos:

* Al eliminar todas las observaciones con valores perdidos o variables con una gran cantidad de datos perdidos, puedes reducir tu poder o capacidad para realizar algunos tipos de análisis, como se ha visto en el apartado anterior.
* Dependiendo de por qué faltan tus datos, el análisis solo de datos no faltantes podría conducir a resultados **sesgados** o engañosos. Por ejemplo, como se vió anteriormente, faltan datos de algunos pacientes sobre si han tenido síntomas importantes como fiebre o tos. Pero, como posibilidad, tal vez esa información no se registró para personas que obviamente no estaban muy enfermas (**MAR/MNAR**). En ese caso, si simplemente eliminamos esas observaciones, estaríamos excluyendo a algunas de las personas más saludables en nuestro conjunto de datos y eso podría sesgar realmente cualquier resultado.

A continucación veremos distintas formas de imputación y cuándo son más recomendables unas respecto a otras.&#x20;

### Imputación usando la media (v.continuas) o moda (v.categóricas o v.discretas)&#x20;

Esta es la forma más sencilla de imputación cuando puedes asumir que los datos son **MCAR y el % de datos faltante es pequeño.** Si las variables son continuas puedes simplemente establecer los valores perdidos usando la media de esa variable.&#x20;

Vamos a asumir que las mediciones de temperatura (<mark style="color:purple;">`temp`</mark>) faltantes en nuestro conjunto de datos son **MCAR** y que por tanto los podemos imputar con la media ya que el % de datos faltantes es pequeño ( 3%)

¡Cuidado! en muchas situaciones reemplazar los datos con la media puede generar sesgos si el porcentaje de missing es elevado.&#x20;

#### **Imputamos por la media y comprobamos gráficamente la distribución:**

```r
data$temp_imp<-data$temp #Nueva variable
mean(data$temp_imp, na.rm = TRUE) #Media
# 38.55829
data$temp_imp[is.na(data$temp_imp)] <- mean(data$temp_imp, na.rm = TRUE) #Imputación

##Gráfico para representar las diferencias  
ggplot(data, aes(x = temp, fill = "temp")) +  
geom_density(alpha = 0.5) +  
geom_density(aes(x = temp_imp, fill = "temp_imp"), alpha = 0.5) 
```

<figure><img src="../../.gitbook/assets/image (222).png" alt=""><figcaption></figcaption></figure>

Tras imputar por la media se ve como la distribución se desplaza hacia el valor 38.5 que es el valor de la media. Quizás esta forma no sea la mejor, puesto que hay claramente una distribución bimodal con un pico en 37 seguramente correspondiente a los que no tienen fiebre y un pico en 39 correspondiente a aquellos que si tienen fiebre. En el siguiente apartado veremos como mejorar esta imputación.&#x20;

#### **Imputación por la moda en variables categóricas o discretas:**&#x20;

En el caso de las variables categóricas la forma de imputar valores perdidos de forma sencilla es usando la moda. Por ejemplo, asumimos que los valores faltantes de la variable <mark style="color:purple;">`gender`</mark> (variable categórica) son MCAR y sólo tienen un 5% de missing, por tanto se podrían representar por la categoría más frecuente ("_f_" en este caso):

```r
#Comprobar la categoría más frecuente
prop.table(table(data$gender,useNA = "always"))
         f          m       <NA> 
0.47673234 0.47605299 0.04721467 

## Sustituir los NA con la categoría más frecuente
data$gender_imp <-data$gender
data$gender_imp[is.na(data$gender_imp)] <- "f"

## Volvemos a comprobar
prop.table(table(data$gender_imp,useNA = "always"))
       f        m     <NA> 
0.523947 0.476053 0.000000 

# Graficar la distribución de la variable categórica antes y después de la imputación
par(mfrow = c(1, 2)) # Organizar las gráficas en una fila de 2 columnas
barplot(prop.table(table(data$gender, useNA = "ifany")), main = "Antes de la imputación", col = "#F8766D" )
barplot(prop.table(table(data$gender_imp)), main = "Después de la imputación",col = "#00BFC4")


```

<figure><img src="../../.gitbook/assets/image (299).png" alt=""><figcaption></figcaption></figure>

Vemos que la categoría female "f" aumenta respecto a la de male "m" ya que un 5% ya es un número considerable, pero no distorsiona demasiado, así que se podría dar por válido en el caso de querer un método de imputación muy sencillo.&#x20;

### Imputación usando modelos de regresión

Un método algo más avanzado es utilizar algún tipo de modelo estadístico para predecir cuál podría ser el valor perdido y reemplazarlo con el valor predicho.&#x20;

Dependiendo de si la variable que queremos imputar es continua o categórica, usaremos un modelo de **regresión lineal** (continua) o **regresión logística/multinomial** (categórica).&#x20;

En este caso, vamos a ver como imputar la variable <mark style="color:purple;">`temp`</mark> con valores predichos usando una regresión lineal con la variable <mark style="color:purple;">`fever`</mark>  como predictora para compararlo con la imputación por la media que veíamos en el apartado anterior.&#x20;

Lo primero será comprobar que los valores faltantes de la variable <mark style="color:purple;">`temp`</mark> no lo son en la variable <mark style="color:purple;">`fever`</mark>, si no, esos valores no se podrán imputar:

```r
vis_miss(select(data,temp,fever))
```

Después ajustamos un modelo de regresión lineal de la forma <mark style="color:purple;">`temp ~ fever`</mark>

```r
#Ajustar un modelo de regresión lineal de temp ~ fever
model1 <- lm(temp ~ fever, data = data)

#Predecir los valores solo para las observaciones faltantes 
predictions <- predict(model1,newdata = data [is.na(data$temp),]) 

##Crear una nueva variable de linelist con la temperatura imputada
data$temp_imp_model1 <- data$temp  
data$temp_imp_model1[is.na(data$temp)]<- predictions

```

<pre class="language-r"><code class="lang-r"><strong>summary(model1)
</strong>
Call:
lm(formula = temp ~ fever, data = data)

Residuals:
     Min       1Q   Median       3Q      Max 
-1.77314 -0.32113 -0.02113  0.32686  1.77887 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept) 36.97314    0.01451  2548.3   &#x3C;2e-16 ***
feveryes     2.04798    0.01613   126.9   &#x3C;2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.4701 on 5488 degrees of freedom
  (398 observations deleted due to missingness)
Multiple R-squared:  0.7459,	Adjusted R-squared:  0.7459 
F-statistic: 1.611e+04 on 1 and 5488 DF,  p-value: &#x3C; 2.2e-16
</code></pre>

En esta regresión, el modelo explica un un 74% de la variabilidad de temp (R<sup>2</sup> = 0.74) con un p-valor muy pequeño, señalando que existe una asociación entre las dos variables. Por tanto este modelo se puede usar para predecir de la siguiente forma:

$$
temp = 36.97 + 2.05*(fever=yes)
$$

Cuando los individuos no tiene fiebre (`fever=no`) la predicción será 36.97 y cuando si tengan (`fever=yes`) la predicción será 36.97+2.05 = 39.02

```r
#Hacer un gráfico para comparar las observaciones
ggplot(data, aes(x = temp, fill = "temp")) +  geom_density(alpha = 0.5) +  
geom_density(aes(x = temp_imp_model1, fill = "temp_imp_model1"), alpha = 0.5)

# Hacer un gráfico para comparar las observaciones con la media y la regresión
ggplot(data, aes(x = temp, fill = "temp")) +  geom_density(alpha = 0.5) +  
geom_density(aes(x = temp_imp_model1, fill = "temp_imp_model1"), alpha = 0.5) +  
geom_density(aes(x = temp_imp, fill = "temp_imp_mean"), alpha = 0.5)
```

<figure><img src="../../.gitbook/assets/image (300).png" alt="" width="563"><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (301).png" alt="" width="563"><figcaption></figcaption></figure>

En el prmer gráfico se pueden ver los dos picos correspondientes a los dos valores de la predicción y en el segundo la comparación con la imputación por la media, viendo claramente como el modelo de regresión hace una imputación mucho más robusto ya que tiene en cuanto información extra sobre si los individuos tienen o no tienen fiebre.&#x20;

### Imputación mediante regresión lineal + error estocástico

La imputación por regresión reemplaza los valores perdidos por la predicción puntual del modelo. Eso suele infraestimar la variabilidad (las imputaciones quedan “demasiado perfectas”). Para aproximar mejor la dispersión real, podemos añadir un término aleatorio a cada predicción:

Este término aleatorio añade un error estocástico a nuestras predicciones. Este error estocástico es una variable aleatoria que sigue una distribución normal $$\varepsilon_i^{\ast} \sim \mathcal{N}(0,\sigma^2)\$$donde  $$\hat{\sigma}$$ es el error estándar residual del modelo de ajuste

```r
summary(model1) ##Cogemos el residual standard error
set.seed(3)
inc<-rnorm(sum(is.na(data$temp)), 0, sd = 0.47) 

data$temp_imp_model1_inc <- data$temp  
data$temp_imp_model1_inc[is.na(data$temp)]<- predictions + inc

#Hacer un gráfico para comparar las observaciones con la media y la regresión
ggplot(data, aes(x = temp, fill = "temp")) +
  geom_density(alpha = 0.5) +
  geom_density(aes(x = temp_imp_model1, fill = "temp_imp_model1"), alpha = 0.5) +
  geom_density(aes(x = temp_imp_model1_inc, fill = "temp_imp_model1_inc"), alpha = 0.5) 

```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

En este caso, como se ha metido un error estocástico, vemos un ajuste casi igual a la distribución original de la variable sin imputar
