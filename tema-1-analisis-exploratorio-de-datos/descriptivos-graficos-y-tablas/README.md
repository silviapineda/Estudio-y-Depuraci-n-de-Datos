---
cover: ../../.gitbook/assets/image (132).png
coverY: 0
---

# Descriptivos, Gráficos y Tablas

No siempre vamos a poder realizar de forma tan sencilla la detección de errores, sobre todo cuando tenemos un gran volumen de datos, algo habitual en estadística y ciencia de datos. Por tanto, para poder detectar problemas presentes en los datos, es necesario llevar a cabo un análisis exploratorio de los mismos a través de gráficos y tablas.

Para ello es importante fijarse en los siguientes aspectos:

1. **Límites de las variables cuantitativas:** Una forma rápida de verificar que las variables cuantitativas toman valores lógicos, es verificar que el máximo y el mínimo de las variables  se encuentran dentro de los límites que marque la variable (por ejemplo, las variables que representan porcentajes deben situarse entre 0 y 100 o medidas como el peso, la altura, la edad, etc tomarán un número positivo). **Lo miraremos con descriptivos y gráficos**
2. **Niveles de las variables cualitativas**: En línea con el punto anterior, también debemos verificar que los niveles de las variables cualitativas tienen sentido, teniendo en cuenta el significado de las mismas. **Lo mirarmos con tablas de frecuencias**
3. **Número de datos perdidos**: Este tema lo veremos con mas detalle en el [Tema 3,](/broken/pages/OMVc91rK0Dccr8uQx7x7) pero si en una variable observamos muchos datos faltantes (más de la mitad), es recomendable rechazarla al inicio del proceso, pues carece de suficiente información.&#x20;
4. **Datos perdidos codificados**: En ocasiones, en lugar de dejar "huecos" para representar los datos faltantes, se utiliza algún tipo de codificación que facilite el envío y la lectura de los conjuntos de datos. Ejemplo: En variables cualitativas el símbolo `?` o el clásico `NS/NC` (no sabe/no contesta) y en variables cuantitativas, el valor `-1` (si la variable no puede tomar valores negativos) o `999`. Es fundamental detectar este aspecto para poder analizar de manera correcta la presencia de ausentes. **Lo miraremos con descriptivos, tablas y gráficos.**
5. **Frecuencia de las categorías de las variables cualitativas**: Dado que los modelos predictivos se basan en detectar patrones en las variables input que nos permitan aproximar el valor de la variable objetivo, es imprescindible que todos los niveles de las variables cualitativas estén bien representados pues, de los contrario, se podrían detectar patrones que no fueran extrapolables al estar basados en muy pocas observaciones. Por ello, se debe verificar que la frecuencia de todas ellas sea superior al 2-5% (el porcentaje exacto depende del número de observaciones del conjunto de datos).

Algunas de las funciones más útiles para este propósito son:

<mark style="color:green;">**`summary(dataFrame)`**</mark>: Ofrece un resumen de todas las variables presentes en el conjunto de datos. Para las variables numéricas, muestra valor mínimo, máximo, media, mediana y primer y tercer cuartil; mientras que para las variables de tipo factor, muestra los 6 niveles mayoritarios, junto con sus frecuencias. En ambos casos se muestra además el número de casos perdidos.&#x20;

<mark style="color:green;">**`psych::describe(Filter(is.numeric,dataFrame))`**</mark>: Esta función ofrece más medidas de las variables numéricas como la desviación estándar y el índice de simetría

<mark style="color:green;">**`skim(datos_agrupados,var1,var2):`**</mark> Esta función saca descriptivos de las variables que se le pasan según la variable de agrupación de datos\_agrupados <mark style="color:green;">**`(datos_agrupados <- group_by(dataFrame, var_agrupacion))`**</mark>&#x20;

<mark style="color:green;">**`table(varCuali), prop.table(table(varCuali)`**</mark>: Muestra la tabla de frecuencias de la variable cualitativa seleccionada.

<mark style="color:green;">**`hist(varCuant)`**</mark>: Muestra el histograma de la variable cuantitativa seleccionada.

<mark style="color:green;">**`barplot(table(varCuali))`**</mark>: Muestra el diagrama de barras de la variable cualitativa seleccionada.

<mark style="color:green;">**`boxplot(varCuant)`**</mark>: Muestra el diagrama de cajas de la variable cuantitativa seleccionada.

A partir de estas funciones es posible hacerse una idea muy clara de cómo son las variables del conjunto de datos y de sus problemas.&#x20;

## Tablas

Vamos a construir una base de datos sencilla para ver este apartado

<pre class="language-r"><code class="lang-r">#Definición de los datos (simulamos los datos)
<strong>set.seed(123)
</strong>datos &#x3C;- data.frame(
  ID = 1:20,
  genero = as.factor(c(rep("Masculino", 10), rep("Femenino", 9), "No binario")), # 10 hombres, 9 mujeres, 1 no binario
  edad = c(sample(18:80, 18, replace = TRUE), 150, -5), # Errores: edad negativa y edad demasiado alta
  estudios = as.factor(sample(c("Primaria", "Secundaria", "Universitaria", "?"), 20, replace = TRUE, prob = c(0.2, 0.3, 0.3, 0.05))),
  horas_ejercicio = c(rnorm(18, mean = 4, sd = 2), -2, 50) # Errores: valores negativos y extremadamente altos
)
</code></pre>

### Tablas de frecuencias unidemensionales

Construimos las tablas de frecuencias para la variable genero

<mark style="color:green;">**`table()`**</mark>: frecuencias absolutas

<mark style="color:green;">**`prop.table(table())`**</mark>: frecuencias relativas

<pre class="language-r"><code class="lang-r"><strong>table(datos$genero)
</strong># genero
#  Hombre      Mujer No binario 
#     9          10          1 
prop.table(table(datos$genero))
# genero
# Hombre      Mujer No binario 
#   0.45       0.50       0.05 
</code></pre>

Esto nos sirve para ver que hay una categoría muy pequeña y que muchas veces trabajar con categorías tan pequeñas es un problema. **A veces las juntaremos, otras veces las borraremos y otras veces seguiremos adelante.**&#x20;

### Tablas de frecuencias cruzadas

Las tablas cruzadas también se construyen usando <mark style="color:green;">**`table()`**</mark>y <mark style="color:green;">**`prop.table()`**</mark>especificando las dos variables a representar. En  <mark style="color:green;">**`prop.table()`**</mark> incluimos un 1 si las queremos por filas, un 2 si por columnas, o nada si queremos las frecuencias relativas globales

```r
t=table(datos$estudios,datos$genero)

##LA tabla con los totales
prop.table(t)

# La tabla con los totales por fila
prop.table(t,1)

# La tabla con los totales por clumna
prop.table(t,2)
```

```r
                 Femenino Masculino No binario
  ?                 0.05      0.10       0.00
  Primaria          0.15      0.10       0.00
  Secundaria        0.15      0.05       0.05
  Universitaria     0.10      0.25       0.00
               
                 Femenino Masculino No binario
  ?             0.3333333 0.6666667  0.0000000
  Primaria      0.6000000 0.4000000  0.0000000
  Secundaria    0.6000000 0.2000000  0.2000000
  Universitaria 0.2857143 0.7142857  0.0000000
               
                 Femenino Masculino No binario
  ?             0.1111111 0.2000000  0.0000000
  Primaria      0.3333333 0.2000000  0.0000000
  Secundaria    0.3333333 0.1000000  1.0000000
  Universitaria 0.2222222 0.5000000  0.0000000
```

### Tablas de frecuencias de una variable continua agrupada por intervalos

Una tabla de frecuencia para una variable cuantitativa no tiene sentido, veamos que pasa si representamos la edad con una tabla de frecuencia:

```r
table(datos$edad)
# -5  21  22  23  26  32  33  39  43  45  51  54  55  56  61  64  75 150 
#  1   1   1   1   1   1   2   2   1   1   1   1   1   1   1   1   1   1 
```

No nos está dando informacióm adicional ni resumiendo la variable. Para trabajar este tipo de variables, podemos construir intervalos. Normalmente pensaremos qué intervalos pueden tener sentido según el **objetivo del estudio**. En este caso podemos pensar simplemente en una variable dicotómica con los que son menores de 40 y los que son mayores de 40.&#x20;

En este caso lo podemos hacer de forma muy sencilla con el comando <mark style="color:green;">**`if_else`**</mark>:&#x20;

```r
datos$edad_cat<-if_else(datos$edad<40,1,2)
datos$edad_cat<-factor(datos$edad_cat, levels=c(1,2),   
                labels=c("<40",">=40"))
table(datos$edad_cat)
```

### Descriptivos

De la misma forma que podemos hacer tablas de frecuencias, podemos llevar a cabo un resumen descriptivo de las variables que contiene un **`data.frame`** con <mark style="color:green;">**`summary()`**</mark>

```r
summary(datos)
```

```r
       ID               genero        edad                 estudios horas_ejercicio  edad_cat 
 Min.   : 1.00   Femenino  : 9   Min.   : -5.00   Primaria     :4   Min.   :-2.000   <40 :11  
 1st Qu.: 5.75   Masculino :10   1st Qu.: 19.00   Secundaria   :7   1st Qu.: 1.623   >=40: 9  
 Median :10.50   No binario: 1   Median : 35.50   Universitaria:9   Median : 2.176            
 Mean   :10.50                   Mean   : 44.70                     Mean   : 4.726            
 3rd Qu.:15.25                   3rd Qu.: 63.25                     3rd Qu.: 3.154            
 Max.   :20.00                   Max.   :150.00                     Max.   :50.000           
```

En las dos variables cuantitativas vemos que hay número incorrecto, habrá que corregirlos. ¿Cómo podemos estar seguros de los número incorrectos? Con los [gráficos](./#graficos) que veremos a continuación

También podemos calcular media y desviación típica de la variable _edad_ por genero con la función <mark style="color:green;">**`aggregate()`**</mark>

<pre class="language-r"><code class="lang-r"><strong>aggregate(datos$edad,by=list(datos$genero),mean)
</strong></code></pre>

La librería **`psych`** contiene las funciones <mark style="color:green;">**`describe()`**</mark> y <mark style="color:green;">**`describeBy()`**</mark> que no sólo calculan la media sino que añaden otros estadísticos de interés

```r
#install.packages("psych")
library(psych)
describe(datos$edad)
describeBy(datos$edad,datos$genero)
```

La librería **`skimr`** contiene las funciones <mark style="color:green;">**`skim()`**</mark> y <mark style="color:green;">**`group_by()`**</mark> que no sólo calculan la media sino que añaden otros estadísticos de interés por una variable de agrupación

```r
#install.packages("skimr")
library(skimr)
datos_agrupados <- group_by(datos, genero)
skim(datos_agrupados,edad)
```

## Gráficos

Estos serían las  representaciones gráficas más sencillas para las variables que estamos viendo:

```r
barplot(table(datos$genero),col=rainbow(10))

library(ggplot2)
ggplot(datos, aes(x = estudios)) +
  geom_bar(aes(fill=estudios))
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```r
hist(datos$edad,col="skyblue")
hist(datos$horas_ejercicio,col="orange")

ggplot(datos, aes(x = edad)) +
  geom_histogram(fill = "steelblue")
```

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>



```r
boxplot(datos$edad~datos$genero,col=rainbow(10))

ggplot(datos, aes(x = genero, y = edad, fill = genero)) +
  geom_boxplot(color = "black") +
  labs(
    title = "Distribución de la Edad según Género",
    x = "Género",
    y = "Edad"
  ) +
  theme(legend.position = "none")  # Ocultar leyenda

```

<figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

Si recordamos lo que veíamos con el <mark style="color:green;">**`summary()`**</mark> y ahora con los gráficos, podemos corregir los errores que encontramos en las variables

```r
datos$estudios <-recode(datos$estudios, "?" = NA_character_)

datos$edad<-replace(datos$edad , datos$edad <0 | datos$edad>100,NA)
hist(datos$edad)

datos$horas_ejercicio<-replace(datos$horas_ejercicio , datos$horas_ejercicio <0 | datos$horas_ejercicio>40,NA)
hist(datos$horas_ejercicio)
```

Ahora es tu turno: Realiza los siguientes ejercicios:

{% content-ref url="ejercicio-1.3-graficos-y-tablas/" %}
[ejercicio-1.3-graficos-y-tablas](ejercicio-1.3-graficos-y-tablas/)
{% endcontent-ref %}

