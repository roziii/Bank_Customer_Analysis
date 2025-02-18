## Modelos de Regresión

- **Resultados comparativos**: Se evaluarán diferentes modelos de regresión (por ejemplo, regresión lineal, regresión polinómica o métodos basados en árboles) para determinar cuál ofrece el mejor desempeño de acuerdo con métricas como RMSE, MAE o R².

- **Justificación de hiperparámetros**: Se documentará el proceso de búsqueda y optimización de hiperparámetros (Grid Search, Random Search, Bayesian Optimization, etc.) junto a los criterios para seleccionar la mejor configuración.

- **Análisis de residuos**: Se presentará un estudio detallado de los residuos para identificar sesgos en las predicciones, zonas de sobreajuste o bajoajuste y pautas de comportamiento inesperadas.


## Análisis de Causalidad

- **Conclusiones basadas en cohortes**: Se crearán y estudiarán cohortes de usuarios (ej. por fecha de primer adelanto de efectivo, categoría de riesgo, etc.) para aislar variables que puedan tener un impacto significativo en la métrica de interés.

- **Evidencia estadística**: A partir de correlaciones observadas en los datos, se utilizarán técnicas (p. ej., análisis de diferencias en diferencias, métodos de control sintético o validación cruzada con cohortes) para aportar pruebas que respalden o descarten relaciones de causalidad.

- ## Análisis Relacion TASA DE PARO Y TASA DE RETENCIÓN
### CREACION SUBGRUPOS DE DATOS

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Identificar columnas categóricas
categorical_columns = df.select_dtypes(include=['object']).columns

# Eliminar filas con NaN en columnas categóricas para análisis
df_categorical = df[categorical_columns].dropna()

# Crear una tabla de contingencia entre 'Repayment_Status' y 'fees_rejection_segment'
contingency_table = pd.crosstab(df_categorical['Repayment_Status'], df_categorical['fees_rejection_segment'])
# Convertir a porcentaje por fila
cohort_table_percentage = contingency_table.div(contingency_table.sum(axis=1), axis=0) * 100

# Graficar la tabla de contingencia con un heatmap
plt.figure(figsize=(10, 6))
sns.heatmap(cohort_table_percentage, annot=True, fmt=".1f", cmap="Blues", linewidths=0.5)
plt.title("Relación entre Repayment_Status y fees_rejection_segment")
plt.xlabel("fees_rejection_segment")
plt.ylabel("Repayment_Status")
plt.xticks(rotation=45)
plt.yticks(rotation=0)
plt.show()
```

![Image](https://github.com/user-attachments/assets/ac175d6c-6023-43f2-8cc5-b14bf327062d)


```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns



# Convertir las columnas de fechas a formato datetime
df['cohort_d'] = pd.to_datetime(df['cohort_d'], errors='coerce')
df['Cohort'] = pd.to_datetime(df['Cohort'], errors='coerce')

# Crear una tabla de cohortes para analizar "Repayment_Status" en función del mes de cohorte
cohort_table = pd.crosstab(df['Cohort'].dt.to_period('M'), df['fees_rejection_segment'])
# Convertir a porcentaje por fila
cohort_table_percentage = cohort_table.div(cohort_table.sum(axis=1), axis=0) * 100
# Visualizar la evolución de cohortes con un heatmap
plt.figure(figsize=(12, 6))
sns.heatmap(cohort_table_percentage, cmap="Blues", annot=True, fmt=".1f", linewidths=0.5)
plt.title("Análisis de Cohortes: Fees rejection segment por Mes de Cohorte")
plt.xlabel("Repayment Status")
plt.ylabel("Cohort Month")
plt.yticks(rotation=0)
plt.xticks(rotation=45)
plt.show()
```
![Image](https://github.com/user-attachments/assets/6be1871f-01d8-45e8-bb94-83df9b83b07d)

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Convertir las columnas de fechas a formato datetime
df['cohort_d'] = pd.to_datetime(df['cohort_d'], errors='coerce')
df['Cohort'] = pd.to_datetime(df['Cohort'], errors='coerce')

# Crear una tabla de cohortes para analizar "Repayment_Status" en función del mes de cohorte
cohort_table = pd.crosstab(df['Cohort'].dt.to_period('M'), df['Repayment_Status'])

# Convertir a porcentaje por fila
cohort_table_percentage = cohort_table.div(cohort_table.sum(axis=1), axis=0) * 100

# Visualizar la evolución de cohortes con un heatmap en porcentajes
plt.figure(figsize=(12, 6))
sns.heatmap(cohort_table_percentage, cmap="Blues", annot=True, fmt=".1f", linewidths=0.5)  # Cambio de "d" a ".1f"
plt.title("Análisis de Cohortes: Repayment Status por Mes de Cohorte (%)")
plt.xlabel("Repayment Status")
plt.ylabel("Cohort Month")
plt.yticks(rotation=0)
plt.xticks(rotation=45)
plt.show()
```
![Image](https://github.com/user-attachments/assets/812c3153-30a8-4720-b8d8-605ae90c28e9)

```python
import matplotlib.pyplot as plt


cohort_table_percentage.index = cohort_table_percentage.index.astype(str)


plt.figure(figsize=(12, 6))
cohort_table_percentage.plot(marker='o')
plt.title("Evolución de Repayment Status por Mes de Cohorte (%)")
plt.xlabel("Cohort Month")
plt.ylabel("Porcentaje (%)")
plt.xticks(rotation=45)
plt.legend(title="Repayment Status")
plt.tight_layout()
plt.show()
```
![Image](https://github.com/user-attachments/assets/bb6c48f0-5c30-4e7c-bc4c-774d6cfabbaf)

