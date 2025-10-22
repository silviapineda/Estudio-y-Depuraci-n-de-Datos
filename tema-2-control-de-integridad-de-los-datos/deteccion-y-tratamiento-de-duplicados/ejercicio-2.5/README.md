# Ejercicio

Con la siguiente base de datos generada en R, realiza los siguientes ejercicios:

```r
set.seed(123)  # Para reproducibilidad
df <- data.frame(  ID = c(1, 2, 3, 4, 5, 3, 4, 6, 7, 8, 2, 9, 10),  # ID con duplicados  
    Nombre = c("Ana", "Luis", "Carlos", "Marta", "Sofía", "Carlos", "Marta", "Jorge", "Elena", "Daniel", "Luis", "Raúl", "Clara"),  
    Edad = c(25, 30, 22, 28, 35, 22, 28, 40, 26, 32, 30, 45, 29),  
    Ciudad = c("Madrid", "Barcelona", "Sevilla", "Madrid", "Bilbao", "Sevilla", "Madrid", "Valencia", "Zaragoza", "Barcelona", "Barcelona", "Málaga", "Granada")
)
```

1. Identifica los duplicados en toda las fila
2. Identifica los IDs de los duplicados
3. Elimina los duplicados manteniendo la primera observación que aparece



