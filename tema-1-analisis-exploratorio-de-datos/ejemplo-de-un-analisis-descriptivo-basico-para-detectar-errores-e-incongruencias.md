---
cover: ../.gitbook/assets/image (112).png
coverY: 0
---

# Ejemplo de un análisis descriptivo básico para detectar errores e incongruencias

## Pasos para la detección inicial de errores

Para este apartado vamos a utilizar el conjunto de datos "Wine Quality" utilizado en Cortez et al. (2009).&#x20;

{% file src="../.gitbook/assets/DatosVino.xlsx" %}

Las variables son las siguientes:

<table><thead><tr><th width="193.9140625">Variable</th><th width="448.65625">Descripción</th></tr></thead><tbody><tr><td><mark style="color:purple;"><code>Id</code></mark></td><td>Código identificativo del tipo de vino</td></tr><tr><td><mark style="color:purple;"><code>Beneficio</code></mark></td><td>Beneficio obtenido por la venta de ese tipo de vino</td></tr><tr><td><mark style="color:purple;"><code>Compra</code></mark></td><td>Variable dicotómica que toma valor 1 si se ha realizado algún pedido de ese tipo de vino, y 0, en caso contrario</td></tr><tr><td><mark style="color:purple;"><code>Acidez</code></mark></td><td>rango ilimitado de valores</td></tr><tr><td><mark style="color:purple;"><code>Acidocitrico</code></mark></td><td>rango ilimitado de valores</td></tr><tr><td><mark style="color:purple;"><code>Azucar</code></mark></td><td>Sólo valores positivos</td></tr><tr><td><mark style="color:purple;"><code>Clorurosodico</code></mark></td><td>rango ilimitado de valores</td></tr><tr><td><mark style="color:purple;"><code>Densidad</code></mark></td><td>Sólo valores positivos</td></tr><tr><td><mark style="color:purple;"><code>Ph</code></mark></td><td>Entre 4 y 10</td></tr><tr><td><mark style="color:purple;"><code>Sulfatos</code></mark></td><td>rango ilimitado de valores</td></tr><tr><td><mark style="color:purple;"><code>Alcohol</code></mark></td><td>Contenido de alcohol en % </td></tr><tr><td><mark style="color:purple;"><code>Etiqueta</code></mark></td><td>Percepción del diseño de la etiqueta (MM=muy mal, M=malo, R=regular, B=bueno, MB=muy bueno)</td></tr><tr><td><mark style="color:purple;"><code>CalifProductor</code></mark></td><td>Calificacion (entre 0 y 9, sin decimales) del vino según el productor</td></tr><tr><td><mark style="color:purple;"><code>Clasificacion</code></mark></td><td>Calsificacion obtenida por un equipo de expertos (toma 4 valores, siendo los extremos "****" = "excelente" y "+" = "pobre")</td></tr><tr><td><mark style="color:purple;"><code>Region</code></mark></td><td>Region de la que proviene (toma 3 valores distintos)</td></tr><tr><td><mark style="color:purple;"><code>PrecioBotella</code></mark></td><td>Precio por botella</td></tr></tbody></table>

### 1. Leer los datos

Lo primero que haremos es leer los datos. Seguidamente verificamos que los tipos de variables se hayan asignado correctamente con <mark style="color:green;">**`str()`**</mark>

```r
#install.packages("readxl")
library(readxl)
datos<-read_excel("DatosVino.xlsx")
str(datos)
```

```r
tibble [6,365 × 16] (S3: tbl_df/tbl/data.frame)
 $ ID            : num [1:6365] 13606 3119 1190 5261 2871 ...
 $ Beneficio     : num [1:6365] 0 0 0 0 0 0 0 0 0 0 ...
 $ Compra        : num [1:6365] 0 0 0 0 0 0 0 0 0 0 ...
 $ Acidez        : num [1:6365] 0.39 2.19 1.44 0.5 -0.1 1.26 0.85 0.34 0.46 -0.555 ...
 $ AcidoCitrico  : num [1:6365] 0.14 2.58 -1.54 0.3 0.3 1.26 0.14 -1.04 -0.74 0.27 ...
 $ pH            : num [1:6365] 4.99 5.54 5.1 5.23 5.75 5.3 5.74 5.12 5.37 4.96 ...
 $ CloruroSodico : num [1:6365] 0.073 0.205 0.058 0.276 0.048 0.305 0.169 -0.345 0.064 0.315 ...
 $ Densidad      : num [1:6365] 1.022 0.954 0.993 0.996 1.027 ...
 $ Azucar        : num [1:6365] 5.9 6.4 7.1 3.5 7.2 3.7 7.4 6.2 1.5 4 ...
 $ Sulfatos      : num [1:6365] 0.12 0.49 0.39 -0.81 0.44 1.92 -0.99 -0.26 1.91 NA ...
 $ Alcohol       : num [1:6365] 6.6 112 10.2 4.4 10.1 14 10.1 9.3 11 10 ...
 $ Etiqueta      : chr [1:6365] "B" "b" "B" "R" ...
 $ CalifProductor: num [1:6365] 5 4 2 4 2 3 4 2 5 4 ...
 $ Clasificacion : chr [1:6365] "?" "?" "?" "?" ...
 $ Region        : num [1:6365] 1 NA 1 3 1 NA 1 3 2 1 ...
 $ PrecioBotella : num [1:6365] 4.56 3.01 1 1.85 1.28 7 2.1 5.21 2.95 1 ...
```

### 2. Declarar bien las variables

No todas las variables categóricas han sido asignadas a factores, por lo que debemos modificarlo. Para ello usamos <mark style="color:green;">**`as.factor()`**</mark>. Podemos hacerlo en combinación con <mark style="color:green;">**`lapply()`**</mark> que nos permite aplicar la misma función a varios objetos.&#x20;

```r
columns_to_factor<-c("Compra","Etiqueta","Clasificacion","Region")
datos[,columns_to_factor]<-
  lapply(datos[,columns_to_factor],as.factor)
str(datos)
```

### 3. Resumen de todas las variables para detectar bien los errores iniciales

Ahora podemos ver un resumen de todas las variables para poder detectar errores iniciales usando la función <mark style="color:green;">**`summary()`**</mark>

```r
summary(datos)
```

```r
 ID              Beneficio        Compra   Acidez            AcidoCitrico      pH              CloruroSodico     Densidad         Azucar      
 Min.   :    2   Min.   :   0.0   0:1367   Min.   :-2.7900   Min.   :-3.2400   Min.   :4.800   Min.   : -1.171   Min.   :0.8881   Min.   : 0.000  
 1st Qu.: 3980   1st Qu.: 236.0   1:4998   1st Qu.: 0.1300   1st Qu.: 0.0200   1st Qu.:6.820   1st Qu.: -0.004   1st Qu.:0.9882   1st Qu.: 6.700  
 Median : 8065   Median : 480.0            Median : 0.2800   Median : 0.3100   Median :7.920   Median :  0.048   Median :0.9944   Median : 8.900  
 Mean   : 8011   Mean   : 452.4            Mean   : 0.3312   Mean   : 0.3144   Mean   :7.529   Mean   : 46.978   Mean   :0.9942   Mean   : 8.851  
 3rd Qu.:12027   3rd Qu.: 671.0            3rd Qu.: 0.6500   3rd Qu.: 0.5800   3rd Qu.:8.400   3rd Qu.:  0.224   3rd Qu.:1.0006   3rd Qu.:11.000  
 Max.   :16128   Max.   :1568.0            Max.   : 3.6800   Max.   : 3.8600   Max.   :9.300   Max.   :999.000   Max.   :1.0992   Max.   :21.000  
                                                                                                                                                  
 Sulfatos          Alcohol          Etiqueta       CalifProductor  Clasificacion Region      PrecioBotella   
 Min.   :-3.1200   Min.   : -4.50   R      :2380   Min.   :0.000   ?   :1680     1   :2059   Min.   : 1.000  
 1st Qu.: 0.2800   1st Qu.:  9.90   M      :1357   1st Qu.:2.000   *   :1535     2   :2067   1st Qu.: 1.420  
 Median : 0.5000   Median : 11.60   B      :1282   Median :3.000   **  :1754     3   :2132   Median : 2.190  
 Mean   : 0.5267   Mean   : 17.46   r      : 420   Mean   :2.759   *** :1074     NA's: 107   Mean   : 2.611  
 3rd Qu.: 0.8800   3rd Qu.: 14.00   m      : 230   3rd Qu.:3.000   ****: 322                 3rd Qu.: 3.440  
 Max.   : 4.2100   Max.   :150.00   MM     : 216   Max.   :9.000                             Max.   :11.440  
```

Con estos resultados, podemos analizar las variables del conjunto de datos y detectar errores que deben ser tratados posteriormente

**Posibles errores en las variables:**

* La variable <mark style="color:purple;">`CloruroSodico`</mark> tiene un valor máximo de 999, lo que suele representar valores ausentes.
* La variable <mark style="color:purple;">`Alcohol`</mark> toma valores fuera del intervalo 0-100.
* La variable <mark style="color:purple;">`Etiqueta`</mark> tiene más categorías de las que necesita debido al uso de mayúsculas y minúsculas.&#x20;
* La variable <mark style="color:purple;">`Clasificacion`</mark> tiene valores representados por el símbolo ?, lo que también suele representar valores ausentes.&#x20;

### 4. Corregir errores

#### Variable <mark style="color:purple;">`CloruroSodico`</mark>

Para verificar si los valores 999 de la variable <mark style="color:purple;">`CloruroSodico`</mark> son efectivamente ausentes, podemos representar su histograma

```r
hist(datos$CloruroSodico,xlab="CloruroSodico")
```



<figure><img src="../.gitbook/assets/image (91).png" alt="" width="375"><figcaption></figcaption></figure>

Queda claro que ese dato es erróneo, lo convertimos en `NA`

```r
datos$CloruroSodico<-replace(datos$CloruroSodico,datos$CloruroSodico==999,NA)
hist(datos$CloruroSodico,main="CloruroSodico")
```



#### Variable <mark style="color:purple;">`Alcohol`</mark>

En el caso de la variable _<mark style="color:purple;">`Alcohol`</mark>_, vamos a declarar como _NA_ los valores fuera de rango

```r
datos$Alcohol<-replace(datos$Alcohol,which((datos$Alcohol<0) | (datos$Alcohol>100)),NA)
hist(datos$Alcohol,main="Alcohol")
```



#### Variable <mark style="color:purple;">`Etiqueta`</mark>

Finalmente vamos a corregir los errores de escritura de la variable _<mark style="color:purple;">`Etiqueta`</mark>_, para ello podemos usar la funcion <mark style="color:green;">**`recode()`**</mark> del paquete **`car`**

<pre class="language-r"><code class="lang-r"><strong>table(datos$Etiqueta)
</strong><strong>datos$Etiqueta&#x3C;-car::recode(datos$Etiqueta,"'b'='B';'m'='M';'mb'='MB';'mm'='MM';'r'='R'")
</strong>table(datos$Etiqueta)
</code></pre>

#### Variable <mark style="color:purple;">`Clasificacion`</mark>

Para la variable _<mark style="color:purple;">`Clasificacion`</mark>_ vamos a recodificar ? por `NA` &#x20;

```r
table(datos$Clasificacion)
datos$Clasificacion<-car::recode(datos$Clasificacion,"'?'= NA")
table(datos$Clasificacion)
```

Vamos a ver que efectivamente todos los errores se han corregido y guardamos el fichero corregido

```r
summary(datos)

#install.packages("writexl")
library(writexl)
write_xlsx(datos,"DatosVino_corregido.xlsx")
```

```r
   ID          Beneficio      Compra       Acidez         AcidoCitrico           pH        CloruroSodico         Densidad          Azucar          Sulfatos      
 Min.   :    2   Min.   :   0.0   0:1367   Min.   :-2.7900   Min.   :-3.2400   Min.   :4.800   Min.   :-1.17100   Min.   :0.8881   Min.   : 0.000   Min.   :-3.1200  
 1st Qu.: 3980   1st Qu.: 236.0   1:4998   1st Qu.: 0.1300   1st Qu.: 0.0200   1st Qu.:6.820   1st Qu.:-0.03275   1st Qu.:0.9882   1st Qu.: 6.700   1st Qu.: 0.2800  
 Median : 8065   Median : 480.0            Median : 0.2800   Median : 0.3100   Median :7.920   Median : 0.04600   Median :0.9944   Median : 8.900   Median : 0.5000  
 Mean   : 8011   Mean   : 452.4            Mean   : 0.3312   Mean   : 0.3144   Mean   :7.529   Mean   : 0.05135   Mean   :0.9942   Mean   : 8.851   Mean   : 0.5267  
 3rd Qu.:12027   3rd Qu.: 671.0            3rd Qu.: 0.6500   3rd Qu.: 0.5800   3rd Qu.:8.400   3rd Qu.: 0.14675   3rd Qu.:1.0006   3rd Qu.:11.000   3rd Qu.: 0.8800  
 Max.   :16128   Max.   :1568.0            Max.   : 3.6800   Max.   : 3.8600   Max.   :9.300   Max.   : 1.35100   Max.   :1.0992   Max.   :21.000   Max.   : 4.2100  
                                                                                               NA's   :299                                          NA's   :604      
    Alcohol      Etiqueta  CalifProductor  Clasificacion  Region     PrecioBotella   
 Min.   : 0.80   B :1491   Min.   :0.000   *   :1535     1   :2059   Min.   : 1.000  
 1st Qu.: 9.90   M :1587   1st Qu.:2.000   **  :1754     2   :2067   1st Qu.: 1.420  
 Median :11.50   MB: 231   Median :3.000   *** :1074     3   :2132   Median : 2.190  
 Mean   :11.92   MM: 256   Mean   :2.759   ****: 322     NA's: 107   Mean   : 2.611  
 3rd Qu.:13.60   R :2800   3rd Qu.:3.000   NA's:1680                 3rd Qu.: 3.440  
 Max.   :26.50             Max.   :9.000                             Max.   :11.440  
```
