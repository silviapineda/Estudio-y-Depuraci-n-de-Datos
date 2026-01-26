# Ejercicio 1.1: Tipos de Datos

Con el fichero de datos <mark style="color:orange;">**TiposDatos.csv**</mark> que hemos leido en este tema:

{% file src="../../../.gitbook/assets/TiposDatos.csv" %}

Realizar los siguientes ejercicios.

Antes de empezar, inicia un fichero quarto para renderizar en formato HTML. Subiremos al campus el fichero renderizado al terminar la clase.

<figure><img src="../../../.gitbook/assets/image (304).png" alt="" width="563"><figcaption></figcaption></figure>

Para que me puedas enviar solo el fichero que se genera HTML y aparezcan todos los gráficos y tablas, debes poner `embed-resources: true` y si quieres que aparezca un índice que ayuda a leer el fichero puedes poner `toc: TRUE`. Quedaría así la cabecera:

```r
---
title: "Ejercicio 1.1: Tipos de Datos"
format: 
  html:
    embed-resources: true
    toc: TRUE
    toc-title: Índice
    toc-location: right
editor: visual
author: Silvia Pineda
---
```

Además en cada chunk de código puedes poner la opción <mark style="color:green;">**`#| eval: true`**</mark> o <mark style="color:green;">**`#| eval: false`**</mark>, con la primera el código se ejecuta y saldrá su ejecución en el HTML, con la opción (false), el código no se ejecuta, esto te sirve para que se pueda ejecutar el HTML, aunque un chunk no funcione.

**Ahora ya puedes realizar los siguientes ejercicios:**

1. Lee el fichero y clasifica las variables en cuantitativas y cualitativas.
2. Mira si todos los tipos de variables están bien declarados y sino declaralos bien.&#x20;
3. Averigua como se llaman los individuos que están solteros.
4. Calcula la edad de los individuos.
