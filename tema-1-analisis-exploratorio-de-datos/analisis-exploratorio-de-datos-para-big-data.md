---
hidden: true
cover: >-
  ../.gitbook/assets/138395349-concepto-de-tecnología-futura-con-la-mano-presionando-un-botón-en-una-pantalla-de-tecnología.jpg
coverY: 0
---

# Análisis exploratorio de datos para Big Data

Cuando tenemos bases de datos muy grandes, la aplicación de estas técnicas se puede convertir en una práctica muy compleja. Por ejemplo, si en vez de tener 16 variables como en el ejemplo anterior, tenemos 60,000 variables medidas en más de 1,000 observaciones, ir verificando una a una si hay "cosas" raras es imposible.&#x20;

## Ejemplo de aplicación a las ciencias ómicas

¿Qué son las ciencias _**ómicas**_?

* Son las ciencias que analizan grandes cantidades de datos generando información relevante para la toma de decisiones en nuestras salud y enfermedades.&#x20;
* Son un grupo de disciplinas nuevas que manejan conocimiento que se genera a partir de medidas de un gran número de elementos biológicos que están involucradas en el comportamiento normal o patológico del ser humano.&#x20;

**Algunos ejemplos**:&#x20;

* **Genómica**: Es nuestro ADN, son kilómetros de letras seguidas que definen al ser humano, en total unos 3 mil millones de letras que podemos traducir a 0 y 1.&#x20;
* **Transcriptómica**: La traducción del ADN a genes que definen nuestros caracteres (color de ojos, pelo, etc). El cuerpo humano lo conforman 60,000 genes de los cuales solo 20,000 se sabe su función.&#x20;
* **Microbioma**: Los millones de bacterias, hongos, virus que pueden vivir en nuestro cuerpo.&#x20;

Todo esto, o la combinación de todo este conocimiento nos puede servir en la práctica clínica para hacer un diagnóstico más temprano o prevenir el desarrollo de una enfermedad o desarrollar un fármaco nuevo.&#x20;

Cada uno de estos elementos se traduce a enormes cantidades de datos que generan matrices que los científicos de datos podemos analizar:

**Genómica**:

<figure><img src="../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>



**Transcriptómica**:

<figure><img src="../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>



**¿Cómo depuramos estos datos?**

Vamos a usar como ejemplo los datos de transcriptómica de 200 genes medidos en 153 pacientes de los cuales 87 tienen cáncer de mama y 66 son individuos sanos. El obejetivo del estudio sería ver si hay genes que puedan estar asociados con el desarrollo de cáncer de mama. Ésto nos puede ayudar a prevenir el cáncer o a buscar nuevos fármacos para luchar contra la enfermedad.

{% file src="../.gitbook/assets/TCGA.BRCA.sampleMap_HiSeqV2.csv" %}

## 1) Leer los datos

```r
datos<-read.csv("TCGA.BRCA.sampleMap_HiSeqV2.csv")
```

| Variables           | Descripción                                                                                                                                                              |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| sample\_id          | Identificador del paciente                                                                                                                                               |
| tumor\_1\_normal\_2 | Si tiene cáncer de mama es 1 y si es una mujer sana es 2                                                                                                                 |
| Genes               | El resto de variables corresponden a los nombres de los genes estudiados que son variables cuantitativas representando los niveles de expresión. No pueden ser negativos |

## 2) Tipos de variables y codificación

<mark style="color:red;">¿Podemos ir mirando variable por variable si es numerica, categorica, etc?</mark>&#x20;

Una forma de asegurarnos lo que hay en un fichero tan grande es a través del uso de funciones como <mark style="color:green;">**`apply() sapply()`**</mark>

<mark style="color:green;">**`apply()`**</mark> es un tipo de función en R que aplica la función que se le pase como argumento a todos los elementos de una **matriz**.&#x20;

```r
apply(X, MARGIN, FUN)
```

`apply` tiene tres argumentos:

* `X`: Una matriz o un objeto que pueda coercionarse a una matriz, generalmente, un data frame.
* `MARGIN`: La dimensión (margen) que agrupará los elementos de la matriz `X`, para aplicarles una función. Son identificadas con números, **1** son filas y **2** son colummnas.
* `FUN`: La función que aplicaremos a la matriz `X` en su dimención `MARGIN`.

<mark style="color:green;">**`sapply()`**</mark> es un caso especial de <mark style="color:green;">**`apply()`**</mark>, diseñado para **aplicar funciones a todos los elementos de una lista, vector o data.frame**.&#x20;

```
sapply(X, FUN)
```

Para ver el tipo de dato en nuestro ejemplo usaríamos la función <mark style="color:green;">**`class()`**</mark> combinada con <mark style="color:green;">**`sapply()`**</mark>:

```r
type<-sapply(datos,class) #Nos guardamos todos los tipos de datos
table(type) #Resumimos el tipo de datos
# type
# character   integer   numeric         
#        1         4       197 

```

Vemos que tenemos una variable _character_, 4 _integer_ y 197 _numeric_. La variable _character_ debería ser la correspondiente al ID, las _numeric_ los genes, pero, ¿a qué corresponden las 4 _integer_?

```r
which(type=="integer")
summary(datos[,which(type=="integer")])
```

<figure><img src="../.gitbook/assets/image (183).png" alt="" width="375"><figcaption></figcaption></figure>

Vemos que uno de los _integer_ es la variable que nos indica si la muestra corresponde a mujer con cáncer o sana. Ésta debería ser un factor. Los otros tres corresponden a genes que o tienen todo ceros o tienen todo unos. Esto claramente son genes que son erróneos y por tanto procederemos a eliminarlos.&#x20;

```r
##Declaramos el factor
datos$tumor_1_normal_2<-factor(datos$tumor_1_normal_2,levels=c(1,2), 
                                labels = c("tumor","normal"))

##Cambiar nombre de variables
colnames(datos)[2]<-"tumor_type"

##Declaramos otra vez la variable type
type<-sapply(datos,class)
summary(datos[,which(type=="integer")])

#Borramos los erróneos
datos<-datos[,-which(type=="integer")]

##Comprobamos que esté ya todo bien
type<-sapply(datos,class)
table(type)
```

## 3) Tablas y Gráficos para detección de errores

Creamos un gráfico con las proporciones correspondientes para la variable _tumor\_type_:

```r
porcentajes<-prop.table(table(datos$tumor_type))*100
etiquetas<-c("tumor","normal")

pie(table(datos$tumor_type),
    col=c("lightsalmon","paleturquoise"),
    labels=paste(etiquetas, "\n", round(porcentajes, 1), "%"),
    main="Proporción de pacientes con tumor vs. normal")
```

![](<../.gitbook/assets/image (184).png>)

Para comprobar el resto de variables podemos hacer algunos gráficos:

Este tipo de datos están normalizados, por tanto una cosa que se debería comprobar es si siguen una distribución normal. Este lo trabajaremos más en el Tema 4.&#x20;

```
hist(data.matrix(datos[3:ncol(datos)]))
```

Para comprobar que no haya cosas raras podemos representar un boxplot e intentar ver todos los individuos y genes en un solo gráfico:

```r
boxplot(t(datos[3:ncol(datos)]))
```

<figure><img src="../.gitbook/assets/image (74).png" alt=""><figcaption></figcaption></figure>

Y otro por genes:

```
boxplot(datos[3:ncol(datos)])
```

<figure><img src="../.gitbook/assets/image (75).png" alt=""><figcaption></figcaption></figure>

En el primero no detectamos nada que llame la atención por tanto no parece que los pacientes tengan que pasar ningún filtro, pero en el de genes observamos que algunos genes tienen muchos ceros o valores muy cercanos a cero. En este tipo de datos esto es un problema ya que un gen con muchos ceros o valores cercanos a cero puede ser indicativo de que la secuenciación ha fallado para ese gen y por tanto habría que quitarlo. Esto lo veremos en el [Tema 2. ](/broken/pages/URTDCDyfevgBm2fPJCWA)
