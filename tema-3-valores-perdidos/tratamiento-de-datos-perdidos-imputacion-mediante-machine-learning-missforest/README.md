---
cover: ../../.gitbook/assets/missing (1).jpeg
coverY: 0
---

# Tratamiento de datos perdidos (Imputación mediante Machine Learning MissForest)

## Imputación mediante algoritmo de Machine Learning

_**missForest**_ es un algoritmo de imputación basado en el algoritmo de Machine Learning Random Forest (package <mark style="color:green;">**`missForest`**</mark> en R) que maneja tanto variables cuantitativas como cualitativas sin transformaciones previas. Empieza imputando los datos perdidos con media/moda y luego, variable a variable, entrena un bosque con las filas observadas y predice las celdas faltantes. Repite este ciclo de forma iterativa hasta que la mejora entre iteraciones deja de ser relevante o se alcanza un número máximo de iteraciones. A partir de la segunda vuelta, los bosques se entrenan sobre datos progresivamente mejor imputados, lo que mejora la calidad de las predicciones para las variables restantes.

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

_missForest_ devuelve un **error OOB** (_out-of-bag_) qusando las muestras no usadas para entrenar cada árbol. Este error mide cómo de bien predicen los bosques sin necesidad de un conjunto externo de validación.

1. Para las **variables numéricas** se mide el **NRMSE** (_Normalized Root Mean Square Error)_

$$\mathrm{RMSE}=\sqrt{\frac{1}{|S|}\sum_{i\in S}\big(\hat y_i-y_i\big)^2}$$

donde S es el número de celdas evaluadas (aquellas con valor real conocido) y luego se normaliza dividiendo por la desviación estándar de los valores reales (para quedar sin unidades y ser comparable entre variables):

$$\mathrm{NRMSE}=\frac{\mathrm{RMSE}}{\,\mathrm{sd}(y)\,}$$.&#x20;

Interpretación: cuanto más pequeño es este valor mejor, siendo el 0 la imputación perfecta.&#x20;

2. Para las **variables categóricas** se midel el **PFC** (_Percentage Falsely Classified)_ que es la proporción mal clasificada:

$$
\mathrm{PFC}=\frac{1}{|S|}\sum_{i\in S}\mathbf{1}\!\left[\hat y_i \neq y_i\right]
$$

De nuevo, cuanto más bajo mejor, siendo 0 la clasificación perfecta.

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



Todas las imputaciones se asemejan a la distribución original de datos.

