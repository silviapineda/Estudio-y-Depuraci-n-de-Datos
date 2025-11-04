---
hidden: true
---

# Ejercicio - MICE

La siguiente base de datos perteneciente al paquete mice <mark style="color:orange;">**`nhanes`**</mark> contine 25 observaciones y 4 variables:_<mark style="color:purple;">`age group`</mark> (3 categorías)_ , _<mark style="color:purple;">`body mass index`</mark>_, _<mark style="color:purple;">`hypertension`</mark> (1:No, 2: Sí)_ y _<mark style="color:purple;">cholesterol</mark> (mg/dL)_.

```r
#Carga la base de datos
library(mice)
data<-nhanes
```

Realiza los siguientes pasos:

1. Inspecciona si las variables están bien declaradas
2. Inspecciona si las variables numéricas tienen datos atípicos
3. Visualiza y cuantifica los datos missing. ¿Qué observas?
4. Inspecciona el patrón de datos missing. ¿Qué observas? ¿Cuántas observaciones hay con todos las variables missing? ¿Y con 3 de las 4 variables?
5. Haz una imputación múltiple para rellenar los datos missing, después:
   1. ¿Qué métodos has usado para cada variable?
   2. Guarda los datos completos de alguna de las bases de datos imputadas
   3. Visualiza las imputaciones. ¿Cómo visualizarías la variable cualitativa?
6. Ajusta un modelo de regresión lineal para predecir el colesterol en función de las demás variables y calcula el $$R^2$$. Comenta los resultados.

