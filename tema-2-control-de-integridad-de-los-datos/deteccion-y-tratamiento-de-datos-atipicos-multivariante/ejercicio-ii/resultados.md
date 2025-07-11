# Resultados

```r
library(dbscan)
library(class)
library(ggplot2)
library(tidyverse)

data <- read.csv("ozone.csv")  # import data
data$Month<-as.factor(data$Month)
data$Day_of_month<-as.factor(data$Day_of_month)
data$Day_of_week<-as.factor(data$Day_of_week)


####Aplicamos LOF
k<-round(log(nrow(data)))
lof<-lof(select(data,-Month,-Day_of_month,-Day_of_week,-Inversion_base_height),minPts = k) 
cbind(data[lof>1.5,],lof[lof>1.5])

data$lof<-lof

ggplot(data, aes(y = lof)) +
  geom_boxplot(fill = "skyblue", outlier.color = "red", outlier.shape = 16) +
  theme_minimal() +
  labs(title = "Distribución de LOF Scores")

####Comprobamos las cuantitativas
ggplot(data, aes(x = Pressure_height, y = Ozone_reading, colour = lof)) +
  geom_point() +
  scale_color_gradient(low = "blue", high = "red", name = "LOF Score") +
  labs(title = "Detección de Valores Atípicos con LOF")


ggplot(data, aes(x = Pressure_height, y = Wind_speed, colour = lof)) +
  geom_point() +
  scale_color_gradient(low = "blue", high = "red", name = "LOF Score") +
  labs(title = "Detección de Valores Atípicos con LOF")


ggplot(data, aes(x = Pressure_height, y = Visibility, colour = lof)) +
  geom_point() +
  scale_color_gradient(low = "blue", high = "red", name = "LOF Score") +
  labs(title = "Detección de Valores Atípicos con LOF")


ggplot(data, aes(x = Pressure_height, y = Humidity, colour = lof)) +
  geom_point() +
  scale_color_gradient(low = "blue", high = "red", name = "LOF Score") +
  labs(title = "Detección de Valores Atípicos con LOF")


ggplot(data, aes(x = Pressure_height, y = Temperature_Sandburg, colour = lof)) +
  geom_point() +
  scale_color_gradient(low = "blue", high = "red", name = "LOF Score") +
  labs(title = "Detección de Valores Atípicos con LOF")

ggplot(data, aes(x = Pressure_height, y = Inversion_base_height, colour = lof)) +
  geom_point() +
  scale_color_gradient(low = "blue", high = "red", name = "LOF Score") +
  labs(title = "Detección de Valores Atípicos con LOF")


####Comprobamos las cualitativas
ggplot(data, aes(x = as.factor(Month), y = lof)) +
  geom_boxplot(fill = "lightblue") +
  labs(title = "lof across months", x = "Month", y = "lof") 

ggplot(data, aes(x = as.factor(Day_of_week), y = lof)) +
  geom_boxplot(fill = "lightgreen") +
  labs(title = "lof across day of week", x = "Day_of_week", y = "lof") 





```

No observamos ninguna observación que tenga un LOF especialmente grande más allá de que los puntos rojos que son los que mayor LOF tienen siempre corresponden a los puntos más alejados de las nubes de puntos al representar las variables dos a dos que corresponden a los datos atípicos que ya hemos observado en el estudio previo. No borraremos nada más.

