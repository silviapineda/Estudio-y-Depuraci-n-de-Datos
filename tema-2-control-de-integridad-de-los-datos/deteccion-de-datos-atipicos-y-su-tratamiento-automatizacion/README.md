---
cover: >-
  ../../.gitbook/assets/Introducing-Automated-Time-Series-Anomaly-Detection_blog_Image_v.1.0.webp
coverY: 0
---

# Detección de datos atípicos y su tratamiento (automatización)

Si tenemos que comprobar varias variables de una misma base de datos podemos pensar en automatizar el proceso mediante el uso de alguna función propia que puede ejecutar el estudio univariante de forma automática.&#x20;

En este ejemplo vemos como sería la función propia <mark style="color:green;">**`outliers()`**</mark> que la guarderemos en un fichero como **`Funciones_propias.R`**

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
source("Funciones_propias.R")

outliers(data,"Pressure_height")

# 1. Obtenemos los nombres de las columnas numéricas
numeric_vars <- names(data)[sapply(data, is.numeric)]

# 2. Usamos una función anónima para pasar 'data' y el 'nombre'
outliers_results <- lapply(numeric_vars, function(v) outliers(data, v))

```

Podemos tener una función que nos saque ambas cosas a la vez

```r
outliers_extreme <- function(data, variable) {
  var_name <- data[[variable]]  # Acceder a la variable por nombre
  
  tot <- sum(!is.na(var_name))  # Total de observaciones no NA
  na1 <- sum(is.na(var_name))   # NA iniciales
  
  # Identificar outliers usando la regla de Tukey (coef = 1.5)
  stats1 <- boxplot.stats(var_name, coef = 1.5)
  outlier_values <- stats1$out  
  prop_outliers <- round(length(outlier_values) / tot * 100, 2)  # Proporción de outliers
  
  # Identificar extremos usando la regla de Tukey (coef = 3)
  stats2 <- boxplot.stats(var_name, coef = 3)
  extreme_values <- stats2$out  
  prop_extreme <- round(length(extreme_values) / tot * 100, 2)  # Proporción de extremos
  
  # Preparar dataframe para los gráficos y clasificar los puntos
  df_plot <- data.frame(value = var_name)
  df_plot$tipo <- "Normal"
  
  # Usamos los bigotes de stats1 (1.5 IQR) y stats2 (3 IQR) para clasificar con precisión
  df_plot$tipo[df_plot$value < stats1$stats[1] | df_plot$value > stats1$stats[5]] <- "Atípico"
  df_plot$tipo[df_plot$value < stats2$stats[1] | df_plot$value > stats2$stats[5]] <- "Extremo"
  
  # Extraemos solo los puntos anómalos para pintarlos por encima en p2
  df_anomalos <- subset(df_plot, tipo != "Normal")
  
  # 1. Histograma con todos los datos
  p1 <- ggplot(df_plot, aes(x = value)) +
    geom_histogram(fill = "steelblue", color = "black", bins = 30, alpha = 0.7) +
    labs(title = paste("All Observations:", variable), x = variable, y = "Count") +
    theme_minimal()
  
  # 2. Boxplot con outliers y extremos resaltados
  p2 <- ggplot(df_plot, aes(x = value, y = factor(1))) +
    # Boxplot base 
    geom_boxplot(outlier.shape = NA, fill = "white", color = "black") +
    
    # Añadimos nuestros puntos con colores. 
    # show.legend = FALSE evita que se cree la leyenda en el gráfico
    geom_point(data = df_anomalos, aes(color = tipo), size = 3.5, shape = 16, show.legend = FALSE) +
    
    scale_color_manual(values = c("Atípico" = "orange", "Extremo" = "red")) +
    labs(title = "Boxplot with Outliers & Extremes", x = variable, y = "") +
    theme_minimal() +
    
    # Limpiamos el eje Y y nos aseguramos de que no haya ninguna leyenda
    theme(axis.text.y = element_blank(), 
          axis.ticks.y = element_blank(),
          panel.grid.major.y = element_blank(),
          panel.grid.minor.y = element_blank(),
          legend.position = "none") #
  
  # Mostrar gráficos juntos con patchwork
  print(p1 / p2)
  
  # Mostrar información en la consola
  cat("\n🟠 Atípicos (Outliers) en", variable, ": ", length(outlier_values), "\n")
  cat("   🔸 Proporción (%):", prop_outliers, "%\n")
  
  cat("\n🔴 Extremos (Extreme values) en", variable, ": ", length(extreme_values), "\n")
  cat("   🔻 Proporción (%):", prop_extreme, "%\n")
  
  return(outlier_values)  # Devolver los valores outliers sin modificar los datos
}
```

```r
source("Funciones_propias.R")

# 1. Obtenemos los nombres de las columnas numéricas
numeric_vars <- names(data)[sapply(data, is.numeric)]

# 2. Usamos una función anónima para pasar 'data' y el 'nombre'
outliers_results <- lapply(numeric_vars, function(v) outliers_extreme(data, v))
```
