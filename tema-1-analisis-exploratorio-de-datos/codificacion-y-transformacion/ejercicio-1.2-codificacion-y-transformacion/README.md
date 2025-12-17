# Ejercicio 1.2: Codificación y Transformación

En la base de datos <mark style="color:orange;">**estres.csv**</mark> se incluye información sobre los niveles de estrés de un grupo de trabajadores y trabajadoras, así como otras variables de interés.

{% file src="../../../.gitbook/assets/estres.csv" %}

Lo primero, genera un documento quarto:

<figure><img src="../../../.gitbook/assets/image (250).png" alt="" width="375"><figcaption></figcaption></figure>

Recuerda ponerle bien la cabecera

Después lee el fichero&#x20;

```r
datos<-read.csv("estres.csv", header = T, sep = "\t")
```

Realiza las siguientes actividades en R:

1. Clasifica las variables en cuantitativas y cualitativas
2. Examina la estructura de los datos (_<mark style="color:green;">**`str`**</mark>_) ¿Cómo son los tipos de variables? ¿Cambiarías alguna?
3. ¿Hay algún `NA`? Utiliza <mark style="color:green;">**`any(is.na(datos))`**</mark>
4. ¿Qué ID tiene la persona con mejor sueldo? puedes usar  <mark style="color:green;">**`which()`**</mark>
5. ¿Cuántos trabajadores tienen un nivel de estrés mayor de 5? Utiliza <mark style="color:green;">**`length()`**</mark>
6. ¿Cuántos trabajadores tienen estrés mayor de 5 y además una carga física > 3?
7. Nos hemos equivocado en la introducción de datos y para todos los valores _2_ de carga física, queríamos haber puesto un _3_. _Recodifica_ esta variable incluyendo este cambio. Recuerda recodificar en una nueva variable. Utiliza <mark style="color:green;">**`if_else()`**</mark>del paquete **`dplyr`**
8. Crea una nueva variable llamada _expo\_total_ que se obtenga como suma de los valores de exposición en _carga física_, _carga psíquica_ y _ruido_.
9. _Categoriza_ la variable _estres_ en otra nueva variable que llamaremos _estres\_grupo_ que incluya tres categorías: _bajo_: 1 a 2; _medio_: 3 a 7; _alto_; 8 a 10. ¿Cuántas personas hay en cada categoría de la nueva variable. Utiliza <mark style="color:green;">**`cut()`**</mark> y <mark style="color:green;">**`breaks()`**</mark>
10. Guarda la base de datos corregida. Usa <mark style="color:green;">**`write.csv()`**</mark> y llamala <mark style="color:orange;">**estres\_corregido.csv**</mark>
