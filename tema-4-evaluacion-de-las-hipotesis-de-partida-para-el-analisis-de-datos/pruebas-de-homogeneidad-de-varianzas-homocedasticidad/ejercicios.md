# Ejercicios

El dataset <mark style="color:orange;">**`births`**</mark> del paquete openintro contiene información sobre 150 nacimientos junto con información de las madres. Se quiere determinar si existen evidencias significativas de que el peso de los recién nacidos cuyas madres fuman difiere de aquellos cuyas madres no fuman.

Estudia la variable <mark style="color:purple;">`weight`</mark> (peso de los recién nacidos) primero para ver si tiene outliers, después si es normal y si se cumple la homocedasticidad entre los grupos de las madres fumadoras y no fumadoras.

Después evalúa mediante un modelo de regresióm lineal simple si existe relación entre el peso de los recién nacidos y fumar. ¿Crees que es necesario hacer una transformación box-cox? ¿Si la haces cambia la relación entre las variables? Justifica la respuesta.&#x20;

```r
library(openintro)
data<-births
```

&#x20;
