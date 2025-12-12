---
cover: ../.gitbook/assets/image (112).png
coverY: 0
---

# Introducción

Antes de aplicar cualquier técnica estadística o de ciencia de datos, es imprescindible entender qué contienen los datos y en qué estado están. En la práctica, los conjuntos de datos rara vez llegan “listos para analizar”: pueden tener nombres de variables inconsistentes, tipos mal declarados, codificaciones poco claras o valores especiales que en realidad representan datos ausentes. Si no detectamos estos problemas al principio, es fácil acabar con conclusiones erróneas o con horas perdidas depurando fallos que podrían haberse evitado.

El análisis exploratorio de datos es la etapa en la que describimos la estructura de los datos, revisamos la calidad y buscamos errores iniciales, patrones "raros" o relaciones relevantes. Además, en esta fase se toman decisiones clave (qué es una variable categórica vs. numérica, cómo interpretar códigos, qué valores son imposibles, etc.) y a dejarlo documentado de forma reproducible.

En este tema nos centraremos en:

1. Identificar y declarar correctamente  los tipos de variables (cuantitativas/cualitativas, fechas, etc.).
2. Revisar nombres, codificaciones y transformaciones necesarias para un análisis correcto.
3. Detectar errores frecuentes de lectura (valores “missing” mal representados, conversiones que generan _warnings_, etc.).
4. Obtener una primera fotografía del dataset con resúmenes y visualizaciones que guíen los siguientes pasos.
