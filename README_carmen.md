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

### APLICACIÓN DE ESTOS SEGMENTOS A LA TASA DE RETENCIÓN

```python
import pandas as pd

subdatasets = {
    f"Repayment_{status}": df_group 
    for status, df_group in df.groupby('Repayment_Status')
}

print(f"Se crearon {len(subdatasets)} subdatasets.")

print(subdatasets.keys())
```
output
Se crearon 3 subdatasets.
dict_keys(['Repayment_Defaulter', 'Repayment_Late Payer', 'Repayment_On-time Payer'])

```python
# Diccionario para almacenar la retención de cada subdataset
sub_retention = {}

# Procesar cada subdataset
for key, df in subdatasets.items():
    print(f" Procesando subdataset: {key}")

    # Asegurar que 'created_at' es datetime y eliminar zona horaria
    df['created_at'] = pd.to_datetime(df['created_at'])
    df['created_at'] = df['created_at'].dt.tz_localize(None)

    # Verificar si 'deleted_account_id' existe antes de usarlo
    if 'deleted_account_id' in df.columns:
        df['user_id'] = df['user_id'].combine_first(df['deleted_account_id'])
        df.drop(columns=['deleted_account_id'], inplace=True)

    # Convertir a periodos diarios
    df['Day'] = df['created_at'].dt.to_period('D')

    # Calcular la cohorte diaria
    df['cohort_d'] = df.groupby('user_id')['Day'].transform('min')

    # Contar clientes iniciales por cohorte
    cohort_daily_initial_sizes = df.groupby('cohort_d').agg(
        Number_of_initial_customers=('user_id', 'nunique')
    ).reset_index()

    # Contar usuarios activos por cohorte y día
    cohort_daily_sizes = df.groupby(['cohort_d', 'Day']).agg(
        Number_of_customers=('user_id', 'nunique')
    ).reset_index()

    # Convertir 'Day' a datetime
    cohort_daily_sizes['Day'] = cohort_daily_sizes['Day'].astype(str)
    cohort_daily_sizes['Day'] = pd.to_datetime(cohort_daily_sizes['Day'])

    # Calcular tasa de retención diaria
    customers_daily_retention = cohort_daily_sizes.pivot(
        index='cohort_d', columns='Day', values='Number_of_customers'
    )

    # Dividir por número inicial de clientes en cada cohorte
    customers_daily_retention = customers_daily_retention.div(
        cohort_daily_initial_sizes.set_index('cohort_d')['Number_of_initial_customers'], axis=0
    )

    # Convertir tabla a formato largo
    df_churn = customers_daily_retention.reset_index().melt(
        id_vars=['cohort_d'], var_name='Day', value_name='Retention_Rate'
    )

    # Asegurar que 'Day' es datetime
    df_churn['Day'] = pd.to_datetime(df_churn['Day'])

    # Calcular diferencia en días desde cohort_d hasta Day
    df_churn['cohort_d'] = df_churn['cohort_d'].astype(str)  
    df_churn['cohort_d'] = pd.to_datetime(df_churn['cohort_d'])
    df_churn['Day_Num'] = (df_churn['Day'] - df_churn['cohort_d']).dt.days

    # Almacenar el resultado en un diccionario
    sub_retention[key] = df_churn

    print(f"Procesamiento de {key} completado.\n")

# Mostrar los subdatasets procesados
print(" Subdatasets procesados:", sub_retention.keys())
```

```python

warnings.filterwarnings('ignore')

for key, df_churn in sub_retention.items():
    print(f" Generando gráfico de dispersión para: {key}")


    df_churn["cohort_d"] = pd.to_datetime(df_churn["cohort_d"])


    df_daily_mean = df_churn.groupby(pd.Grouper(key="cohort_d", freq="D")).agg(
        Mean_Retention_Rate=("Retention_Rate", "mean")
    ).reset_index()


    plt.figure(figsize=(15, 6))
    plt.scatter(df_daily_mean["cohort_d"], df_daily_mean["Mean_Retention_Rate"], alpha=0.7, color="blue")

    plt.title(f"Daily Retention Rate - {key}")
    plt.xlabel("Cohort Date (YYYY-MM-DD)")
    plt.ylabel("Average Retention Rate")
    plt.xticks(rotation=90, fontsize=8)
    plt.grid(alpha=0.3)
    plt.show()

    print(f" Gráfico generado para: {key}\n")
```
#### OUTPUT
![Image](https://github.com/user-attachments/assets/7436a227-dfb8-43a6-927b-fe587a2c5482)
![Image](https://github.com/user-attachments/assets/f38167ff-3acf-4ffe-8d57-7da9b630ffe3)
![Image](https://github.com/user-attachments/assets/6ebdca7e-4af5-4662-9d76-82e7088dc26a)




