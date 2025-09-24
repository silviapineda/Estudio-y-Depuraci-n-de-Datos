# Reusltados

* Identifica los duplicados en toda las fila

```r
df[duplicated(df), ]
```

* Identifica los IDs duplicados

```r
df$ID[duplicated(df$ID)]
```

* Elimina los duplicados manteniendo la primera observación que aparece

```r
df_sin_duplicados<-distinct(df)
```

