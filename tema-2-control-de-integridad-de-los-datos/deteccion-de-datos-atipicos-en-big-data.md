---
hidden: true
cover: >-
  ../.gitbook/assets/138395349-concepto-de-tecnología-futura-con-la-mano-presionando-un-botón-en-una-pantalla-de-tecnología.jpg
coverY: 0
---

# Detección de datos atípicos en Big Data

Lo primero es cargar los datos que ya limpiamos de errores en el Tema 1:

{% file src="../.gitbook/assets/TCGA.BRCA.sampleMap_HiSeqV2_Tema1.csv" %}

Primero cargamos la base de datos y luego siguiendo con lo que ya veíamos en el Tema 1, cuando representamos en un boxplot todos los genes (variables) vemos que hay muchos datos atípico, pero ¿Qué hacer con ellos? ¿Son realmente atípicos?

```r
##Primero cargamos la base de datos
breast.cancer.data<-read.csv("TCGA.BRCA.sampleMap_HiSeqV2_Tema1.csv")


boxplot(breast.cancer.data[3:ncol(breast.cancer.data)])
```

Aquí vamos a diferenciar entre las variables que tiene "cosas raras" y las que tienen unos pocos atípicos.&#x20;

Primero vemos que hay unos cuantos genes que tienen muchos ceros y luego unos pocos atípicos:

<figure><img src="../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

Para hacer esto de una forma automatizada, podemos primero contar cuantos ceros hay por variable y luego filtrar los que tengan muchos.

```r
### Sumamos por columnas el número de ceros
colSums(breast.cancer.data == 0)

## Lo vemos relativo al número de individuos
(colSums(breast.cancer.data == 0)/dim(breast.cancer.data)[1])

### Filtrar todos los genes que sean cero en mas de un 20% de las observaciones
umbral<-dim(breast.cancer.data)[1]*0.20
breast.cancer.data.filter<-breast.cancer.data[,which(colSums(breast.cancer.data == 0) < umbral)]

```

Ahora vamos con los outliers. Para ver los outliers podríamos aplicar la función descrita en este [tema](/broken/pages/I4jkN01jSoWh684mZ7T5), pero ir tomando la decisión uno a uno puede ser muy tedioso:

```r
numeric_integer_vars <- names(which(sapply(breast.cancer.data.filter, is.numeric) | sapply(breast.cancer.data.filter, is.integer)))
outliers_results <- lapply(paste0("breast.cancer.data.filter$", numeric_integer_vars), outliers)
```

Por lo tanto, habrá que automatizar todavía más la función. En este caso vamos a borrar automáticamente los valores que son datos extremos, pero no outliers, ya que en este tipo de datos, los outliers entra dentro de la normalidad

<pre class="language-r"><code class="lang-r">#### Automatizo una función
eliminar_outliers &#x3C;- function(df, umbral = 3) {    

<strong>    # Obtener nombres de columnas numéricas  
</strong>    columnas_numericas &#x3C;- names(df)[sapply(df, is.numeric) | sapply(df,is.integer)]    

    # Iterar sobre columnas numéricas  
    columnas&#x3C;-c()  
    for (columna in columnas_numericas) {        

        # Obtener datos de la columna    
        datos &#x3C;- df[[columna]]            

        # Calcular el rango intercuartílico (IQR)    
        iqr &#x3C;- IQR(datos,na.rm = TRUE)            

        # Definir límites para los outliers        
        umbral_inferior &#x3C;- quantile(datos,na.rm = TRUE)[2] - umbral * iqr    
        umbral_superior &#x3C;- quantile(datos,na.rm = TRUE)[4] + umbral * iqr            

        # Identificar y reemplazar outliers con NA    
        df[[columna]][datos &#x3C; umbral_inferior | datos > umbral_superior] &#x3C;- NA    
        if(any(datos &#x3C; umbral_inferior | datos > umbral_superior)){      
            columnas&#x3C;-c(columnas,columna)      
        }    

    }    

    # Retornar el data frame con las columnas sin outliers  
    return(list(df = df, columnas = columnas))
}

# Aplicar la función
result&#x3C;- eliminar_outliers(breast.cancer.data.filter,3)
breast.cancer.data.filter_no_outliers&#x3C;-result$df
affected_columns &#x3C;- result$columnas

# Coprobamos con un boxplot
boxplot(breast.cancer.data.filter[,affected_columns])
breast.cancer.data.filter$tumor_type&#x3C;-as.factor(breast.cancer.data.filter$tumor_type)
boxplot(breast.cancer.data.filter[,affected_columns[1]]~breast.cancer.data.filter$tumor_type)

###Método Multivariante
lof_result&#x3C;-lof(breast.cancer.data.filter[,3:ncol(breast.cancer.data.filter)])
breast.cancer.data.filter[lof_result>1.5,]
boxplot(t(breast.cancer.data.filter[lof_result>1.5,3:ncol(breast.cancer.data.filter)]))


</code></pre>

Finalmente comprobamos si hay duplicados

```r
hay_duplicados<-duplicated(breast.cancer.data.filter_no_outliers)
table(hay_duplicados)

## Guardamos el fichero para usarlo en los próximos temas
write.csv(breast.cancer.data.filter_no_outliers,"TCGA.BRCA.sampleMap_HiSeqV2_Tema2.csv",row.names = F)
```
