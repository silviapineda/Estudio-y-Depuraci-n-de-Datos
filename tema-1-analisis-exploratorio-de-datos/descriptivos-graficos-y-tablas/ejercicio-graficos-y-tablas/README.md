# Ejercicio: Gráficos y Tablas

Vamos a trabajar sobre la base de datos <mark style="color:orange;">**estres\_corregido.csv**</mark> que ya hemos corregido previamente. Para leer un csv con la modificación de los factores que ya hicimos hay que añadirle el argumento **`stringsAsFactors`**

{% file src="../../../.gitbook/assets/estres_corregido.csv" %}

<pre class="language-r"><code class="lang-r"><strong>datos&#x3C;-read.csv("estres_corregido.csv",stringsAsFactors = TRUE)
</strong></code></pre>

Vamos a tratar las variables **cualitativas** (factores en R) y **cuantitativas** (num o int en R)  por separado. Recuerda que en el ejercicio anterior ya las hemos clasificado:

1. Haz un histograma de las variables cuantitativas y saca los estadísticos de las mismas. **Comenta los resultados**. ¿Representarías algunas de estas variables con un diagrama de barras? Hazlo si la respuesta es Si.&#x20;
2. Haz un gráfico para las variables cualitativas. **Comenta los resultados**
3. ¿Están equilibradas las categorías de las variables cualitativas? Ayudate de los gráficos anteriores y haz unas  tablas de frecuencias relativas para contestar.
4. Representa en un gráfico la variable salario por genero y edad por genero, luego haz los estadísticos usando <mark style="color:green;">**`describeBy().`**</mark>**&#x20;¿Quién gana más dinero? ¿quién es más mayor?**



