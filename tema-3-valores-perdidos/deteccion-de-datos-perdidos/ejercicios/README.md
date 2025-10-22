---
hidden: true
---

# Ejercicios

Con la misma base de datos de la epidemia de ébola haz los siguientes ejercicios:

```r
library(naniar)
library(tidyverse)
load("linelist.Rdata")
```

1. ¿Qué número absoluto de datos missing hay en esta base de datos? y ¿porcentaje? y ¿observaciones completas? ¿Te parecen muchos o pocos?
2. Usando un gráfico ¿Qué variables son las que más porcentaje de datos missing tienen? ¿Hay variables que no tengan datos missing o que tengan menos de un 5%?
3. Usando un gráfico, representa los datos missing de toda la base de datos por los valores de la variable gender, outcome, age\_cat y hospital. ¿Podemos ver algún patrón?
4. Imagímate que quieres estudiar los días que han pasado desde la fecha de hospitalización hasta el outcome según las características de los pacientes (age, gender, hospital) y sus síntomas (fever, chills, cough, aches vomit, temp). ¿Qué heatmap representarías para ver los datos missing? ¿Qué problemas vas a encontrar en este análisis?&#x20;
5. Representa un heatmap de todos los datos por las variables categóricas (gender, outcome y hospital y por las fechas date\_hospitalisation y date\_outcome). ¿Qué observas en estos gráficos?
6. Usando las variables "shadow", realiza un gráfico que muestren la distribución de las distintas fechas considerando los datos missing (NA) y no missing (!NA) de la variable edad y gender. ¿Qué conclusión sacarías?      &#x20;
7. Usando las variables "shadow", realiza un gráfico para ver si las personas que tienen missing en los síntomas, corresponden a persona más sanas (puedes usar la variable temp)

