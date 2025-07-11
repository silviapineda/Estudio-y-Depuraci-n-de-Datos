---
cover: >-
  ../../.gitbook/assets/Introducing-Automated-Time-Series-Anomaly-Detection_blog_Image_v.1.0.webp
coverY: 0
---

# Detección de datos atípicos y su tratamiento (automatización)

Si tenemos que comprobar varias variables de una misma base de datos podemos pensar en automatizar el proceso mediante el uso de alguna función propia.&#x20;

Esta sería la función propia <mark style="color:green;">**`outliers()`**</mark> que la guarderemos en un **`Funciones_propias.R`**

```r
library(ggplot2)
library(dplyr)
library(patchwork)  # Para mostrar gráficos juntos

outliers <- function(data, variable) {
  var_name <- data[[variable]]  # Acceder a la variable por nombre
  
  tot <- sum(!is.na(var_name))  # Total de observaciones no NA
  na1 <- sum(is.na(var_name))   # NA iniciales
  
  # Identificar outliers usando la regla de Tukey (coef = 1.5)
  stats <- boxplot.stats(var_name, coef = 1.5)
  outlier_values <- stats$out  
  prop_outliers <- round(length(outlier_values) / tot * 100, 2)  # Proporción de outliers
  
  # Filtrar datos sin outliers
  data_no_outliers <- data.frame(value = var_name) %>%
    filter(!(value %in% outlier_values))
  
  # Histograma con todos los datos
  p1 <- ggplot(data.frame(value = var_name), aes(x = value)) +
    geom_histogram(fill = "steelblue", color = "black", bins = 30, alpha = 0.7) +
    labs(title = "All Observations", x = variable, y = "Count") +
    theme_minimal()
  
  # Boxplot con outliers resaltados
  p2 <- ggplot(data.frame(value = var_name), aes(x = value)) +
    geom_boxplot(outlier.colour = "red", outlier.shape = 16, outlier.size = 3) +
    labs(title = "Boxplot with Outliers", x = variable, y = "") +
    theme_minimal()
  
  # Mostrar gráficos juntos con patchwork
  print(p1 / p2)
  
  
  # Mostrar información en la consola
  cat("\n📌 Outliers identified in", variable, ": ", length(outlier_values), "outliers\n")
  cat("📊 Proportion (%) of outliers:", prop_outliers, "%\n")
  
  return(outlier_values)  # Devolver los valores outliers sin modificar los datos
}



```

Y para usarla sería como una función cualquiera de R:

```r
source("Funciones_propias .R")

outliers(data,"Pressure_height")

# Aplicar la función a múltiples variables numéricas o enteras
numeric_integer_vars <- names(which(sapply(data, is.numeric) | sapply(data, is.integer)))
# Aplicar la función 'outliers' a cada una de las variables numéricas
outliers_results <- lapply(numeric_integer_vars, function(var) {
  outliers(data, var)  # Llamar a la función pasando el nombre de la variable
})

```



