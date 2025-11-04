# Ejercicio 3.2: Imputación de datos perdidos (simple)

Con la misma base de datos del ejercicio 3.1, realiza la imputación de todas las variables siguiendo los siguientes pasos

1. Las variables definidas como MCAR con <5% de datos faltantes, ¿Cómo las imputarías? Ejecútalo.
2.  Realiza la imputación de las variables MAR/MNAR sabiendo los siguiente:

    * La variable <mark style="color:purple;">`hours_study_week`</mark> está relacionada con <mark style="color:purple;">`gpa`</mark> y <mark style="color:purple;">`exam_score`</mark>, pero sus datos NA están sompletos sólo para la variable <mark style="color:purple;">`exam_score`</mark>. Lo puedes comprobar con:&#x20;

    ```r
    ggplot(data = data, aes (x = hours_study_week , y =gpa )) + geom_miss_point()
    ggplot(data = data, aes (x = hours_study_week , y =exam_score )) + geom_miss_point()

    vis_miss(select(data,hours_study_week,gpa,exam_score),cluster=TRUE)  + 
      theme(axis.text.x = element_text(angle = 90))
    ```



    * La variable <mark style="color:purple;">`exam_score`</mark> está relacionada con <mark style="color:purple;">`gpa`</mark> y <mark style="color:purple;">`hours_study_week`</mark>, pero sus datos NA están sompletos sólo para la variable <mark style="color:purple;">`hours_study_week`</mark>. Comprueba esta afirmación y realiza la imputación que consideres.&#x20;
    * La variable <mark style="color:purple;">`attendance_pct`</mark> está relacionada con <mark style="color:purple;">`gpa`</mark> y <mark style="color:purple;">`exam_score`</mark>, pero sus datos NA están completos sólo para la variable <mark style="color:purple;">`exam_score`</mark>. Comprueba esta afirmación y realiza la imputación que consideres.&#x20;
    * La variable <mark style="color:purple;">`gpa`</mark> está relacionada con <mark style="color:purple;">`hours_study_week`</mark> y <mark style="color:purple;">`exam_score`</mark>, pero sus datos NA no están completos para ninguna de esas dos variables. ¿Qué harías? Realiza la imputación que consideres



