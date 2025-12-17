# Ejercicio 4.2: Pruebas de homogeneidad de varianzas

El dataset <mark style="color:orange;">**`births`**</mark> del paquete <mark style="color:green;">**`openintro`**</mark> contiene información sobre 150 nacimientos junto con información de las madres. Se quiere determinar si existen evidencias significativas de que el peso de los recién nacidos cuyas madres fuman difiere de aquellos cuyas madres no fuman.

Estudia la variable <mark style="color:purple;">`weight`</mark> (peso de los recién nacidos) primero para ver si tiene outliers, después si es normal y si se cumple la homocedasticidad entre los grupos de las madres fumadoras y no fumadoras. Justifica la elección del test de homogeneidad de varianzas

```r
library(openintro)
data(births)
data<-births
```

&#x20;
