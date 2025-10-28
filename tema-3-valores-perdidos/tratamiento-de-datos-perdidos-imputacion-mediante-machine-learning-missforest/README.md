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

Para evaluar la calidad de imputación se hace de dos formas:

1. **Métrica OOB (interna del algoritmo).**\
   &#xNAN;_&#x6D;issForest_ devuelve un **error OOB** (_out-of-bag_) que estima cómo de bien bien predicen los bosques:
   1. Variables numéricas: **NRMSE** (_Normalized RMSE_) que cuanto más pequeño mejor, siendo el 0 la imputación perfecta.&#x20;
   2. Variables categóricas: **PFC** (_Percentage Falsely Classified)_ que cuanto más pequeño mejor, siendo el 0 la imputación perfecta. \
      Si usas <mark style="color:green;">`variablewise = TRUE`</mark> obtienes el error por variable con NA (muy útil para detectar variables problemáticas).
2. **Validación externa.**\
   Se seleccionan al azar algunos valores conocidos, se imputa con _missForest_ y se comparan imputaciones vs. valores reales, esto habría que repetirlo un mínimo de 10 veces.&#x20;

Un ejemplo de como se aplicaría este algoritmo es el siguiente:

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

Como todas las variables son continuas, sólo nos muestra el error NRMSE&#x20;

Comparación por la imputación por la media:

```
data$temp_imp<-data$temp #Nueva variable
mean(data$temp_imp, na.rm = TRUE) #Media
# 38.55829
data$temp_imp[is.na(data$temp_imp)] <- mean(data$temp_imp, na.rm = TRUE) #Imputación

##Gráfico para representar las diferencias  
ggplot(data, aes(x = temp, fill = "temp")) +  
geom_density(alpha = 0.5) +  
geom_density(aes(x = temp_imp, fill = "temp_imp"), alpha = 0.5) 
```
