---
cover: ../../.gitbook/assets/missing (1).jpeg
coverY: 0
---

# Tratamiento de datos perdidos (Imputación Múltiple con MICE)

Imputación múltiple:

En R podemos usar distintos paquetes muy útiles para ello: <mark style="color:green;">**`Mmisc`**</mark>, <mark style="color:green;">**`missForest`**</mark> (usa Random Forests para imputar datos missing), and <mark style="color:green;">**`mice`**</mark> (Multiple Imputation by Chained Equations). Uno de los paquetes más usados es <mark style="color:green;">**`mice`**</mark>.&#x20;

### Multiple Imputation by Chained Equations (Paquete <mark style="color:green;">`mice`</mark>):

El paquete <mark style="color:green;">**`mice`**</mark> en R te ayuda a imputar valores faltantes con valores de datos plausibles. Estos valores plausibles se extraen de una distribución diseñada específicamente para cada punto de datos faltante.

La **imputación múltiple** es un método que aborda el problema de los valores faltantes en los conjuntos de datos al generar múltiples conjuntos de datos completos, cada uno con valores imputados de manera diferente. Luego, los resultados se combinan para obtener estimaciones finales y varianzas que reflejen la incertidumbre de la imputación.

<figure><img src="../../.gitbook/assets/image (213).png" alt=""><figcaption></figcaption></figure>

### Principio detrás de MICE:

1. **Ecuaciones encadenadas**: El método MICE se basa en ecuaciones encadenadas, lo que significa que los valores faltantes de cada variable se imputan secuencialmente utilizando modelos predictivos basados en las demás variables en el conjunto de datos.
2. **Iterativo**: El proceso de imputación es iterativo, lo que significa que se repite varias veces hasta que se alcanza una convergencia. En cada iteración, se imputan los valores faltantes de una variable a la vez, utilizando los valores imputados de las otras variables en la iteración anterior.
3. **Modelos Predictivos**: Para imputar los valores faltantes, se utilizan modelos predictivos adecuados para cada tipo de variable. Por ejemplo, para variables numéricas, se pueden utilizar modelos de regresión lineal, mientras que para variables categóricas se pueden utilizar modelos de regresión logística.

En este algoritmo, de forma iterativa, se harán progresivamente cada vez mejores estimaciones de los datos faltantes. Inicialmente la primera estimación no es muy buena, pues se hace con la imputación por la media que vimos anteriormente, pero en los pasos restantes se aplica una regresión lineal entre pares consecutivos de columnas, y el procedimiento se repite una y otra vez hasta completar un número pre-definido de iteraciones. La idea es que progresivamente las estimaciones sean cada vez más precisas y se acerquen más y más al valor real

{% tabs %}
{% tab title="Iteración 1" %}
<figure><img src="../../.gitbook/assets/image (215).png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Iteración 2" %}
<figure><img src="../../.gitbook/assets/image (217).png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Iteración 3" %}
<figure><img src="../../.gitbook/assets/image (218).png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Iteración 4" %}
<figure><img src="../../.gitbook/assets/image (219).png" alt=""><figcaption></figcaption></figure>
{% endtab %}
{% endtabs %}

La función <mark style="color:green;">**`mice()`**</mark> se encarga del proceso de imputación de forma que considera todas las variables de forma múltiple y genera varios datasets imputadas de las que luego podremos hacer una media

Los parámetros más importantes a tener en cuenta son:

_**m**_ que representa el número de datasets que son imputados. Por defecto es 5.

_**maxit**_ el número máximo de iteraciones que se van a realizar.

_**seed**_ que marca la semilla con la que se va a iniciar la imputación.

_**method**_ que indica el método de imputación.

La diferencia entre el número de conjuntos de datos imputados y el número de iteraciones en el algoritmo MICE es:

1. **Número de conjuntos de datos imputados (Multiple Imputations)**: En MICE, se crean múltiples conjuntos de datos imputados. La idea detrás de esto es capturar la incertidumbre asociada con la imputación de valores perdidos. Cada conjunto de datos imputado refleja una posible imputación de los valores perdidos. Este número de conjuntos de datos imputados es una decisión que el usuario debe tomar antes de ejecutar el algoritmo. Por lo general, se generan varios conjuntos (a menudo 5 a 10), lo que permite obtener estimaciones más robustas y tener en cuenta la variabilidad en las imputaciones.&#x20;
2. **Número de iteraciones**: Las iteraciones se refieren a la cantidad de veces que se repite el proceso de imputación dentro de cada conjunto de datos. En cada iteración, se imputan los valores perdidos utilizando modelos estadísticos, y luego se actualizan y recalculan los valores imputados para las siguientes iteraciones. El número de iteraciones se elige para garantizar que el algoritmo converja a una solución estable. Es importante ejecutar suficientes iteraciones para permitir que el proceso de imputación se estabilice y produzca estimaciones confiables.

### Los métodos más comunes son:

**pmm** (**Predictive Mean Matching):** Este método se utiliza para variables numéricas. Imputa los valores faltantes basándose en la distribución predictiva de la variable condicional a otras variables observadas.

**logreg (Logistic Regression):** Este método se utiliza para imputar variables categóricas. Utiliza regresión logística para predecir la categoría de una variable categórica faltante.

**cart (Classification and Regression Trees):** Este método utiliza árboles de clasificación y regresión para imputar valores faltantes.

Puedes adaptar los métodos según las características específicas de tus datos y el tipo de variables que estás imputando. Además, puedes combinar métodos para diferentes variables utilizando el argumento _**method**_ de manera apropiada.

**Ten en cuenta que todos los métodos combina la predicción con el muestreo aleatorio para generar bases de datos distintas y así poder medir una incertidumbre.**&#x20;

### **Ejemplo de la aleatoriedad con PMM:**

1. **Ajuste de un modelo de regresión**
   * Se toma la variable con valores faltantes Y y se ajusta una regresión usando las observaciones completas en función de otras variables predictoras.
   * Esto da un conjunto de coeficientes de regresión  $$\hat{\beta}$$.
   * MICE usa **bootstrap** en cada iteración para generar variabilidad en los datos de entrenamiento.&#x20;
   * En cada imputación se obtiene un modelo de regresión **ligeramente distinto**, lo que cambia las predicciones y, por lo tanto, las imputaciones.
2. **Predicción de valores faltantes**
   * Se usa el modelo ajustado para obtener una **predicción** de los valores faltantes de Y.
   * Estas predicciones no se usan directamente para la imputación (como en la regresión clásica), sino como referencia para encontrar valores similares en los datos observados.
3. **Búsqueda de vecinos más cercanos**
   * Para cada observación con un valor faltante, se buscan **las k observaciones más cercanas** (según la predicción de la regresión) en los datos completos.
   * Este número k puede ajustarse (por defecto, PMM usa k=5).
4. **Selección aleatoria del vecino**
   * Se elige **aleatoriamente** uno de esos vecinos más cercanos y se usa su valor real observado como imputación.
   * Este paso introduce **aleatoriedad** en la imputación y evita que los valores imputados sean siempre los mismos.

### Ejemplo

Vamos a utilizar los datos de airquality de R que son datos de mediciones de la calidad del aire medidos en EEUU.

{% file src="../../.gitbook/assets/airquality.csv" %}

```r
data<-read.csv("airquality.csv")
str(data)
```

1. **Visualización y Cuantificación:**

```r
##Vamos a visualizar y cuantificar los datos missing
library(naniar)
vis_miss(data)
```

<figure><img src="../../.gitbook/assets/image (207).png" alt="" width="563"><figcaption></figcaption></figure>

Observamos que la varianble <mark style="color:purple;">`ozone`</mark> tiene casi el 25% de los puntos de datos faltantes, por lo tanto, podríamos considerar excluirla del análisis o recopilar más medidas. Las otras variables están por debajo del umbral del 5%, por lo que podemos conservarlas.&#x20;

2. **Tipos de datos perdidos**

```r
ggplot(data = data, aes (x = Ozone, y = Solar.R)) + geom_miss_point()
ggplot(data = data, aes (x = Ozone, y = Wind)) + geom_miss_point()
ggplot(data = data, aes (x = Ozone, y = Temp)) + geom_miss_point()
```

{% tabs %}
{% tab title="Solar.R" %}
<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Wind" %}
<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Temp" %}
<figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>
{% endtab %}
{% endtabs %}

Podríamos decir según estos gráficos que los datos faltantes de Ozone y Solar.R son MCAR ya que no hay ninguna relación con las demás variables, pero los datos de Wind y Temp corresponden a datos que tienen valores bajos de Ozone pero no son todos los valores bajos de Ozone, por tanto serían MNAR ya que parece que hay una relación no observada, pero poco pronunciada.

3. **Patrón de los datos faltantes. ¿Hay alguna observación con todo datos faltantes?**

El propio paquete **`mice`** proporciona una función útil llamada <mark style="color:green;">**`md.pattern()`**</mark> para ver mejor el patrón de datos faltantes.

```renpy
##install.packages("mice")
library(mice)
md.pattern(data)
```

<figure><img src="../../.gitbook/assets/image (209).png" alt=""><figcaption></figcaption></figure>

Vemos que hay una observación para el que todas las variables son faltantes y por tanto esta observación habría que borrarla.

```r
data <- data[rowSums(is.na(data)) < ncol(data), ]

library(dplyr)
data <- data %>% filter(!if_all(everything(), is.na))
```

4. **Imputación usando el método pmm**

```r
impData <- mice(data,m=5,maxit=50,meth='pmm',seed=500)
summary(impData)
```

Para visualizar la imputación de las variables en las 5 datasets imputados hacemos lo siguiente:

```r
impData$imp$Ozone
impData$imp$Solar.R
impData$imp$Wind
impData$imp$Temp
```

Y para visualizar la base de datos complete hay que ejecutar la función <mark style="color:green;">**`complete()`**</mark>:

```r

completedData <- complete(impData,1)
```

Los valores faltantes han sido reemplazados con los valores imputados en el primer conjunto de los cinco datasets. Si deseas utilizar otro, simplemente cambia el segundo parámetro en la función<mark style="color:green;">**`complete()`**</mark>

Para la inspección de los datos vamos a utilizar varios gráficos que nos comparan la distribución de los datos originales con los imputados:

Primero, podemos utilizar un diagrama de dispersión (scatterplot) y representar la variable ozone frente a todas las demás variables.

```r
xyplot(impData, Ozone ~ Wind+Temp+Solar.R,pch=18,cex=1)
```

<figure><img src="../../.gitbook/assets/image (210).png" alt=""><figcaption></figcaption></figure>

Lo que nos gustaría ver es que la forma de los puntos magenta (imputados) coincida con la forma de los azules (observados). La coincidencia de formas nos indica que los valores imputados son efectivamente "valores plausibles".

Otro gráfico útil es el gráfico de densidad

```r
densityplot(impData)
```

<figure><img src="../../.gitbook/assets/image (175).png" alt=""><figcaption></figcaption></figure>

La densidad de los datos imputados para cada conjunto de datos imputados se muestra en magenta, mientras que la densidad de los datos observados se muestra en azul. Nuevamente, bajo nuestras suposiciones anteriores, esperamos que las distribuciones sean similares.

Otra forma visual útil de observar las distribuciones se puede obtener utilizando la función <mark style="color:green;">**`stripplot()`**</mark>, que muestra las distribuciones de las variables como puntos individuales.

```r
stripplot(impData, pch = 20, cex = 1.2)
```

<figure><img src="../../.gitbook/assets/image (176).png" alt=""><figcaption></figcaption></figure>

5. **Ajustar un modelo de regresión lineal**

Supongamos que el siguiente paso en nuestro análisis es ajustar un modelo lineal a los datos. Puede surgir la pregunta de qué conjunto de datos imputados elegir. El paquete <mark style="color:green;">**`mice`**</mark> facilita ajustar un modelo a cada uno de los conjuntos de datos imputados y luego combinar los resultados.

```r
###Ajustamos un modelo y luego hacemos un pool de los resultados
modelFit1 <- with(impData,lm(Temp~ Ozone+Solar.R+Wind))
modelFit1
summary(modelFit1)

pool(modelFit1)
summary(pool(modelFit1))

```

La variable _**modelFit1**_ contiene los resultados del ajuste realizado sobre los conjuntos de datos imputados, mientras que la función <mark style="color:green;">**`pool()`**</mark> los combina todos juntos.

```r
pool(modelFit1)
Class: mipo    m = 5 
         term m    estimate         ubar            b            t dfcom        df       riv    lambda       fmi
1 (Intercept) 5 72.55796363 6.595501e+00 0.5750036463 7.285505e+00   148 101.97555 0.1046174 0.0947092 0.1119569
2       Ozone 5  0.16081480 4.935193e-04 0.0001610921 6.868298e-04   148  34.09063 0.3916979 0.2814533 0.3201987
3     Solar.R 5  0.01274085 4.324896e-05 0.0000122131 5.790469e-05   148  39.70957 0.3388688 0.2531008 0.2880765
4        Wind 5 -0.33902052 3.904917e-02 0.0045108400 4.446218e-02   148  86.94099 0.1386203 0.1217441 0.1412737
```

```r
summary(pool(modelFit1))
         term    estimate   std.error statistic        df      p.value
1 (Intercept) 72.55796363 2.699167503 26.881608 101.97555 4.373091e-48
2       Ozone  0.16081480 0.026207437  6.136228  34.09063 5.674375e-07
3     Solar.R  0.01274085 0.007609513  1.674331  39.70957 1.019253e-01
4        Wind -0.33902052 0.210860560 -1.607795  86.94099 1.115059e-01
```

Para evaluar la calidad del ajuste con las imputaciones, se puede usar el valor <mark style="color:orange;">**`fmi`**</mark> que proporciona la **fracción de información perdida debida a los valores faltantes** y el <mark style="color:orange;">**`lambda`**</mark> que es la **proporción de la varianza total atribuible a los datos faltantes**.  Aparentemente, solo la variable <mark style="color:purple;">`Ozone`</mark> es estadísticamente significativa.

Recuerda que inicializamos la función <mark style="color:green;">`mice()`</mark> con una semilla específica, por lo tanto, los resultados dependen en cierta medida de nuestra elección inicial. Para reducir este efecto, podemos imputar un mayor número de conjuntos de datos, cambiando el parámetro m (por defecto, **m=5**) en la función <mark style="color:green;">**`mice()`**</mark> de la siguiente manera:

```r
impData2 <- mice(data,m=50,seed=245435)
modelFit2 <- with(impData2,lm(Temp~ Ozone+Solar.R+Wind))
pool(modelFit2)

Class: mipo    m = 50 
         term  m   estimate         ubar            b            t dfcom       df       riv    lambda       fmi
1 (Intercept) 50 72.6409950 6.805400e+00 1.082784e+00 7.909840e+00   148 119.6660 0.1622887 0.1396286 0.1536564
2       Ozone 50  0.1636463 5.224431e-04 1.390522e-04 6.642763e-04   148 103.7690 0.2714807 0.2135154 0.2282479
3     Solar.R 50  0.0115144 4.461246e-05 7.239493e-06 5.199674e-05   148 119.1549 0.1655207 0.1420143 0.1560619
4        Wind 50 -0.3357630 4.024084e-02 6.924729e-03 4.730407e-02   148 117.5870 0.1755237 0.1493153 0.1634244

summary(pool(modelFit2))

        term   estimate   std.error statistic       df      p.value
1 (Intercept) 72.6409950 2.812443743 25.828426 119.6660 9.143086e-51
2       Ozone  0.1636463 0.025773558  6.349388 103.7690 5.800127e-09
3     Solar.R  0.0115144 0.007210876  1.596810 119.1549 1.129568e-01
4        Wind -0.3357630 0.217494982 -1.543774 117.5870 1.253302e-01
```

6. **Calcular el** $$R^2$$

Aunque la estimación exacta del $$R^2$$ no se puede calcular como parte de la función combinada, existe una opción para calcular un pseudo $$R^2$$ basado en la transformación de Fisher-z para correlaciones y que ha sido estudiada en el contexto de múltiples imputaciones por Harel (2009) que se puede calcular con la función <mark style="color:green;">**`pool.r.squared()`**</mark>

```r
pool.r.squared(pool(modelFit1))
```
