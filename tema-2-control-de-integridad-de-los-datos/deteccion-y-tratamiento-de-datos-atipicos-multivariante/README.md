---
cover: >-
  ../../.gitbook/assets/Introducing-Automated-Time-Series-Anomaly-Detection_blog_Image_v.1.0.webp
coverY: 0
---

# Detección y tratamiento de datos atípicos (multivariante)

## Enfoque Multivariante

Un estudio multivariante es aquel que estudia más de dos variables de forma simultanea. Su objetivo principal es **capturar las relaciones internas** (correlaciones, dependencias, estructuras latentes) que no se aprecian en un enfoque univariado o bivariado.&#x20;

Al igual que tenemos métodos más sofisticados de _machine learning_ para analizar nuestros datos, los tenemos también para la detección de datos atípicos.&#x20;

Los outliers **no siempre destacan en la distribución global**. En un espacio multidimensional puede haber puntos perfectamente “normales” vistos variable a variable, pero **raros respecto a sus vecinos más próximos**. LOF (Local Outlier Factor) —propuesto por [Breunig et al., 2000](https://dl.acm.org/doi/10.1145/335191.335388)— aborda ese problema midiendo la _desviación local_.

### Local Outlier Factor (LOF)

LOF es un algoritmo de detección de valores atípicos basado en la proximida y la densidad. Evalúa la densidad local de los puntos de datos para identificar valores atípicos. A diferencia de algoritmos de agrupación como k-Nearest Neighbors (KNN), LOF se centra en encontrar observaciones que se desvíen del patrón de densidad local. Es decir, modela los valores atípicos como puntos que están aislados de los datos restantes.

En la práctica, la densidad local se obtiene de los k vecinos más cercanos. La puntuación LOF de una observación es igual a la relación entre la densidad local promedio de sus k vecinos más cercanos y su propia densidad local. Se espera que un _caso_ normal tenga una densidad local similar a la de sus vecinos, mientras que los _casos_ anómalos se espera que tengan una densidad local mucho menor.

En un **KNN**, lo que hacemos es clasificar a un nuevo miembro a una _clase_ o categoría que ya existe. Lo determina según que sus atributos se parezcan a los casos de una categoría u otra. Supongamos los grupos que se presentan en la siguiente gráfica. Se quiere asignar un nuevo punto, en negro. KNN buscará los _k_ puntos más cercanos a éste para encontrar la clase dominante del grupo. Para garantizar la existencia de una clase dominante, k debe ser un número impar.<br>

<figure><img src="../../.gitbook/assets/image (35).png" alt="" width="375"><figcaption></figcaption></figure>

En este ejemplo se utilizaron las cinco observaciones más cercanas ($$𝐾=5$$), para determinar a qué grupo asignar al nuevo _individuo_. Hay muchas maneras de _medir distancias_. Cada manera se adapta de mejor o peor manera al tipo de atributos con que cuentan los individuos o casos. Esto lo estudiaréis en asignaturas más adelante.&#x20;

**El algoritmo LOF** usa una idea similar al KNN, pero orientado a detectar datos anómalos. Calcula una puntuación (denominada factor de valor atípico local) que refleja el grado de anomalía de las observaciones. Mide la desviación de la _densidad local_ de un punto con respecto a sus vecinos. La idea es detectar las muestras que tienen una densidad sustancialmente menor que sus vecinas.

En la práctica, la densidad local se obtiene de los k vecinos más cercanos. La puntuación LOF de una observación es igual a la relación entre la densidad local promedio de sus k vecinos más cercanos y su propia densidad local. Se espera que un _caso_ normal tenga una densidad local similar a la de sus vecinos, mientras que los _casos_ anómalos se espera que tengan una densidad local mucho menor.

**Resumen del Algoritmo:** Aquí tenéis un esquema básico del algoritmo LOF:

* **Elegir&#x20;**_**k**_ (número de vecinos “cercanos”): $$k = log(n)$$
* Para cada punto de datos:
  * Calcula la distancia a sus k vecinos más cercanos.&#x20;
  * Evalúa la densidad local en función de la distancia promedio a los vecinos.
  * Compara la densidad local del punto con las densidades locales de sus vecinos.
  * Calcula el Local Outlier Factor (LOF) como la relación entre la densidad del punto y la densidad promedio de sus vecinos.

**Interpretación:**

* **LOF > 1:** Un valor LOF mayor que 1 indica que el punto tiene una densidad local significativamente menor que la de sus vecinos. En otras palabras, el punto se encuentra en una región donde la densidad de puntos es menor de lo esperado en comparación con su entorno local. Esto sugiere que el punto podría ser un valor atípico.
* **LOF ≈ 1:** Un valor LOF cercano a 1 indica que el punto tiene una densidad local similar a la de sus vecinos. En este caso, el punto se encuentra en una región donde la densidad de puntos es consistente con su entorno local. No se considera un valor atípico según LOF.
* **LOF < 1:** Un valor LOF menor que 1 indica que el punto tiene una densidad local más alta que la de sus vecinos. Esto puede suceder en regiones de alta densidad donde el punto está más densamente rodeado que la media de sus vecinos. En este caso, el punto puede considerarse menos atípico que sus vecinos.

La costumbre lleva a considerar anómalos a **valores de LOF por encima de** $$1.5$$ **ó** $$2$$**,** pero cada problema debe definir cuál es su umbral adecuado.

**Ejemplo con la base de datos de R iris**

El conjunto de datos Iris consta de 150 observaciones de iris, con 50 observaciones de cada una de las tres especies de iris: setosa, versicolor y virginica. Para cada observación, se miden cuatro características: longitud del sépalo, ancho del sépalo, longitud del pétalo y ancho del pétalo. Es conocido por su utilidad en la demostración de técnicas de clasificación y análisis estadístico.

```r
#Instala y carga los paquetes
install.packages(c("dbscan", "class"))

library(dbscan)
library(class)
library(ggplot2)

# Carga el conjunto de datos iris
data(iris)


# Añade un valor atípico al conjunto de datos
iris$Species<-as.character(iris$Species)
iris_atipico <- rbind(iris, c(7, 5, 5, 0.7, "Atipico"))
iris_atipico$Species<-as.factor(iris_atipico$Species)
iris_atipico[,c(1:4)]<-lapply(iris_atipico[,c(1:4)],as.numeric)
iris$Species<-as.factor(iris$Species)


# Gráfico de dispersión para mostrar el conjunto de datos con un valor atípico
ggplot(iris_atipico, aes(x = Sepal.Length, y = Sepal.Width, color = Species)) +
  geom_point() +
  geom_text(data = iris_atipico[nrow(iris_atipico),], aes(label = "Atipico"), vjust = -1) +
  labs(title = "Conjunto de Datos Iris con un Valor Atípico",    
       x = "Longitud del Sépalo", y = "Anchura del Sépalo")

ggplot(iris_atipico, aes(x = Petal.Length, y = Petal.Width, color = Species)) +
  geom_point() +
  geom_text(data = iris_atipico[nrow(iris_atipico),], aes(label = "Atipico"), vjust = -1) +
  labs(title = "Conjunto de Datos Iris con un Valor Atípico",
       x = "Longitud del Pétalo", y = "Anchura del Pétalo")

```

<figure><img src="../../.gitbook/assets/image (209).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (279).png" alt=""><figcaption></figcaption></figure>

```r
# Aplica LOF para detección de valores atípicos
k<-round(log(nrow(iris_atipico)))
lof_resultados <- lof(iris_atipico[, 1:4],minPts = k)

iris_atipico$lof<-lof_resultados
ggplot(iris_atipico, aes(y = lof)) +
  geom_boxplot(fill = "skyblue", outlier.color = "red", outlier.shape = 16) +
  theme_minimal() +
  labs(title = "Distribución de LOF Scores")
  
### Los que tienen un LOF mayor de 1.5
cbind(iris_atipico[which(iris_atipico$lof>1.5),])

# Gráfico de dispersión con colores según las puntuaciones LOF
ggplot(iris_atipico, aes(x = Sepal.Length, y = Sepal.Width, colour = lof_resultados)) +
  geom_point() +
  scale_color_gradient(low = "blue", high = "red", name = "LOF Score") +
  labs(title = "Detección de Valores Atípicos con LOF",
       x = "Longitud del Sépalo", y = "Anchura del Sépalo")
       
ggplot(iris_atipico, aes(x = Petal.Length, y = Petal.Width, colour = lof_resultados)) +
  geom_point() +
  scale_color_gradient(low = "blue", high = "red", name = "LOF Score") +
  labs(title = "Detección de Valores Atípicos con LOF",
       x = "Longitud del Pétalo", y = "Anchura del Pétalo")

```

```r
Sepal.Length Sepal.Width Petal.Length Petal.Width    Species      lof
21           5.4         3.4          1.7         0.2     setosa 1.590261
23           4.6         3.6          1.0         0.2     setosa 2.107731
24           5.1         3.3          1.7         0.5     setosa 1.510867
32           5.4         3.4          1.5         0.4     setosa 1.529246
42           4.5         2.3          1.3         0.3     setosa 2.406485
63           6.0         2.2          4.0         1.0 versicolor 1.717688
107          4.9         2.5          4.5         1.7  virginica 1.992299
110          7.2         3.6          6.1         2.5  virginica 1.840244
151          7.0         5.0          5.0         0.7    Atipico 5.176055
```

<figure><img src="../../.gitbook/assets/image (210).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (280).png" alt=""><figcaption></figcaption></figure>

En el gráfico vemos claramente el valor atípico con un lof muy alto y en la tabla, donde hemos pintado todos los datos que están por encima de lof score > 1.5 vemos como el dato atípico tiene un claro valor que se va del reso con un lof score = 5.17. En este caso no tenemos que borrar todos los valores que estén por encima de 1.5 sino ser capaces de identificar que ese valor dista mucho del resto tanto en valor como en el gráfico. Además en este caso vemos que los valores son raros para casi todas las variables, por tanto podemos sospechar que lo que deberíamos borrar es la observación entera.&#x20;
