---
cover: ../../.gitbook/assets/image (112).png
coverY: 0
---

# Tipos de variables

## Crear un proyecto en R

Antes de nada vamos a crear un proyecto en R donde iremos guardando todos los scripts y todas las bases de datos. Para ello ve a <mark style="color:blue;">File > New Project</mark> e introduce el nombre del proyecto "<mark style="color:blue;">Depuración</mark>" y la URL donde lo quieres guardar:

![](<../../.gitbook/assets/image (237).png>)

A partir de ahora siempre en el inicio de clase abrirás ese proyecto y guardarás todos los ficheros en esa carpeta.

Puedes crear una carpeta por cada  tema para tener todo más ordenado. Por ejemplo <mark style="color:blue;">Tema\_1, Tema\_2, Tema\_3, Tema\_4</mark>

## Tipos de variables

Las variables que pueden aparecer en un conjunto de datos pueden ser de varios tipos:&#x20;

* **V. Cuantitativas**: Son aquellas que representan cantidades numéricas
  * **Continuas**: peso, altura, salario
  * **Discretas**: edad, número de hijos
* **V. Cualitativas**: Son aquellas que toman un número finito de valores o categorías
  * **Ordinales**: se pueden ordenar y asignar un valor: nivel de eduación, niveles de dolor, grado de satisfacción
  * **Nominales**: No se puede establecer ningún orden: sexo, raza, país de nacimiento
* **V. Fecha**: se consideran variables cuantitativas, pero cada software suele tener un tratamiento específico

<details>

<summary>Ejercicio</summary>

Escribe un ejemplo de cada tipo de variable

</details>

## Tipos de datos R

En R los datos se guardan en _**objetos**_ . Los _objetos_ tienen nombre y contenido, pero también atributos que especifican el tipo de datos representados.&#x20;

<figure><img src="../../.gitbook/assets/image (108).png" alt="" width="510"><figcaption></figcaption></figure>



| Objeto     | Tipos                                           | Varios tipos en el mismo |
| ---------- | ----------------------------------------------- | ------------------------ |
| Vector     | Numérico, carácter o lógico                     | No                       |
| Factor     | Numérico o carácter                             | No                       |
| Matriz     | Numérico, carácter o lógico                     | No                       |
| Data Frame | Numérico, carácter o lógico                     | Si                       |
| Lista      | Numérico, carácter, lógico, función o expresión | Si                       |

Para saber los tipos de datos en R tenemos la función <mark style="color:green;">**`class()`**</mark>

## Vectores

En R hay dos clases fundamentales de datos: **numéricos** y **caracteres**:&#x20;

```r
x=1
class(x)
## [1] "numeric" 

y="a"
class(y)
## [1] "character
```

Podemos agrupar varios elementos en una misma clase usando **`vectores`**

```r
## Vectores de clase numérico
edad = c(22, 34, 29, 25, 30, 33, 31, 27, 25, 25)
edad
## [1] 22 34 29 25 30 33 31 27 25 25

## Vectores de clase caracter
letras=c("a","b","c","d","e")
letras
## [1] "a" "b" "c" "d" "e"
```

Para saber su longitud usamos la función <mark style="color:green;">**`length()`**</mark>

<pre class="language-r"><code class="lang-r">length(edad)
# [1] 10
<strong>
</strong>length(letras)
# [1] 5
</code></pre>

Tenemos distintas formas de acceso a las posiciones del vector:

```r
#Para acceder a una posición k del vector: 
edad[3]
# [1] 29

#Para acceder a varios valores dentro de un rango:
edad[2:5]
# [1] 34 29 25 30

#Para acceder a varios valores alternos:
edad[c(1,3,7)]
# [1] 22 29 31
```

Para acceder de forma condicionada a un vector usaremos la función <mark style="color:green;">**`which()`**</mark>

```r
which(edad>25)
# [1] 2 3 5 6 7 8

#Podemos asignar estas posiciones a una nueva variable:
mayores25<-which(edad>25) 
edad[mayores25]
# [1] 34 29 30 33 31 27

# y de forma simplificada:
edad[edad>25]
# [1] 34 29 30 33 31 27
```

Podríamos hacer también una selección en función de valores de otro vector:

```r
sexo <- c("M","H","H","M","M","H","M","M","H","H")
edad[sexo=="M"]
## [1] 22 25 30 31 27
```

En _**tidyverse**_ podemos usar la opción <mark style="color:green;">**`filter`**</mark>

```r
# Filtrar personas con edad > 25
datos |> 
  filter(edad > 25)

# Extraer solo el vector de edad correspondiente
datos |> 
  filter(edad > 25) |> 
  pull(edad)

# Edad de personas con sexo "M"
datos |>
  filter(sexo == "M") |> 
  pull(edad)
```

## Factores

Un **factor** es una variable categórica con un número finito de valores o _niveles_. En R los factores se utilizan habitualmente para realizar clasificaciones de los datos, estableciendo su pertenencia a los grupos o categorías determinados por los niveles del factor.

Los factores juegan un papel muy importante en la elaboración de modelos estadísticos. En particular, los modelos lineales pueden dar resultados muy diferentes según que una variable se declare como numérica o como factor.

Los niveles de un factor puede estar codificados como valores numéricos o como caracteres. Independientemente de que el factor sea numérico o carácter, sus valores son siempre almacenados internamente por R como números enteros, con lo que se consigue economizar memoria.

_<mark style="color:orange;">Ejemplo</mark>_

```r
sexo <- factor(sexo,levels=c("H","M"),labels=c("Hombre","Mujer"))
sexo
# [1] Mujer  Hombre Hombre Mujer  Mujer  Hombre Mujer  Mujer   Hombre Hombre
# Levels: Hombre Mujer
```

Y podemos construir su tabla de frecuencias con la función <mark style="color:green;">**`table()`**</mark>

```r
table(sexo)
# sexo
# Hombre  Mujer 
#      5      5
```

## Variables lógicas

Las variables lógicas constituyen un tipo particular de factor en R, que se caracteriza por tomar sólo dos valores: `TRUE` o `FALSE` codificados, respectivamente, como 1 y 0. Es posible construir condiciones lógicas utilizando los operadores _‘y’_ (`&`), _‘o’_ (`|`) y _‘no’_ (`!`). La comparación de valores se lleva a cabo mediante `==` (‘_es igual a_’) y `!=` (‘es distinto de’).

**ATENCIÓN: No debe confundirse el operador de asignación `=` con el de comparación `==`**.

_<mark style="color:orange;">**Ejemplo**</mark>_

Podemos realizar la tabla de frecuencias anterior pero teniendo sólo en cuenta aquellos individuos que estén solteros:

```r
soltero<-c(TRUE,TRUE,TRUE,FALSE,FALSE,TRUE,TRUE,FALSE,FALSE,TRUE)
soltero
#  [1]  TRUE  TRUE  TRUE FALSE FALSE  TRUE  TRUE FALSE FALSE  TRUE
table(sexo[soltero==TRUE])
```

Podemos hacer lo mismo con la función <mark style="color:green;">**`which()`**</mark> para acceder a las posiciones cuando es TRUE:

```r
which(soltero)
# [1]  1  2  3  6  7 10
table(sexo[which(soltero)])
# Hombre  Mujer 
#      4      2
```

## Fechas

R almacena las **fechas** en la clase _**Date**._  Esta clase almacena los valores como el número de días transcurridos desde el 1 de enero de 1970; para las fechas anteriores el número de días se cuenta en sentido negativo.

Para crear una fecha en R el modo más sencillo es utilizar la función <mark style="color:green;">**`as.Date()`**</mark>. Esta función recibe como argumento un dato de fecha en modo carácter y la convierte a la clase `Date`. En esta clase, el estándar de fecha que se utiliza (en el que se leen y muestran las fechas si no se indica otra cosa) es _“año-mes-día”_ (yyyy-mm-dd).&#x20;

_<mark style="color:orange;">Ejemplo</mark>_

```r
navidad=as.Date("2013-12-25")
navidad
# [1] "2013-12-25"

class(navidad)
# [1] "Date"
```

Si introducimos la fecha en otro formato (por ejemplo _“dia/mes/año”_), hemos de indicarlo al usar <mark style="color:green;">**`as.Date`**</mark> para que R pueda interpretar la fecha correctamente:

```r
navidad=as.Date("25/12/2013",format="%d/%m/%Y")
navidad
# [1] "2013-12-25"
```

Esta conversión opera también sobre vectores (siempre que estén codificados del mismo modo):

```r
dias=c("1/10/2005","2/2/2006","3/4/2006","8/11/2014")
dias
# [1] "1/10/2005" "2/2/2006"  "3/4/2006"  "8/11/2014"

dias=as.Date(dias,format="%d/%m/%Y")
# [1] "2005-10-01" "2006-02-02" "2006-04-03" "2014-11-08"
```

#### Formatos de fecha

Para formatear una fecha se pueden utilizar los siguientes símbolos:

<table><thead><tr><th width="188">Símbolo</th><th>Significado</th></tr></thead><tbody><tr><td>%d</td><td>día (numérico, de 0 a 31)</td></tr><tr><td>%a</td><td>día de la semana abreviado a tres letras</td></tr><tr><td>%A</td><td>día de la semana (nombre completo)</td></tr><tr><td> </td><td> </td></tr><tr><td>%m</td><td>mes (numérico de 0 a 12)</td></tr><tr><td>%b</td><td>mes (nombre abreviado a tres letras)</td></tr><tr><td>%B</td><td>mes (nombre completo)</td></tr><tr><td> </td><td> </td></tr><tr><td>%y</td><td>año (con dos dígitos)</td></tr><tr><td>%Y</td><td>año (con cuatro dígitos)</td></tr></tbody></table>

La función <mark style="color:green;">**`Sys.Date()`**</mark> lee la fecha actual del reloj de nuestro ordenador y la codifica como un objeto de la clase `Date`:

```r
Sys.Date()
```

## Lectura de datos en R

El formato más cómodo y habitual de lectura en R es el CSV en el que normalmente la primera fila contiene los nombres de las columnas que serán las variables y las siguientes filas contendrán los datos correspondientes. Las columnas están separadas por comas.&#x20;

Para la lectura de CSV en R usaremos la función <mark style="color:green;">**`read.csv()`**</mark>y con la función <mark style="color:green;">**`str()`**</mark> podemos ver las variables y el tipo de dato con el que se han importado.&#x20;

{% file src="../../.gitbook/assets/TiposDatos.csv" %}

```r
datos<-read.csv("Tema_1/TiposDatos.csv")
str(datos)
```

Ahora es tu turno: Realiza los siguientes ejercicios:

{% content-ref url="ejercicio-tipos-de-datos/" %}
[ejercicio-tipos-de-datos](ejercicio-tipos-de-datos/)
{% endcontent-ref %}
