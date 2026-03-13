# Ejercicio 3.1: Detección de datos perdidos

La siguiente base de datos corresponde al rendimiento académico de alumnado que cursan una FP:&#x20;

{% file src="../../../.gitbook/assets/students_FP.csv" %}

| <mark style="color:purple;">`student_id`</mark>       | Identificador único alfanumérico del estudiante.                    |
| ----------------------------------------------------- | ------------------------------------------------------------------- |
| <mark style="color:purple;">`hours_work_week`</mark>  | Cantidad de horas que el estudiante trabaja por semana.             |
| <mark style="color:purple;">`hours_study_week`</mark> | Cantidad de horas que el estudiante dedica al estudio semanalmente. |
| <mark style="color:purple;">`attendance_pct`</mark>   | Porcentaje de asistencia a clases (0 a 100).                        |
| <mark style="color:purple;">`gpa`</mark>              | Promedio de calificaciones acumulado (Grade Point Average).         |
| <mark style="color:purple;">`exam_score`</mark>       | Puntuación obtenida en el examen parcial.                           |
| <mark style="color:purple;">`program`</mark>          | Nombre de la FP que cursa.                                          |
| <mark style="color:purple;">`study_mode`</mark>       | Modalidad en la que se imparten las clases.                         |
| <mark style="color:purple;">`shift`</mark>            | Turno en el que el estudiante asiste a clase.                       |

```r
library(naniar)
library(tidyverse)
data <- read.csv("students_FP.csv",
na.strings = c("", "NA", "NaN", "NULL"),
stringsAsFactors = TRUE
)
```

Realiza lo siguiente:

1. Cuantifica el número de datos perdidos que hay en la base de datos tanto a nivel global como de forma individual, piensa en estos números y qué significan.&#x20;
2. Visualiza los datos perdidos de forma global y comenta los gráficos.&#x20;
3. Clasifica en MAR, MNAR y MCAR los datos perdidos. Para ello usa la siguiente guía:
   1. Visualiza por subgrupos de las variables cualitativas.
   2. Visualiza por las variables cuantitativas.
   3. Crea el dataset de shadow y visualiza por cuantitativas y cualitativas para verificar lo observado anteriormente.

