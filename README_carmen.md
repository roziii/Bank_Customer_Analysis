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

## APLICAR MODELO REGRESIÓN POLINÓMICA

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from sklearn.preprocessing import PolynomialFeatures, StandardScaler
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error, r2_score
from sklearn.model_selection import train_test_split
import warnings

warnings.filterwarnings('ignore')

# Función para aplicar regresión polinómica a un subdataset
def apply_regression(df_retention, title):
    print(f"Evaluando regresión para: {title}")

    # Convertir cohort_d a datetime
    df_retention["cohort_d"] = pd.to_datetime(df_retention["cohort_d"])

    # Agrupar por cohorte diaria y calcular la media de la Retention Rate
    df_daily_mean = df_retention.groupby(pd.Grouper(key="cohort_d", freq="D")).agg(
        Mean_Retention_Rate=("Retention_Rate", "mean")
    ).reset_index()

    # Eliminar valores NaN
    df_daily_mean = df_daily_mean.dropna(subset=["Mean_Retention_Rate"])

    # Convertir la fecha a un número ordinal
    df_daily_mean["Date_Num"] = df_daily_mean["cohort_d"].map(pd.Timestamp.toordinal)

    # Determinar el origen dinámico basado en el primer día de los datos
    origin_date = df_daily_mean["cohort_d"].min()

    # Separar en entrenamiento y prueba
    X = df_daily_mean[['Date_Num']].values
    y = df_daily_mean['Mean_Retention_Rate'].values

    if len(X) < 10:  # Si hay muy pocos datos, evitar errores
        print(f" {title} tiene muy pocos datos para aplicar regresión.")
        return

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

    # Generar un rango continuo de fechas para la predicción
    date_range = np.linspace(df_daily_mean['Date_Num'].min(), df_daily_mean['Date_Num'].max(), 500).reshape(-1, 1)

    # Extender fechas para predicciones futuras
    additional_days = 90  # Extender predicción por 90 días
    future_dates = np.linspace(df_daily_mean['Date_Num'].max() + 1, df_daily_mean['Date_Num'].max() + additional_days, 1000).reshape(-1, 1)

    results = []

    for degree in [3, 5, 7, 9, 10, 11]:
     
        scaler = StandardScaler()
        X_train_scaled = scaler.fit_transform(X_train)
        X_test_scaled = scaler.transform(X_test)
        X_scaled = scaler.transform(X)
        date_range_scaled = scaler.transform(date_range)
        future_dates_scaled = scaler.transform(future_dates)

        # Transformar las fechas en base al modelo polinómico
        poly = PolynomialFeatures(degree=degree)
        X_train_poly = poly.fit_transform(X_train_scaled)
        X_test_poly = poly.transform(X_test_scaled)
        X_poly = poly.transform(X_scaled)
        date_range_poly = poly.transform(date_range_scaled)
        future_dates_poly = poly.transform(future_dates_scaled)

        # Entrenar el modelo de regresión polinómica
        model = LinearRegression()
        model.fit(X_train_poly, y_train)

        # Predicción en entrenamiento y test
        y_train_pred = model.predict(X_train_poly)
        y_test_pred = model.predict(X_test_poly)
        y_pred_range = model.predict(date_range_poly)
        y_future_pred = model.predict(future_dates_poly)

        # Evaluación
        rmse_train = np.sqrt(mean_squared_error(y_train, y_train_pred))
        r2_train = r2_score(y_train, y_train_pred)
        rmse_test = np.sqrt(mean_squared_error(y_test, y_test_pred))
        r2_test = r2_score(y_test, y_test_pred)
        r2_diff = abs(r2_train - r2_test)

        results.append((degree, rmse_train, r2_train, rmse_test, r2_test, r2_diff, y_pred_range, y_future_pred))     
        print(f"Grado {degree} - RMSE Train: {rmse_train:.4f}, R² Train: {r2_train:.4f}, RMSE Test: {rmse_test:.4f}, R² Test: {r2_test:.4f}, Diferencia R²: {r2_diff:.4f}")

    # Seleccionar el modelo con el mejor R² en test
    best_model = max(results, key=lambda x: x[4])  # Selecciona el mayor R² Test
    best_degree, _, _, _, best_r2_test, _, best_y_pred_range, best_y_future_pred = best_model

    print(f" Mejor modelo: Grado {best_degree} - R² Test: {best_r2_test:.4f}")

    # Crear gráfico con SOLO el mejor modelo
    plt.figure(figsize=(12, 6))
    plt.scatter(df_daily_mean["cohort_d"], df_daily_mean["Mean_Retention_Rate"], alpha=0.7, label="Datos Reales", color="blue")

    # Graficar la mejor curva de ajuste
    plt.plot([origin_date + pd.Timedelta(days=int(num - df_daily_mean['Date_Num'].min())) for num in date_range.flatten()], 
             best_y_pred_range, label=f'Mejor Modelo (Grado {best_degree})', linewidth=2, color="green")

    # Graficar la mejor extrapolación futura
    plt.plot([origin_date + pd.Timedelta(days=int(num - df_daily_mean['Date_Num'].min())) for num in future_dates.flatten()], 
             best_y_future_pred, linestyle='dashed', color="red", label=f'Predicción Futura (Grado {best_degree})')

    # Configurar el gráfico
    plt.title(f'Regresión Polinómica - {title} (Mejor Modelo: {best_degree})', fontsize=16)
    plt.xlabel('Fecha de Cohorte', fontsize=14)
    plt.ylabel('Retention Rate Promedio', fontsize=14)
    plt.grid(alpha=0.3)
    plt.xticks(rotation=45)
    plt.yscale('log')
    plt.legend(loc='upper right')
    plt.tight_layout()
    plt.show()

# Aplicar regresión a todos los subdatasets en sub_retention
for key, df_churn in sub_retention.items():
    apply_regression(df_churn, f"Segmento - {key}")
```
#### OUTPUT
Evaluando regresión para: Segmento - Repayment_Defaulter
Grado 3 - RMSE Train: 0.2807, R² Train: 0.3060, RMSE Test: 0.2971, R² Test: 0.1921, Diferencia R²: 0.1138
Grado 5 - RMSE Train: 0.2760, R² Train: 0.3287, RMSE Test: 0.2952, R² Test: 0.2025, Diferencia R²: 0.1261
Grado 7 - RMSE Train: 0.2616, R² Train: 0.3970, RMSE Test: 0.2818, R² Test: 0.2733, Diferencia R²: 0.1236
Grado 9 - RMSE Train: 0.2520, R² Train: 0.4407, RMSE Test: 0.2634, R² Test: 0.3649, Diferencia R²: 0.0759
Grado 10 - RMSE Train: 0.2475, R² Train: 0.4601, RMSE Test: 0.2486, R² Test: 0.4343, Diferencia R²: 0.0258
Grado 11 - RMSE Train: 0.2475, R² Train: 0.4602, RMSE Test: 0.2491, R² Test: 0.4322, Diferencia R²: 0.0280
 Mejor modelo: Grado 10 - R² Test: 0.4343
 ![Image](https://github.com/user-attachments/assets/eb7c421c-ab50-43a8-a0f7-47e4d439d975)

 Evaluando regresión para: Segmento - Repayment_Late Payer
Grado 3 - RMSE Train: 0.2604, R² Train: 0.4256, RMSE Test: 0.2793, R² Test: 0.0614, Diferencia R²: 0.3642
Grado 5 - RMSE Train: 0.2520, R² Train: 0.4620, RMSE Test: 0.2761, R² Test: 0.0827, Diferencia R²: 0.3793
Grado 7 - RMSE Train: 0.2438, R² Train: 0.4967, RMSE Test: 0.2679, R² Test: 0.1366, Diferencia R²: 0.3601
Grado 9 - RMSE Train: 0.2336, R² Train: 0.5379, RMSE Test: 0.2596, R² Test: 0.1891, Diferencia R²: 0.3488
Grado 10 - RMSE Train: 0.2324, R² Train: 0.5424, RMSE Test: 0.2574, R² Test: 0.2032, Diferencia R²: 0.3392
Grado 11 - RMSE Train: 0.2298, R² Train: 0.5526, RMSE Test: 0.2526, R² Test: 0.2327, Diferencia R²: 0.3198
 Mejor modelo: Grado 11 - R² Test: 0.2327
![Image](https://github.com/user-attachments/assets/74dd2e22-f224-4474-9063-4ce63e9100ab)
Evaluando regresión para: Segmento - Repayment_On-time Payer
Grado 3 - RMSE Train: 0.2721, R² Train: 0.3299, RMSE Test: 0.2763, R² Test: 0.3384, Diferencia R²: 0.0085
Grado 5 - RMSE Train: 0.2567, R² Train: 0.4037, RMSE Test: 0.2687, R² Test: 0.3741, Diferencia R²: 0.0295
Grado 7 - RMSE Train: 0.2514, R² Train: 0.4277, RMSE Test: 0.2604, R² Test: 0.4121, Diferencia R²: 0.0156
Grado 9 - RMSE Train: 0.2415, R² Train: 0.4723, RMSE Test: 0.2470, R² Test: 0.4711, Diferencia R²: 0.0012
Grado 10 - RMSE Train: 0.2316, R² Train: 0.5144, RMSE Test: 0.2464, R² Test: 0.4737, Diferencia R²: 0.0408
Grado 11 - RMSE Train: 0.2293, R² Train: 0.5240, RMSE Test: 0.2495, R² Test: 0.4603, Diferencia R²: 0.0637
 Mejor modelo: Grado 10 - R² Test: 0.4737
 ![Image](https://github.com/user-attachments/assets/74dd2e22-f224-4474-9063-4ce63e9100ab)

 ## NUEVA SEGMENTACIÓN, ADAPTACION DEL CODIGO Y NUEVOS RESULTADOS
 Se crearon 6 subdatasets.
dict_keys(['Defaulter_activo', 'Defaulter_inactivo', 'Late Payer_activo', 'Late Payer_inactivo', 'On-time Payer_activo', 'On-time Payer_inactivo'])

Evaluando regresión para: Segmento - Defaulter_activo
Grado 3 - RMSE Train: 0.2762, R² Train: 0.2711, RMSE Test: 0.2761, R² Test: 0.3129, Diferencia R²: 0.0418
Grado 5 - RMSE Train: 0.2713, R² Train: 0.2970, RMSE Test: 0.2705, R² Test: 0.3406, Diferencia R²: 0.0436
Grado 7 - RMSE Train: 0.2523, R² Train: 0.3916, RMSE Test: 0.2460, R² Test: 0.4546, Diferencia R²: 0.0629
Grado 9 - RMSE Train: 0.2436, R² Train: 0.4332, RMSE Test: 0.2488, R² Test: 0.4420, Diferencia R²: 0.0088
Grado 10 - RMSE Train: 0.2379, R² Train: 0.4593, RMSE Test: 0.2494, R² Test: 0.4395, Diferencia R²: 0.0198
Grado 11 - RMSE Train: 0.2376, R² Train: 0.4608, RMSE Test: 0.2484, R² Test: 0.4437, Diferencia R²: 0.0171
Mejor modelo: Grado 7 - R² Test: 0.4546

![Image](https://github.com/user-attachments/assets/3798a4a2-be57-40c8-974d-c48f330639fe)

Evaluando regresión para: Segmento - Defaulter_inactivo
Grado 3 - RMSE Train: 0.2626, R² Train: 0.3061, RMSE Test: 0.3008, R² Test: 0.2297, Diferencia R²: 0.0764
Grado 5 - RMSE Train: 0.2544, R² Train: 0.3487, RMSE Test: 0.2802, R² Test: 0.3319, Diferencia R²: 0.0168
Grado 7 - RMSE Train: 0.2492, R² Train: 0.3750, RMSE Test: 0.2646, R² Test: 0.4042, Diferencia R²: 0.0292
Grado 9 - RMSE Train: 0.2408, R² Train: 0.4166, RMSE Test: 0.2423, R² Test: 0.5001, Diferencia R²: 0.0835
Grado 10 - RMSE Train: 0.2389, R² Train: 0.4258, RMSE Test: 0.2411, R² Test: 0.5051, Diferencia R²: 0.0793
Grado 11 - RMSE Train: 0.2362, R² Train: 0.4387, RMSE Test: 0.2464, R² Test: 0.4830, Diferencia R²: 0.0444
Mejor modelo: Grado 10 - R² Test: 0.5051

![image](https://github.com/user-attachments/assets/2c3a32fe-65f3-4a77-a031-79e32562d481)

Evaluando regresión para: Segmento - Late Payer_activo
Grado 3 - RMSE Train: 0.2347, R² Train: 0.3265, RMSE Test: 0.2557, R² Test: 0.2831, Diferencia R²: 0.0434
Grado 5 - RMSE Train: 0.2306, R² Train: 0.3496, RMSE Test: 0.2546, R² Test: 0.2897, Diferencia R²: 0.0599
Grado 7 - RMSE Train: 0.2173, R² Train: 0.4225, RMSE Test: 0.2595, R² Test: 0.2619, Diferencia R²: 0.1606
Grado 9 - RMSE Train: 0.2131, R² Train: 0.4449, RMSE Test: 0.2534, R² Test: 0.2960, Diferencia R²: 0.1489
Grado 10 - RMSE Train: 0.2088, R² Train: 0.4670, RMSE Test: 0.2697, R² Test: 0.2024, Diferencia R²: 0.2646
Grado 11 - RMSE Train: 0.2088, R² Train: 0.4670, RMSE Test: 0.2706, R² Test: 0.1974, Diferencia R²: 0.2697
Mejor modelo: Grado 9 - R² Test: 0.2960

![Image](https://github.com/user-attachments/assets/658196bb-c65a-4f63-909d-7e7f24426672)

Evaluando regresión para: Segmento - Late Payer_inactivo
Grado 3 - RMSE Train: 0.2626, R² Train: 0.3061, RMSE Test: 0.3008, R² Test: 0.2297, Diferencia R²: 0.0764
Grado 5 - RMSE Train: 0.2544, R² Train: 0.3487, RMSE Test: 0.2802, R² Test: 0.3319, Diferencia R²: 0.0168
Grado 7 - RMSE Train: 0.2492, R² Train: 0.3750, RMSE Test: 0.2646, R² Test: 0.4042, Diferencia R²: 0.0292
Grado 9 - RMSE Train: 0.2408, R² Train: 0.4166, RMSE Test: 0.2423, R² Test: 0.5001, Diferencia R²: 0.0835
Grado 10 - RMSE Train: 0.2389, R² Train: 0.4258, RMSE Test: 0.2411, R² Test: 0.5051, Diferencia R²: 0.0793
Grado 11 - RMSE Train: 0.2362, R² Train: 0.4387, RMSE Test: 0.2464, R² Test: 0.4830, Diferencia R²: 0.0444
Mejor modelo: Grado 10 - R² Test: 0.5051

![Image](https://github.com/user-attachments/assets/78d906e7-0b5d-45e7-b8b8-3fb9d3e86a54)

Evaluando regresión para: Segmento - On-time Payer_activo
Grado 3 - RMSE Train: 0.2214, R² Train: 0.1984, RMSE Test: 0.2380, R² Test: -0.0229, Diferencia R²: 0.2213
Grado 5 - RMSE Train: 0.2181, R² Train: 0.2226, RMSE Test: 0.2695, R² Test: -0.3114, Diferencia R²: 0.5341
Grado 7 - RMSE Train: 0.2142, R² Train: 0.2499, RMSE Test: 0.8897, R² Test: -13.2890, Diferencia R²: 13.5389
Grado 9 - RMSE Train: 0.2135, R² Train: 0.2550, RMSE Test: 0.2433, R² Test: -0.0685, Diferencia R²: 0.3235
Grado 10 - RMSE Train: 0.2124, R² Train: 0.2624, RMSE Test: 7.2672, R² Test: -952.3812, Diferencia R²: 952.6437
Grado 11 - RMSE Train: 0.2081, R² Train: 0.2920, RMSE Test: 70.6852, R² Test: -90194.2490, Diferencia R²: 90194.5409
Mejor modelo: Grado 3 - R² Test: -0.0229

![Image](https://github.com/user-attachments/assets/d1a688c2-a93a-4371-8ce2-6351178d3413)

Evaluando regresión para: Segmento - On-time Payer_inactivo
Grado 3 - RMSE Train: 0.2626, R² Train: 0.3061, RMSE Test: 0.3008, R² Test: 0.2297, Diferencia R²: 0.0764
Grado 5 - RMSE Train: 0.2544, R² Train: 0.3487, RMSE Test: 0.2802, R² Test: 0.3319, Diferencia R²: 0.0168
Grado 7 - RMSE Train: 0.2492, R² Train: 0.3750, RMSE Test: 0.2646, R² Test: 0.4042, Diferencia R²: 0.0292
Grado 9 - RMSE Train: 0.2408, R² Train: 0.4166, RMSE Test: 0.2423, R² Test: 0.5001, Diferencia R²: 0.0835
Grado 10 - RMSE Train: 0.2389, R² Train: 0.4258, RMSE Test: 0.2411, R² Test: 0.5051, Diferencia R²: 0.0793
Grado 11 - RMSE Train: 0.2362, R² Train: 0.4387, RMSE Test: 0.2464, R² Test: 0.4830, Diferencia R²: 0.0444
Mejor modelo: Grado 10 - R² Test: 0.5051

![Image](https://github.com/user-attachments/assets/50098236-9608-4c36-840c-08b9f5d08274)

![Image](https://github.com/user-attachments/assets/7cf5535e-0b5d-4cf0-b330-a613b9849425)


## QUE PUEDE ESTAR PASANDO AQUI?

Se observa claramente en todos los grupos una caida de la tasa de retención más o menos acusada en los mismos meses.

A que se puede deber esto?

En mayo de 2020 en Estados Unidos se vivieron momentos de gran transformación y tensión. Algunos de los hechos más relevantes fueron:

**Inicio de la reapertura tras el confinamiento**
Tras los estrictos cierres de marzo y abril para frenar la propagación del COVID-19, muchos estados comenzaron a flexibilizar las restricciones. Se implementaron medidas para reabrir negocios, restaurantes y otros servicios, aunque de forma muy cautelosa y con protocolos sanitarios, lo que generó incertidumbre respecto al riesgo de nuevos contagios.

**Tensiones en la gestión de la pandemia**
Mientras algunos estados impulsaban la reactivación económica, surgían debates intensos sobre la seguridad y la eficacia de estas medidas. La presión por equilibrar la salud pública con la necesidad de reactivar la economía se convirtió en un tema central en el discurso político y mediático.

**Protestas por la justicia racial**
El 25 de mayo de 2020, el trágico fallecimiento de George Floyd en Minneapolis desencadenó protestas masivas en todo el país. Estas manifestaciones se centraron en denunciar la brutalidad policial y el racismo sistémico, convirtiéndose en un movimiento que no solo marcó a Estados Unidos, sino que tuvo repercusión a nivel global.

**Impacto económicO**
La incertidumbre provocada tanto por la pandemia como por las tensiones sociales llevó a una mayor inestabilidad económica. La tasa de desempleo alcanzó niveles históricamente altos, y la confianza de consumidores e inversores se vio gravemente afectada.

**Desafíos específicos para una startup financiera en fase de crecimiento**
Business Payments, al haber sido fundada en 2020, se encontraba en una etapa temprana de consolidación de su base de usuarios. En un mes tan convulso, la empresa tuvo que lidiar con cambios bruscos en el comportamiento de sus clientes, quienes respondían a la incertidumbre económica y social con cautela. Esto pudo provocar que la retención de usuarios se viera afectada, ya que muchos optaron por reducir gastos o postergar el uso de adelantos de efectivo ante un panorama incierto.

En resumen, mayo de 2020 fue un mes en el que la reapertura económica se mezcló con una creciente crisis social y una administración federal bajo fuerte escrutinio, creando un escenario de alta volatilidad tanto en el ámbito político como en el económico.

**Si profundizamos más**

En mayo de 2020, Estados Unidos continuó enfrentando una CRISIS LABORAL derivada de la pandemia del COVID-19. Aunque hubo una ligera mejora respecto a abril, la tasa de desempleo seguía siendo históricamente alta:

**Niveles extraordinarios**
La tasa de desempleo (medida U3, que es la oficial) se situó alrededor del 13.3% en mayo, tras alcanzar un pico cercano al 14.7% en abril. Esto representó a millones de estadounidenses sin empleo o en situación de desempleo temporal.

**Impacto por la crisis sanitaria**
La pérdida masiva de empleos se debió a los cierres y restricciones impuestos para frenar la propagación del virus, lo que afectó especialmente a sectores como hostelería, turismo, comercio minorista y servicios.

**Recuperación incipiente**
Aunque la ligera disminución de abril a mayo sugería un inicio de recuperación, el mercado laboral seguía siendo muy vulnerable y la reintegración de los trabajadores a sus empleos habituales no era segura ni homogénea.

En resumen, mayo de 2020 fue un mes en el que, a pesar de un pequeño descenso en la tasa de desempleo, el mercado laboral en EE. UU. seguía en una situación crítica, reflejando el profundo impacto económico y social de la pandemia.

## BUSCANDO DATOS DE LA TASA DE PARO EN EEEUU EN ESA EPOCA

Se baraja ir a apis oficiales (se descarta porque hay que pedir permisos y solicitar token), se podria hacer webscrapping. Se opta por una tabla obtenida de un organismo oficial.

https://data.bls.gov/timeseries/LNS14000000

TABLA ORIGINAL
```html
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>10</th>
      <th>11</th>
      <th>12</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Labor Force Statistics from the Current Popula...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Original Data Value</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Series Id:</td>
      <td>LNS14000000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Seasonally Adjusted</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Series title:</td>
      <td>(Seas) Unemployment Rate</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Labor force status:</td>
      <td>Unemployment rate</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Type of data:</td>
      <td>Percent or rate</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Age:</td>
      <td>16 years and over</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Years:</td>
      <td>2015 to 2025</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>10</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Year</td>
      <td>Jan</td>
      <td>Feb</td>
      <td>Mar</td>
      <td>Apr</td>
      <td>May</td>
      <td>Jun</td>
      <td>Jul</td>
      <td>Aug</td>
      <td>Sep</td>
      <td>Oct</td>
      <td>Nov</td>
      <td>Dec</td>
    </tr>
    <tr>
      <th>12</th>
      <td>2015</td>
      <td>5.7</td>
      <td>5.5</td>
      <td>5.4</td>
      <td>5.4</td>
      <td>5.6</td>
      <td>5.3</td>
      <td>5.2</td>
      <td>5.1</td>
      <td>5</td>
      <td>5</td>
      <td>5.1</td>
      <td>5</td>
    </tr>
    <tr>
      <th>13</th>
      <td>2016</td>
      <td>4.8</td>
      <td>4.9</td>
      <td>5</td>
      <td>5.1</td>
      <td>4.8</td>
      <td>4.9</td>
      <td>4.8</td>
      <td>4.9</td>
      <td>5</td>
      <td>4.9</td>
      <td>4.7</td>
      <td>4.7</td>
    </tr>
    <tr>
      <th>14</th>
      <td>2017</td>
      <td>4.7</td>
      <td>4.6</td>
      <td>4.4</td>
      <td>4.4</td>
      <td>4.4</td>
      <td>4.3</td>
      <td>4.3</td>
      <td>4.4</td>
      <td>4.3</td>
      <td>4.2</td>
      <td>4.2</td>
      <td>4.1</td>
    </tr>
    <tr>
      <th>15</th>
      <td>2018</td>
      <td>4</td>
      <td>4.1</td>
      <td>4</td>
      <td>4</td>
      <td>3.8</td>
      <td>4</td>
      <td>3.8</td>
      <td>3.8</td>
      <td>3.7</td>
      <td>3.8</td>
      <td>3.8</td>
      <td>3.9</td>
    </tr>
    <tr>
      <th>16</th>
      <td>2019</td>
      <td>4</td>
      <td>3.8</td>
      <td>3.8</td>
      <td>3.7</td>
      <td>3.6</td>
      <td>3.6</td>
      <td>3.7</td>
      <td>3.6</td>
      <td>3.5</td>
      <td>3.6</td>
      <td>3.6</td>
      <td>3.6</td>
    </tr>
    <tr>
      <th>17</th>
      <td>2020</td>
      <td>3.6</td>
      <td>3.5</td>
      <td>4.4</td>
      <td>14.8</td>
      <td>13.2</td>
      <td>11</td>
      <td>10.2</td>
      <td>8.4</td>
      <td>7.8</td>
      <td>6.9</td>
      <td>6.7</td>
      <td>6.7</td>
    </tr>
    <tr>
      <th>18</th>
      <td>2021</td>
      <td>6.4</td>
      <td>6.2</td>
      <td>6.1</td>
      <td>6.1</td>
      <td>5.8</td>
      <td>5.9</td>
      <td>5.4</td>
      <td>5.1</td>
      <td>4.7</td>
      <td>4.5</td>
      <td>4.2</td>
      <td>3.9</td>
    </tr>
    <tr>
      <th>19</th>
      <td>2022</td>
      <td>4</td>
      <td>3.8</td>
      <td>3.7</td>
      <td>3.7</td>
      <td>3.6</td>
      <td>3.6</td>
      <td>3.5</td>
      <td>3.6</td>
      <td>3.5</td>
      <td>3.6</td>
      <td>3.6</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>20</th>
      <td>2023</td>
      <td>3.5</td>
      <td>3.6</td>
      <td>3.5</td>
      <td>3.4</td>
      <td>3.6</td>
      <td>3.6</td>
      <td>3.5</td>
      <td>3.7</td>
      <td>3.8</td>
      <td>3.9</td>
      <td>3.7</td>
      <td>3.8</td>
    </tr>
    <tr>
      <th>21</th>
      <td>2024</td>
      <td>3.7</td>
      <td>3.9</td>
      <td>3.9</td>
      <td>3.9</td>
      <td>4</td>
      <td>4.1</td>
      <td>4.2</td>
      <td>4.2</td>
      <td>4.1</td>
      <td>4.1</td>
      <td>4.2</td>
      <td>4.1</td>
    </tr>
    <tr>
      <th>22</th>
      <td>2025</td>
      <td>4</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>
```

DE ESTO SELECCIONO SOLO LO QUE ME INTERESA
```html
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Repayment_Status</th>
      <th>Defaulter</th>
      <th>Late Payer</th>
      <th>On-time Payer</th>
      <th>Tasa_Paro</th>
    </tr>
    <tr>
      <th>Month</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2019-11</th>
      <td>64</td>
      <td>0</td>
      <td>16</td>
      <td>3.6</td>
    </tr>
    <tr>
      <th>2019-12</th>
      <td>14775</td>
      <td>8911</td>
      <td>1590</td>
      <td>3.6</td>
    </tr>
    <tr>
      <th>2020-01</th>
      <td>3644</td>
      <td>2210</td>
      <td>400</td>
      <td>3.6</td>
    </tr>
    <tr>
      <th>2020-02</th>
      <td>2394</td>
      <td>1440</td>
      <td>250</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>2020-03</th>
      <td>4573</td>
      <td>2725</td>
      <td>506</td>
      <td>4.4</td>
    </tr>
    <tr>
      <th>2020-04</th>
      <td>11429</td>
      <td>6703</td>
      <td>1232</td>
      <td>14.8</td>
    </tr>
    <tr>
      <th>2020-05</th>
      <td>18125</td>
      <td>10787</td>
      <td>1954</td>
      <td>13.2</td>
    </tr>
    <tr>
      <th>2020-06</th>
      <td>54395</td>
      <td>32387</td>
      <td>5910</td>
      <td>11</td>
    </tr>
    <tr>
      <th>2020-07</th>
      <td>39790</td>
      <td>23620</td>
      <td>4476</td>
      <td>10.2</td>
    </tr>
    <tr>
      <th>2020-08</th>
      <td>11804</td>
      <td>7205</td>
      <td>1296</td>
      <td>8.4</td>
    </tr>
    <tr>
      <th>2020-09</th>
      <td>27057</td>
      <td>16096</td>
      <td>2882</td>
      <td>7.8</td>
    </tr>
    <tr>
      <th>2020-10</th>
      <td>11768</td>
      <td>6983</td>
      <td>1239</td>
      <td>6.9</td>
    </tr>
    <tr>
      <th>2020-11</th>
      <td>95</td>
      <td>58</td>
      <td>10</td>
      <td>6.7</td>
    </tr>
  </tbody>
</table>
</div>
```










