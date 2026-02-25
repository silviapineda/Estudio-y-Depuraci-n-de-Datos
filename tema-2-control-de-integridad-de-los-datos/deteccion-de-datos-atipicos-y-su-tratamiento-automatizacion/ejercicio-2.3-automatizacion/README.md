# Ejercicio 2.3: automatización

Modifica la función <mark style="color:green;">**`outliers()`**</mark>para detectar de forma rápida los outliers y modifícala para que detecte datos extremos, la puedes llamar <mark style="color:green;">**`extreme().`**</mark>

Después úsa ambas funciones para detectar los datos atípicos y extremos en el ejercicio 2.2 con la base de datos <mark style="color:orange;">**`ozone`**</mark>.&#x20;

Ten en cuenta que para que solo pinte en rojo los extremos hay que hacer un cálculo manual y modificar el ggplot. Ayudate de lo siguiente:

```r
##Datos extremos para dibujar en boxplot
data_extreme <- data.frame(value = var_name) %>%
    filter(value %in% extreme_values)
  
    
ggplot(data.frame(value = var_name), aes(x = value)) +
  geom_boxplot() +
  geom_point(data = data_extreme, 
               aes(x = value, y = 0), color = "red", size = 3) +  # Solo outliers extremos en rojo
   labs(title = "Boxplot with extreme values", x = variable, y = "") +
   theme_minimal()
```

