---
hidden: true
---

# Ejercicio - Epidemia Ébola con MICE

Con las variables siguientes de la base de datos de la epidemia de Ébola:

<pre class="language-r"><code class="lang-r"><strong>load("linelist.Rdata")
</strong>data2&#x3C;-data[,c("outcome","gender","age","hospital","ct_blood","fever","chills","cough","aches","vomit","temp","days_onset_hosp")]
</code></pre>

Realiza los siguientes pasos:

1. Estudia el tipo de missing data. Para facilitarte el proceso, puedes estudiar los missing de las variables cualitativas por un lado usando <mark style="color:green;">**`gg_miss_var()`**</mark> y los missing de las variables cuantitativas usando <mark style="color:green;">**marginplot()**</mark>. También puedes proponer otras formas.&#x20;

```r
vis_miss(data2)

##Variables cualitativas
gg_miss_var(data2, show_pct = TRUE,facet = outcome)
gg_miss_var(data2, show_pct = TRUE,facet = gender)
gg_miss_var(data2, show_pct = TRUE,facet = hospital)
gg_miss_var(data2, show_pct = TRUE,facet = fever)
gg_miss_var(data2, show_pct = TRUE,facet = chills)
## Outcome y hospital no parecen tener relación (MCAR)
## Gender y Age parece tener los mismos missing, asumir MCAR o que hay algo no observado detrás

##Cuantitativas
marginplot(data2[,c("age","temp")])
marginplot(data2[,c("age","days_onset_hosp")])
marginplot(data2[,c("age","ct_blood")])
marginplot(data2[,c("temp","ct_blood")])
##Temp parece que tiene missing con los valores bajos de ct_blood pero no con todos
##Quizás podríamos pensar que aquellos indiviudos con valores bajos de ct_blood son individuos más sanos a los
##que ya no se les cogió la temperatura y el resto de síntomas pero al haber otros que si, está asociación
##no es exclusiva. Podría ser un caso de MAR o MNAR si hay algo más detrás.

marginplot(data2[,c("days_onset_hosp","ct_blood")])
##days_onset_hospital con los valores altos de ct_blood pero no con todos
###Síntomas parecen tener una relación ya que son las mismas observaciones
marginplot(data2[,c("temp","fever")]) 
##La fiebre falta en aquellos con temperatura baja, pero no todos los
## que tienen temperatura baja falta la fiebre, por tanto parece que estamos ante un claro caso de MNAR y
## el resto de síntomas cumplirían lo mismo

#####Cuantitativa con cualitativa
shadowed_data <- data2 %>% bind_shadow()
names(shadowed_data)
ggplot (data = shadowed_data, mapping = aes(x = temp,colour = fever_NA)) +   
 geom_density() 
 ##Aquí vemos como los síntomas faltan para aquellas variables que tienen temp baja,
 ##Aunque hay algunos que tienen temperatura baja siendo no missing
 ##En principio los sintomas parecen MAR aunque hay algo no observado por detrás
```



2. Realiza una imputación con mice usando todas las variables usando 100 iteraciones y generandoo 5 bases de datos. ¿Hay algo que te llame la atención? ¿Te atreverías a generar 10 bases de datos o 500 iteraciones?

```r
impData <- mice(data2,m=5,maxit=10,seed=500)
```

3. Visualiza las variables cuantitativas para ver si se han imputado bien.

```r
###Resuemn de la imputación 
summary(impData)
###Datos completos
completedData <- complete(impData,1)
###Gráficos de visualización cuantitativas
densityplot(impData)
stripplot(impData, pch = 20, cex = 1.2)
```

4. Usando una de las 5 bases de datos completa, visualiza las variables cualitativas.

```r
# Graficar la distribución de las variable categóricas antes y después de la imputación
par(mfrow = c(1, 2))  # Organizar las gráficas en una fila de 2 columnas
barplot(table(data2$outcome, useNA = "ifany"), main = "Antes de la imputación")
barplot(table(completedData$outcome), main = "Después de la imputación")

barplot(table(data2$gender, useNA = "ifany"), main = "Antes de la imputación")
barplot(table(completedData$gender), main = "Después de la imputación")

barplot(table(data2$hospital, useNA = "ifany"), main = "Antes de la imputación")
barplot(table(completedData$hospital), main = "Después de la imputación")

barplot(table(data2$fever, useNA = "ifany"), main = "Antes de la imputación")
barplot(table(completedData$fever), main = "Después de la imputación")

barplot(table(data2$fever, useNA = "ifany"), main = "Antes de la imputación")
barplot(table(completedData$fever), main = "Después de la imputación")

barplot(table(data2$chills, useNA = "ifany"), main = "Antes de la imputación")
barplot(table(completedData$chills), main = "Después de la imputación")
```

5. Usando la variable temperatura, estudia si se ha imputado bien la variable fever.&#x20;

```r
boxplot(completedData$temp~completedData$fever)
cbind(data$fever,completedData$fever)
```
