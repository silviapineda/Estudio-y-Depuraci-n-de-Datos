# Ejercicio 3.2: Imputación de datos perdidos (simple)

Con la misma base de datos del ejercicio 3.1, realiza la imputación de todas las variables siguiendo los siguientes pasos

1. Las variables definidas como MCAR con <5% de datos faltantes, ¿Cómo las imputarías? Ejecútalo.
2. Realiza la imputación de las variables MAR/MNAR sabiendo los siguiente:
   1. La variable hours\_study\_week está relacionada con gpa y exam\_score, pero sus datos NA están sompletos sólo para la variable exam\_score. Lo puedes comprobar con:&#x20;

```r
vis_miss(select(data,hours_study_week,gpa,exam_score),cluster=TRUE)  + 
  theme(axis.text.x = element_text(angle = 90))
```

1.

