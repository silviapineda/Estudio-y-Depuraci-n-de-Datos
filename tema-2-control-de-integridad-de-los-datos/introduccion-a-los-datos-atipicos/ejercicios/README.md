# Ejercicios

Con los siguientes datos de la base de datos implementada en R <mark style="color:orange;">**`cars`**</mark>: donde medimos dos variables <mark style="color:purple;">`speed`</mark> (velocidad) y <mark style="color:purple;">`dist`</mark> (distancia) obtenemos dos bases de datos, una original y una a la que le introducimos unos datos atípicos a la variable <mark style="color:purple;">`dist`</mark>:

```r
cars_original <- cars # original data
outliers <- data.frame(speed=c(19,19,20,20,20), dist=c(190, 186, 210, 220, 218))  # introduce outliers.
cars_outliers <- rbind(cars_original, outliers)  # data with outliers.
```

Ajusta una recta de regresión para los datos originales y los datos después de introducir los datos atípicos para explicar la distancia (variable <mark style="color:purple;">`dist`</mark>) con la velocidad (variable <mark style="color:purple;">`speed`</mark>) y demuestra las diferencias mediante un gráfico.&#x20;

Después realiza un diagrama de cajas para las variables en ambas bases de datos. ¿Podemos ver datos atípicos en estas dos bases de datos?

