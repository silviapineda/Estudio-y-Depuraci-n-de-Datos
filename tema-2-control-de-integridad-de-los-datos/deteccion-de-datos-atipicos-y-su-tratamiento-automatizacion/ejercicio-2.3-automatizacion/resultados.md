---
hidden: true
---

# Resultados

```r

extreme <- function(data, variable) {
  var_name <- data[[variable]]  # Acceder a la variable por nombre
  
  tot <- sum(!is.na(var_name))  # Total de observaciones no NA
  na1 <- sum(is.na(var_name))   # NA iniciales
  
  # Identificar outliers usando la regla de Tukey (coef = 1.5)
  stats <- boxplot.stats(var_name, coef = 3)
  extreme_values <- stats$out  
  prop_outliers <- round(length(extreme_values) / tot * 100, 2)  # Proporción de outliers
  
  ##Datos extremos para dibujar en boxplot
  data_extreme <- data.frame(value = var_name) %>%
    filter(value %in% extreme_values)
  
  # Histograma con todos los datos
  p1 <- ggplot(data.frame(value = var_name), aes(x = value)) +
    geom_histogram(fill = "steelblue", color = "black", bins = 30, alpha = 0.7) +
    labs(title = "All Observations", x = variable, y = "Count") +
    theme_minimal()
  
  
  # Boxplot con outliers resaltados
  p2 <- ggplot(data.frame(value = var_name), aes(x = value)) +
    geom_boxplot() +
    geom_point(data = data_extreme, 
               aes(x = value, y = 0), color = "red", size = 3) +  # Solo outliers extremos en rojo
    labs(title = "Boxplot with extreme values", x = variable, y = "") +
    theme_minimal()
  
  # Mostrar gráficos juntos con patchwork
  print(p1 / p2)
  
  
  # Mostrar información en la consola
  cat("\n📌 Extremes identified in", variable, ": ", length(extreme_values), "extreme values\n")
  cat("📊 Proportion (%) of extreme values:", prop_outliers, "%\n")
  
  return(extreme_values)  # Devolver los valores outliers sin modificar los datos
}
```

{% file src="../../../.gitbook/assets/Ejercicio2.3 (3).pdf" %}
