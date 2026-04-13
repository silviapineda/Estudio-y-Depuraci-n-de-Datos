---
cover: ../../.gitbook/assets/missing (1).jpeg
coverY: 0
---

# Tratamiento de datos perdidos (Imputación mediante Machine Learning MissForest)

## Imputación mediante algoritmo de Machine Learning

_**missForest**_ es un algoritmo de imputación basado en el algoritmo de Machine Learning Random Forest (package <mark style="color:green;">**`missForest`**</mark> en R) que maneja tanto variables cuantitativas como cualitativas sin transformaciones previas. Empieza imputando los datos perdidos con media/moda y luego, variable a variable, entrena un bosque de árboles con las filas observadas y predice las celdas faltantes. Repite este ciclo de forma iterativa hasta que la mejora entre iteraciones deja de ser relevante o se alcanza un número máximo de iteraciones. A partir de la segunda vuelta, los bosques se entrenan sobre datos progresivamente mejor imputados, lo que mejora la calidad de las predicciones para las variables restantes.

Para entender cómo funciona, primero hay que entender la idea básica que hay detrás. Un **árbol de decisión** es un modelo que aprende a predecir una variable haciendo preguntas sucesivas sobre las demás. Funciona como un juego de "preguntas (variables)": por ejemplo, para predecir si un alumno aprobará, el árbol podría preguntar primero "¿ha asistido a más del 80% de las clases?", luego "¿su nota en el parcial fue mayor que 5?", y así sucesivamente. Cada pregunta divide los datos en dos grupos, y al final de la cadena de preguntas se llega a una predicción.&#x20;

<figure><img src="../../.gitbook/assets/image (309).png" alt=""><figcaption></figcaption></figure>

Un **Random Forest** (bosque aleatorio) simplemente consiste en entrenar muchos árboles de decisión, cada uno con una parte aleatoria de los datos y de las variables, y combinar sus predicciones. Al promediar muchos árboles se obtiene una predicción mucho más robusta y estable que la de un solo árbol.

Algunas **ventajas** de este algoritmo son:

* Se puede aplicar tanto a variables cuantitativas como cualitativas.&#x20;
* No necesita prepocesado de los datos.
* Es robusto al ruido ya que atenúa predictores débiles/irrelevantes.
* No asume linealidad y port tanto capta la no linealidad e interacciones entre variables.
* Escala a alta dimensionalidad (p≫n) algo que pasa mucho en ciencia de datos.

Algunas **desventajas** son:&#x20;

* Tiempo computacional es elevado y crece con el número de observaciones, predictores y porcentaje de celdas con datos perdidos.&#x20;
* Tiene poca interpretabilidad ya que hereda la “caja negra” del Random Forest.
* No genera un “modelo” final reutilizable y por tanto hay que re-ejecutar el algoritmo cada vez que se necesite imputar, lo que puede ser un problema.

### **Evaluación mediante métrica OOB (interna del algoritmo)**

Para saber si la imputación es buena, necesitamos alguna forma de medir el error. El problema es que no conocemos los valores reales que faltan (si los conociéramos, no necesitaríamos imputar). missForest resuelve esto con un truco que hereda de Random Forest: el **error OOB (out-of-bag)**.

Cuando Random Forest construye cada árbol, no usa todas las filas de datos sino que toma una muestra aleatoria. Las filas que quedan fuera (las "out-of-bag") no participaron en el entrenamiento de ese árbol, así que se pueden usar como un pequeño conjunto de test. Cada fila acaba quedando fuera de aproximadamente un tercio de los árboles, y se usa la predicción promedio de esos árboles para estimar el error. Así obtenemos una medida de calidad sin necesidad de reservar datos aparte para validación.

Este error se calcula de forma diferente según el tipo de variable:

1. **Variables numéricas: NRMSE (Normalized Root Mean Square Error)**

Se calcula primero el RMSE, que es la raíz del error cuadrático medio:

$$\mathrm{RMSE}=\sqrt{\frac{1}{|S|}\sum_{i\in S}\big(\hat y_i-y_i\big)^2}$$

donde _S_ es el conjunto de celdas evaluadas (aquellas con valor real conocido), $$\hat y_i$$ es el valor predicho e $$y_i$$ el valor real. En términos simples: para cada celda, calcula cuánto se ha equivocado la predicción, eleva esos errores al cuadrado (para que no se cancelen positivos con negativos), calcula la media, y finalmente toma la raíz cuadrada para volver a las unidades originales.

El problema del RMSE es que depende de la escala: un error de 5 es mucho si hablamos de temperatura en grados pero insignificante si hablamos de radiación solar. Para poder comparar entre variables, se normaliza dividiendo por la desviación estándar de los valores observados:

$$\mathrm{NRMSE}=\frac{\mathrm{RMSE}}{\,\mathrm{sd}(y)\,}$$.&#x20;

Así el NRMSE queda sin unidades y es comparable entre variables. **Un valor cercano a 0 indica una imputación muy buena**. Un valor cercano a 1 significa que el error de imputación es del mismo orden que la variabilidad de los datos, es decir, el modelo no aporta mucho más que imputar con la media. Valores superiores a 1 indicarían que la imputación es peor que la media.



2. **Variables categóricas: PFC (Proportion of Falsely Classified):**

Para las variables categóricas no tiene sentido medir un error numérico, así que simplemente se cuenta qué proporción de celdas se ha clasificado incorrectamente:

$$
\mathrm{PFC}=\frac{1}{|S|}\sum_{i\in S}\mathbf{1}\!\left[\hat y_i \neq y_i\right]
$$

donde $$\mathbf{1}[\hat y_i \neq y_i]$$ vale 1 si la predicción es incorrecta y 0 si es correcta. El resultado es una proporción entre 0 y 1: un PFC de 0 significa clasificación perfecta, y un PFC de 0.3, por ejemplo, significa que el 30% de los valores se han clasificado mal.

\
Si usas <mark style="color:green;">`variablewise = TRUE`</mark> obtienes el error por cada variable con NA (muy útil para detectar variables problemáticas).&#x20;

### Ejemplo

Vamos a utilizar los datos de airquality de R que son datos de mediciones de la calidad del aire medidos en EEUU que hemos visto en la imputación con MICE:&#x20;

{% file src="../../.gitbook/assets/airquality.csv" %}

Se ejecuta el algoritmo de forma muy sencilla con la función <mark style="color:green;">`missForest()`</mark>

```r
data<-read.csv("airquality.csv")
str(data)

#install.packages("missForest")
library(missForest)

set.seed(123)
imp <- missForest(data)
```

Nos da por un lado la base de datos imputada y por otro lado los errores OOB:

```r
head(imp$ximp)
     Ozone  Solar.R      Wind     Temp
1 41.00000 190.0000  7.400000 81.10023
2 36.00000 118.0000  8.000000 80.30992
3 12.00000 149.0000 12.600000 71.89835
4 18.00000 313.0000 12.638919 66.89062
5 40.14696 211.4715  9.496077 81.45118
6 28.00000 216.2286 10.360952 66.00000

imp$OOBerror 
  NRMSE 
0.5280747 
```

Como todas las variables son continuas, sólo nos muestra el error NRMSE, para poder ver los errores propios de cada variable hay que usar la opción <mark style="color:green;">`variablewise = TRUE`</mark> <mark style="color:green;"></mark><mark style="color:green;">.</mark> Esta opción nos da simplemente el MSE, habrá que dividirla por la desviación típica para hacerla comparable entre variables&#x20;

```r
imp <- missForest(data,variablewise = TRUE)
imp$OOBerror 
        MSE         MSE         MSE         MSE 
 311.221941 7083.740928    8.251267   39.682168 
# Calcular la Standard Deviation para normalizar
SD<-sapply(data,sd,na.rm=TRUE)

# Calcular el NMRSE
NRMSE<-sqrt(imp$OOBerror)/SD
names(NRMSE)<-colnames(data)
NRMSE
    Ozone   Solar.R      Wind      Temp 
0.5347867 0.9345596 0.8349439 0.6778429 
```

En este caso, podemos ver el error individual por variable siendo la variable Ozone la que menor valor tiene y la de Solar.R la que peor.&#x20;

Comparando gráficamente:

```r
g1<-ggplot(data, aes(x = data$Ozone, fill = "Ozone")) +
  geom_density(alpha = 0.5) +
  geom_density(aes(x = imp$ximp$Ozone, fill = "Ozone_imp"), alpha = 0.5) 

g2<-ggplot(data, aes(x = data$Solar.R, fill = "Solar.R")) +
  geom_density(alpha = 0.5) +
  geom_density(aes(x = imp$ximp$Solar.R, fill = "Solar.R_imp"), alpha = 0.5) 

g3<-ggplot(data, aes(x = data$Wind, fill = "Wind")) +
  geom_density(alpha = 0.5) +
  geom_density(aes(x = imp$ximp$Wind, fill = "Wind_imp"), alpha = 0.5) 

g4<-ggplot(data, aes(x = data$Temp, fill = "Temp")) +
  geom_density(alpha = 0.5) +
  geom_density(aes(x = imp$ximp$Temp, fill = "Temp_imp"), alpha = 0.5) 

library(patchwork)
g1+g2+g3+g4
```

<figure><img src="../../.gitbook/assets/image (303).png" alt=""><figcaption></figcaption></figure>



Todas las imputaciones se asemejan a la distribución original de datos, pero los errores aunque menores de 1, no son del todo buenos.&#x20;

