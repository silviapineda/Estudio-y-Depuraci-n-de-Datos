# Introducción

Aunque un conjunto de datos parezca limpio (tipos correctos, codificaciones consistentes, sin duplicados evidentes y con atípicos revisados), rara vez se dispone de un conjunto de datos completo y perfecto. Es muy común encontrarse con **valores ausentes o perdidos** (_missing values_), es decir, celdas en la matriz  de datos que no contienen ninguna información válida. Estos valores pueden aparecer por múltiples razones:

* Errores en la recogida de información
* Fallos en sistemas de registro
* Abandono de encuestas por parte de los participantes
* Restricciones de privacidad
* etc

Los valores perdidos constituyen un problema relevante porque afectan directamente a la **calidad del análisis**. Una base de datos con muchos valores ausentes puede llevar a resultados sesgados, reducir la precisión de los modelos predictivos, o incluso hacer que ciertos algoritmos no funcionen correctamente. Por ello, en cualquier proyecto de análisis o depuración de datos es esencial abordar de manera sistemática este fenómeno.

En este tema se abordarán:

1. Cómo detectar, cuantificar y visualizar el patrón de datos ausentes.
2. Cómo interpretar los mecanismos de ausencia y qué implican.
3. Estrategias de tratamiento: desde eliminación controlada hasta imputación simple, imputación múltiple (MICE) y enfoques basados en Machine Learning (missForest), con sus ventajas, limitaciones y cuándo usar cada una.

#### <mark style="color:orange;">Ejemplo</mark>

Imaginemos una tabla con información de estudiantes:

| ID | Edad   | Nota examen | Ciudad  |
| -- | ------ | ----------- | ------- |
| 1  | 20     | 8.5         | Madrid  |
| 2  | 22     | _`NA`_      | Madrid  |
| 3  | _`NA`_ | 7.0         | Sevilla |
| 4  | 21     | 9.2         | _`NA`_  |

En esta pequeña base de datos aparecen diferentes **valores perdidos** (`NA`):

* El estudiante 2 no tiene registrada la nota del examen (quizá solo figuran los aprobados).
* Del estudiante 3 no se conoce la edad (puede que no la facilitara en el formulario).
* En el caso del estudiante 4 falta el dato de la ciudad (podría ser un error en la recogida).

Aunque son pocos valores, si esta situación se repite a gran escala puede dificultar el análisis:

* ¿Cómo calcular la nota media de la clase si el que no ha aprobado no tiene nota?
* ¿Podemos analizar la distribución de edades si algunas no están presentes? Imagina que no está porque es una persona mayor que no ha querido facilitar su edad
* ¿Qué ocurre si queremos estudiar el rendimiento por ciudad y hay valores ausentes?&#x20;

Este ejemplo refleja la necesidad de **detectar, comprender y tratar los valores perdidos** antes de avanzar en cualquier análisis o modelado estadístico.
