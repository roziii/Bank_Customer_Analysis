## Modelos de Regresión

- **Resultados comparativos**: Se evaluarán diferentes modelos de regresión (por ejemplo, regresión lineal, regresión polinómica o métodos basados en árboles) para determinar cuál ofrece el mejor desempeño de acuerdo con métricas como RMSE, MAE o R².

- **Justificación de hiperparámetros**: Se documentará el proceso de búsqueda y optimización de hiperparámetros (Grid Search, Random Search, Bayesian Optimization, etc.) junto a los criterios para seleccionar la mejor configuración.

- **Análisis de residuos**: Se presentará un estudio detallado de los residuos para identificar sesgos en las predicciones, zonas de sobreajuste o bajoajuste y pautas de comportamiento inesperadas.


## Análisis de Causalidad

- **Conclusiones basadas en cohortes**: Se crearán y estudiarán cohortes de usuarios (ej. por fecha de primer adelanto de efectivo, categoría de riesgo, etc.) para aislar variables que puedan tener un impacto significativo en la métrica de interés.

- **Evidencia estadística**: A partir de correlaciones observadas en los datos, se utilizarán técnicas (p. ej., análisis de diferencias en diferencias, métodos de control sintético o validación cruzada con cohortes) para aportar pruebas que respalden o descarten relaciones de causalidad.

## ARCHIVOS DONDE VER LOS DETALLES
  
- bank-customer-payback-carmen.ipynb
- experimento-carmen.ipynb

# Análisis Relación TASA DE PARO (exógena) Y TASA DE RETENCIÓN

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

#### OBSERVACION

Hay una continuidad entre los diferentes grupos, en sus porcentajes, a lo largo del tiempo. Eso es algo a tener en cuenta. No varian significativamente excepto respecto al primer mes observado. 

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
- Grado 3 - RMSE Train: 0.2807, R² Train: 0.3060, RMSE Test: 0.2971, R² Test: 0.1921, Diferencia R²: 0.1138
- Grado 5 - RMSE Train: 0.2760, R² Train: 0.3287, RMSE Test: 0.2952, R² Test: 0.2025, Diferencia R²: 0.1261
- Grado 7 - RMSE Train: 0.2616, R² Train: 0.3970, RMSE Test: 0.2818, R² Test: 0.2733, Diferencia R²: 0.1236
- Grado 9 - RMSE Train: 0.2520, R² Train: 0.4407, RMSE Test: 0.2634, R² Test: 0.3649, Diferencia R²: 0.0759
- Grado 10 - RMSE Train: 0.2475, R² Train: 0.4601, RMSE Test: 0.2486, R² Test: 0.4343, Diferencia R²: 0.0258
- Grado 11 - RMSE Train: 0.2475, R² Train: 0.4602, RMSE Test: 0.2491, R² Test: 0.4322, Diferencia R²: 0.0280
 - Mejor modelo: Grado 10 - R² Test: 0.4343
 ![Image](https://github.com/user-attachments/assets/eb7c421c-ab50-43a8-a0f7-47e4d439d975)

 Evaluando regresión para: Segmento - Repayment_Late Payer
- Grado 3 - RMSE Train: 0.2604, R² Train: 0.4256, RMSE Test: 0.2793, R² Test: 0.0614, Diferencia R²: 0.3642
- Grado 5 - RMSE Train: 0.2520, R² Train: 0.4620, RMSE Test: 0.2761, R² Test: 0.0827, Diferencia R²: 0.3793
- Grado 7 - RMSE Train: 0.2438, R² Train: 0.4967, RMSE Test: 0.2679, R² Test: 0.1366, Diferencia R²: 0.3601
- Grado 9 - RMSE Train: 0.2336, R² Train: 0.5379, RMSE Test: 0.2596, R² Test: 0.1891, Diferencia R²: 0.3488
- Grado 10 - RMSE Train: 0.2324, R² Train: 0.5424, RMSE Test: 0.2574, R² Test: 0.2032, Diferencia R²: 0.3392
- Grado 11 - RMSE Train: 0.2298, R² Train: 0.5526, RMSE Test: 0.2526, R² Test: 0.2327, Diferencia R²: 0.3198
 Mejor modelo: Grado 11 - R² Test: 0.2327
![Image](https://github.com/user-attachments/assets/74dd2e22-f224-4474-9063-4ce63e9100ab)

Evaluando regresión para: Segmento - Repayment_On-time Payer
- Grado 3 - RMSE Train: 0.2721, R² Train: 0.3299, RMSE Test: 0.2763, R² Test: 0.3384, Diferencia R²: 0.0085
- Grado 5 - RMSE Train: 0.2567, R² Train: 0.4037, RMSE Test: 0.2687, R² Test: 0.3741, Diferencia R²: 0.0295
- Grado 7 - RMSE Train: 0.2514, R² Train: 0.4277, RMSE Test: 0.2604, R² Test: 0.4121, Diferencia R²: 0.0156
- Grado 9 - RMSE Train: 0.2415, R² Train: 0.4723, RMSE Test: 0.2470, R² Test: 0.4711, Diferencia R²: 0.0012
- Grado 10 - RMSE Train: 0.2316, R² Train: 0.5144, RMSE Test: 0.2464, R² Test: 0.4737, Diferencia R²: 0.0408
- Grado 11 - RMSE Train: 0.2293, R² Train: 0.5240, RMSE Test: 0.2495, R² Test: 0.4603, Diferencia R²: 0.0637
 - Mejor modelo: Grado 10 - R² Test: 0.4737
 ![Image](https://github.com/user-attachments/assets/74dd2e22-f224-4474-9063-4ce63e9100ab)

## CON FOURIER
```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import warnings
from scipy.optimize import curve_fit
from sklearn.metrics import mean_squared_error, r2_score
from sklearn.model_selection import train_test_split

warnings.filterwarnings('ignore')

def apply_fourier_regression(df_retention, title):
    print(f"Evaluando Fourier para: {title}")

    # Convertir cohort_d a datetime
    df_retention["cohort_d"] = pd.to_datetime(df_retention["cohort_d"])

    # Agrupar por cohorte diaria y calcular la media de la Retention Rate
    df_daily_mean = df_retention.groupby(pd.Grouper(key="cohort_d", freq="D")).agg(
        Mean_Retention_Rate=("Retention_Rate", "mean")
    ).reset_index()

    # Eliminar valores NaN en la Retention Rate
    df_daily_mean = df_daily_mean.dropna(subset=["Mean_Retention_Rate"])

    # Convertir la fecha en tiempo relativo (días desde la primera cohorte)
    df_daily_mean["Time"] = (df_daily_mean["cohort_d"] - df_daily_mean["cohort_d"].min()).dt.days
    X = df_daily_mean["Time"].values
    y = df_daily_mean["Mean_Retention_Rate"].values

    # Dividir en conjuntos de entrenamiento y prueba
    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

    # Definir la función de tendencia lineal
    def linear_trend(t, a, b):
        return a * t + b

    # Ajustar la tendencia utilizando solo los datos de entrenamiento
    params_trend, _ = curve_fit(linear_trend, X_train, y_train)
    trend_train = linear_trend(X_train, *params_trend)
    trend_test = linear_trend(X_test, *params_trend)

    # Definir la función para capturar la estacionalidad con términos de Fourier
    def seasonal_fourier(t, a1, b1, a2, b2):
        P = 365  # Periodo anual
        return (a1 * np.sin(2 * np.pi * t / P) + b1 * np.cos(2 * np.pi * t / P) +
                a2 * np.sin(4 * np.pi * t / P) + b2 * np.cos(4 * np.pi * t / P))

    # Ajustar la estacionalidad con los datos de entrenamiento (sobre los residuos de la tendencia)
    params_seasonal, _ = curve_fit(seasonal_fourier, X_train, y_train - trend_train)
    seasonal_train = seasonal_fourier(X_train, *params_seasonal)
    seasonal_test = seasonal_fourier(X_test, *params_seasonal)

    # Predicciones para entrenamiento y prueba
    y_pred_train = trend_train + seasonal_train
    y_pred_test = trend_test + seasonal_test

    # Evaluar el desempeño del modelo
    rmse_train = np.sqrt(mean_squared_error(y_train, y_pred_train))
    r2_train = r2_score(y_train, y_pred_train)
    rmse_test = np.sqrt(mean_squared_error(y_test, y_pred_test))
    r2_test = r2_score(y_test, y_pred_test)

    print("Resultados en entrenamiento:")
    print(f"RMSE Train: {rmse_train:.4f}")
    print(f"R² Train: {r2_train:.4f}")
    print("Resultados en prueba:")
    print(f"RMSE Test: {rmse_test:.4f}")
    print(f"R² Test: {r2_test:.4f}")
    r2_diff = abs(r2_train - r2_test)
    print( f"diff R² train-test : {r2_diff:.4f}")

    # Generar predicciones futuras
    future_time = np.arange(X.max() + 1, X.max() + 366)
    future_trend = linear_trend(future_time, *params_trend)
    future_seasonal = seasonal_fourier(future_time, *params_seasonal)
    future_predicted = future_trend + future_seasonal

    # Reconstruir fechas futuras
    future_dates = [df_daily_mean["cohort_d"].min() + pd.Timedelta(days=int(t)) for t in future_time]

    # Calcular el modelo ajustado sobre todos los datos utilizando los parámetros de entrenamiento
    model_fitted_all = linear_trend(df_daily_mean["Time"].values, *params_trend) + \
                       seasonal_fourier(df_daily_mean["Time"].values, *params_seasonal)

    # Crear el gráfico
    plt.figure(figsize=(12, 6))
    plt.scatter(df_daily_mean["cohort_d"], y, alpha=0.7, label="Datos Reales", color="blue")
    plt.plot(df_daily_mean["cohort_d"], model_fitted_all, label="Modelo Ajustado", color="orange")
    plt.plot(future_dates, future_predicted, label="Predicción Futura (1 Año)", linestyle='--', color="green")
    plt.title(f"Predicción de la Retention Rate con Fourier - {title}", fontsize=16)
    plt.xlabel("Fecha de Cohorte", fontsize=14)
    plt.ylabel("Retention Rate", fontsize=14)
    plt.gca().set_yticklabels(['{:,.0%}'.format(x) for x in plt.gca().get_yticks()])
    plt.yscale('log')
    plt.grid(alpha=0.3)
    plt.xticks(rotation=45)
    plt.legend()
    plt.tight_layout()
    plt.show()

# Aplicar la función a cada subdataset 
for key, df_churn in sub_retention.items():
    apply_fourier_regression(df_churn, f"Segmento - {key}")
```
Evaluando Fourier para: Segmento - Repayment_Defaulter
- Resultados en entrenamiento:
- RMSE Train: 0.2768
- R² Train: 0.3252
- Resultados en prueba:
- RMSE Test: 0.3003
- R² Test: 0.1745
- diff R² train-test : 0.1507

![Image](https://github.com/user-attachments/assets/a19539a7-e82f-4267-864a-a52f4bd6353d)

Evaluando Fourier para: Segmento - Repayment_Late Payer
- Resultados en entrenamiento:
- RMSE Train: 0.2608
- R² Train: 0.4241
- Resultados en prueba:
- RMSE Test: 0.2848
- R² Test: 0.0243
- diff R² train-test : 0.3998

![Image](https://github.com/user-attachments/assets/04605d03-4dab-471f-b3dd-95dd2dddd281)

Evaluando Fourier para: Segmento - Repayment_On-time Payer
- Resultados en entrenamiento:
- RMSE Train: 0.2588
- R² Train: 0.3939
- Resultados en prueba:
- RMSE Test: 0.2702
- R² Test: 0.3670
- diff R² train-test : 0.0270

![Image](https://github.com/user-attachments/assets/e5adce17-59ee-4b02-8f29-faecc1d07c8e)


 ## NUEVA SEGMENTACIÓN, ADAPTACIÓN DEL CÓDIGO Y NUEVOS RESULTADOS

Se vuelve a hacer la segmentación pero no solo por Defaulter, Late Payer y One-time Payer, se analiza que usarios de estos estan en activo o se han dado de baja.
 
 Se crearon 6 subdatasets.
dict_keys(['Defaulter_activo', 'Defaulter_inactivo', 'Late Payer_activo', 'Late Payer_inactivo', 'On-time Payer_activo', 'On-time Payer_inactivo'])

Evaluando regresión para: Segmento - Defaulter_activo
- Grado 3 - RMSE Train: 0.2762, R² Train: 0.2711, RMSE Test: 0.2761, R² Test: 0.3129, Diferencia R²: 0.0418
- Grado 5 - RMSE Train: 0.2713, R² Train: 0.2970, RMSE Test: 0.2705, R² Test: 0.3406, Diferencia R²: 0.0436
- Grado 7 - RMSE Train: 0.2523, R² Train: 0.3916, RMSE Test: 0.2460, R² Test: 0.4546, Diferencia R²: 0.0629
- Grado 9 - RMSE Train: 0.2436, R² Train: 0.4332, RMSE Test: 0.2488, R² Test: 0.4420, Diferencia R²: 0.0088
- Grado 10 - RMSE Train: 0.2379, R² Train: 0.4593, RMSE Test: 0.2494, R² Test: 0.4395, Diferencia R²: 0.0198
- Grado 11 - RMSE Train: 0.2376, R² Train: 0.4608, RMSE Test: 0.2484, R² Test: 0.4437, Diferencia R²: 0.0171
- Mejor modelo: Grado 7 - R² Test: 0.4546

![Image](https://github.com/user-attachments/assets/3798a4a2-be57-40c8-974d-c48f330639fe)

Evaluando regresión para: Segmento - Defaulter_inactivo
- Grado 3 - RMSE Train: 0.2626, R² Train: 0.3061, RMSE Test: 0.3008, R² Test: 0.2297, Diferencia R²: 0.0764
- Grado 5 - RMSE Train: 0.2544, R² Train: 0.3487, RMSE Test: 0.2802, R² Test: 0.3319, Diferencia R²: 0.0168
- Grado 7 - RMSE Train: 0.2492, R² Train: 0.3750, RMSE Test: 0.2646, R² Test: 0.4042, Diferencia R²: 0.0292
- Grado 9 - RMSE Train: 0.2408, R² Train: 0.4166, RMSE Test: 0.2423, R² Test: 0.5001, Diferencia R²: 0.0835
- Grado 10 - RMSE Train: 0.2389, R² Train: 0.4258, RMSE Test: 0.2411, R² Test: 0.5051, Diferencia R²: 0.0793
- Grado 11 - RMSE Train: 0.2362, R² Train: 0.4387, RMSE Test: 0.2464, R² Test: 0.4830, Diferencia R²: 0.0444
- Mejor modelo: Grado 10 - R² Test: 0.5051

![image](https://github.com/user-attachments/assets/2c3a32fe-65f3-4a77-a031-79e32562d481)

Evaluando regresión para: Segmento - Late Payer_activo
- Grado 3 - RMSE Train: 0.2347, R² Train: 0.3265, RMSE Test: 0.2557, R² Test: 0.2831, Diferencia R²: 0.0434
- Grado 5 - RMSE Train: 0.2306, R² Train: 0.3496, RMSE Test: 0.2546, R² Test: 0.2897, Diferencia R²: 0.0599
- Grado 7 - RMSE Train: 0.2173, R² Train: 0.4225, RMSE Test: 0.2595, R² Test: 0.2619, Diferencia R²: 0.1606
- Grado 9 - RMSE Train: 0.2131, R² Train: 0.4449, RMSE Test: 0.2534, R² Test: 0.2960, Diferencia R²: 0.1489
- Grado 10 - RMSE Train: 0.2088, R² Train: 0.4670, RMSE Test: 0.2697, R² Test: 0.2024, Diferencia R²: 0.2646
- Grado 11 - RMSE Train: 0.2088, R² Train: 0.4670, RMSE Test: 0.2706, R² Test: 0.1974, Diferencia R²: 0.2697
- Mejor modelo: Grado 9 - R² Test: 0.2960

![Image](https://github.com/user-attachments/assets/658196bb-c65a-4f63-909d-7e7f24426672)

Evaluando regresión para: Segmento - Late Payer_inactivo
- Grado 3 - RMSE Train: 0.2626, R² Train: 0.3061, RMSE Test: 0.3008, R² Test: 0.2297, Diferencia R²: 0.0764
- Grado 5 - RMSE Train: 0.2544, R² Train: 0.3487, RMSE Test: 0.2802, R² Test: 0.3319, Diferencia R²: 0.0168
- Grado 7 - RMSE Train: 0.2492, R² Train: 0.3750, RMSE Test: 0.2646, R² Test: 0.4042, Diferencia R²: 0.0292
- Grado 9 - RMSE Train: 0.2408, R² Train: 0.4166, RMSE Test: 0.2423, R² Test: 0.5001, Diferencia R²: 0.0835
- Grado 10 - RMSE Train: 0.2389, R² Train: 0.4258, RMSE Test: 0.2411, R² Test: 0.5051, Diferencia R²: 0.0793
- Grado 11 - RMSE Train: 0.2362, R² Train: 0.4387, RMSE Test: 0.2464, R² Test: 0.4830, Diferencia R²: 0.0444
- Mejor modelo: Grado 10 - R² Test: 0.5051

![Image](https://github.com/user-attachments/assets/78d906e7-0b5d-45e7-b8b8-3fb9d3e86a54)

Evaluando regresión para: Segmento - On-time Payer_activo
- Grado 3 - RMSE Train: 0.2214, R² Train: 0.1984, RMSE Test: 0.2380, R² Test: -0.0229, Diferencia R²: 0.2213
- Grado 5 - RMSE Train: 0.2181, R² Train: 0.2226, RMSE Test: 0.2695, R² Test: -0.3114, Diferencia R²: 0.5341
- Grado 7 - RMSE Train: 0.2142, R² Train: 0.2499, RMSE Test: 0.8897, R² Test: -13.2890, Diferencia R²: 13.5389
- Grado 9 - RMSE Train: 0.2135, R² Train: 0.2550, RMSE Test: 0.2433, R² Test: -0.0685, Diferencia R²: 0.3235
- Grado 10 - RMSE Train: 0.2124, R² Train: 0.2624, RMSE Test: 7.2672, R² Test: -952.3812, Diferencia R²: 952.6437
- Grado 11 - RMSE Train: 0.2081, R² Train: 0.2920, RMSE Test: 70.6852, R² Test: -90194.2490, Diferencia R²: 90194.5409
- Mejor modelo: Grado 3 - R² Test: -0.0229

![Image](https://github.com/user-attachments/assets/d1a688c2-a93a-4371-8ce2-6351178d3413)

Evaluando regresión para: Segmento - On-time Payer_inactivo
- Grado 3 - RMSE Train: 0.2626, R² Train: 0.3061, RMSE Test: 0.3008, R² Test: 0.2297, Diferencia R²: 0.0764
- Grado 5 - RMSE Train: 0.2544, R² Train: 0.3487, RMSE Test: 0.2802, R² Test: 0.3319, Diferencia R²: 0.0168
- Grado 7 - RMSE Train: 0.2492, R² Train: 0.3750, RMSE Test: 0.2646, R² Test: 0.4042, Diferencia R²: 0.0292
- Grado 9 - RMSE Train: 0.2408, R² Train: 0.4166, RMSE Test: 0.2423, R² Test: 0.5001, Diferencia R²: 0.0835
- Grado 10 - RMSE Train: 0.2389, R² Train: 0.4258, RMSE Test: 0.2411, R² Test: 0.5051, Diferencia R²: 0.0793
- Grado 11 - RMSE Train: 0.2362, R² Train: 0.4387, RMSE Test: 0.2464, R² Test: 0.4830, Diferencia R²: 0.0444
- Mejor modelo: Grado 10 - R² Test: 0.5051

![Image](https://github.com/user-attachments/assets/50098236-9608-4c36-840c-08b9f5d08274)

![Image](https://github.com/user-attachments/assets/7cf5535e-0b5d-4cf0-b330-a613b9849425)


## QUE PUEDE ESTAR PASANDO AQUÍ?

Se observa claramente en todos los grupos una **caída de la tasa de retención** más o menos acusada en los mismos meses.

**A que se puede deber esto?**

En mayo de 2020 en Estados Unidos se vivieron momentos de gran transformación y tensión. Algunos de los hechos más relevantes fueron:

**Inicio de la reapertura tras el confinamiento**
Tras los estrictos cierres de marzo y abril para frenar la propagación del COVID-19, muchos estados comenzaron a flexibilizar las restricciones. Se implementaron medidas para reabrir negocios, restaurantes y otros servicios, aunque de forma muy cautelosa y con protocolos sanitarios, lo que generó incertidumbre respecto al riesgo de nuevos contagios.

**Tensiones en la gestión de la pandemia**
Mientras algunos estados impulsaban la reactivación económica, surgían debates intensos sobre la seguridad y la eficacia de estas medidas. La presión por equilibrar la salud pública con la necesidad de reactivar la economía se convirtió en un tema central en el discurso político y mediático.

**Protestas por la justicia racial**
El 25 de mayo de 2020, el trágico fallecimiento de George Floyd en Minneapolis desencadenó protestas masivas en todo el país. Estas manifestaciones se centraron en denunciar la brutalidad policial y el racismo sistémico, convirtiéndose en un movimiento que no solo marcó a Estados Unidos, sino que tuvo repercusión a nivel global.

**Impacto económico**
La incertidumbre provocada tanto por la pandemia como por las tensiones sociales llevó a una mayor inestabilidad económica. La tasa de desempleo alcanzó niveles históricamente altos, y la confianza de consumidores e inversores se vio gravemente afectada.

**Desafíos específicos para una startup financiera en fase de crecimiento**
Business Payments, al haber sido fundada en 2020, se encontraba en una etapa temprana de consolidación de su base de usuarios. En un mes tan convulso, la empresa tuvo que lidiar con cambios bruscos en el comportamiento de sus clientes, quienes respondían a la incertidumbre económica y social con cautela. Esto pudo provocar que la retención de usuarios se viera afectada, ya que muchos optaron por reducir gastos o postergar el uso de adelantos de efectivo ante un panorama incierto.

En resumen, mayo de 2020 fue un mes en el que la reapertura económica se mezcló con una creciente crisis social y una administración federal bajo fuerte escrutinio, creando un escenario de alta volatilidad tanto en el ámbito político como en el económico.

**Si profundizamos más**

En mayo de 2020, Estados Unidos continuó enfrentando una **CRISIS LABORAL derivada de la pandemia del COVID-19**. Aunque hubo una ligera mejora respecto a abril, la tasa de desempleo seguía siendo históricamente alta:

**Niveles extraordinarios**
La tasa de desempleo (medida U3, que es la oficial) se situó alrededor del 13.3% en mayo, tras alcanzar un pico cercano al 14.7% en abril. Esto representó a millones de estadounidenses sin empleo o en situación de desempleo temporal.

**Impacto por la crisis sanitaria**
La pérdida masiva de empleos se debió a los cierres y restricciones impuestos para frenar la propagación del virus, lo que afectó especialmente a sectores como hostelería, turismo, comercio minorista y servicios.

**Recuperación incipiente**
Aunque la ligera disminución de abril a mayo sugería un inicio de recuperación, el mercado laboral seguía siendo muy vulnerable y la reintegración de los trabajadores a sus empleos habituales no era segura ni homogénea.

En resumen, mayo de 2020 fue un mes en el que, a pesar de un pequeño descenso en la tasa de desempleo, el mercado laboral en EE. UU. seguía en una situación crítica, reflejando el profundo impacto económico y social de la pandemia.

**Fenomeno de la gran renuncia**

La gran dimisión o gran renuncia​​ es una dimisión laboral generalizada que comenzó en Estados Unidos en julio de 2020, tras la pandemia de COVID-19, cuando millones de estadounidenses insatisfechos con su trabajo o su salario renunciaron a él. Esta tendencia laboral comenzó a replicarse en el mundo.

## BUSCANDO DATOS DE LA TASA DE PARO EN EEEUU EN ESA ÉPOCA

Se baraja ir a APIs oficiales (se descarta porque hay que pedir permisos y solicitar token y eso tarda, además que hay que estudiarse la api para integrarla), se podria hacer webscrapping. Se opta, por rapidez, por una tabla obtenida de un organismo oficial.

https://data.bls.gov/timeseries/LNS14000000

TABLA ORIGINAL

<div>
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

DE ESTO SELECCIONO SÓLO LO QUE ME INTERESA

<div>
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

### APLICACIÓN DE NUEVO AL MODELO ANTERIOR PERO CON TASA DE PARO

```python
def apply_regression(df_retention, title):
    print(f"Evaluando regresión para: {title}")

    # Convertir cohort_d a datetime (ya debería estarlo)
    df_retention["cohort_d"] = pd.to_datetime(df_retention["cohort_d"])

    # Agrupar por cohorte diaria y calcular la media de la Retention Rate
    df_daily_mean = df_retention.groupby(pd.Grouper(key="cohort_d", freq="D")).agg(
        Mean_Retention_Rate=("Retention_Rate", "mean")
    ).reset_index()
    df_daily_mean = df_daily_mean.dropna(subset=["Mean_Retention_Rate"])

    # Agrupar por cohorte diaria y obtener Tasa_Paro (sin agrupar, se toma la media en este caso) frequencia mensual
    df_daily_tasa = df_retention.groupby(pd.Grouper(key="cohort_d", freq="M")).agg(
        Mean_Tasa_Paro=("Tasa_Paro", "mean")
    ).reset_index()

    # Convertir la fecha a número ordinal para la regresión
    df_daily_mean["Date_Num"] = df_daily_mean["cohort_d"].map(pd.Timestamp.toordinal)
    origin_date = df_daily_mean["cohort_d"].min()

    # Variables para la regresión (sobre Retention Rate)
    X = df_daily_mean[["Date_Num"]].values
    y = df_daily_mean["Mean_Retention_Rate"].values

    if len(X) < 10:
        print(f"{title} tiene muy pocos datos para aplicar regresión.")
        return None

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

    date_range = np.linspace(X_train.min(), X_train.max(), 500).reshape(-1, 1)
    additional_days = 90  # 90 días futuros
    future_dates = np.linspace(X.max() + 1, X.max() + additional_days, 1000).reshape(-1, 1)

    results = []
    for degree in [3, 5, 7, 9, 10, 11]:
        scaler = StandardScaler()
        X_train_scaled = scaler.fit_transform(X_train)
        X_test_scaled = scaler.transform(X_test)
        date_range_scaled = scaler.transform(date_range)
        future_dates_scaled = scaler.transform(future_dates)

        poly = PolynomialFeatures(degree=degree)
        X_train_poly = poly.fit_transform(X_train_scaled)
        X_test_poly = poly.transform(X_test_scaled)
        date_range_poly = poly.transform(date_range_scaled)
        future_dates_poly = poly.transform(future_dates_scaled)

        model = LinearRegression()
        model.fit(X_train_poly, y_train)

        y_train_pred = model.predict(X_train_poly)
        y_test_pred = model.predict(X_test_poly)
        y_pred_range = model.predict(date_range_poly)
        y_future_pred = model.predict(future_dates_poly)

        rmse_train = np.sqrt(mean_squared_error(y_train, y_train_pred))
        rmse_test = np.sqrt(mean_squared_error(y_test, y_test_pred))
        r2_train = r2_score(y_train, y_train_pred)
        r2_test = r2_score(y_test, y_test_pred)
        r2_diff = abs(r2_train - r2_test)

        results.append((degree, rmse_train, r2_train, rmse_test, r2_test, r2_diff, y_pred_range, y_future_pred))
        print(f"Grado {degree} - RMSE Train: {rmse_train:.4f}, R² Train: {r2_train:.4f}, RMSE Test: {rmse_test:.4f}, R² Test: {r2_test:.4f}, Dif. R²: {r2_diff:.4f}")

    best_model = max(results, key=lambda x: x[4])
    best_degree, _, _, _, best_r2_test, _, best_y_pred_range, best_y_future_pred = best_model
    print(f"Mejor modelo: Grado {best_degree} - R² Test: {best_r2_test:.4f}")

    min_date_num = df_daily_mean["Date_Num"].min()
    model_dates = [origin_date + pd.Timedelta(days=int(num - min_date_num)) for num in date_range.flatten()]
    

    # Gráfica con doble eje y:
    fig, ax1 = plt.subplots(figsize=(12, 6))
    ax2 = ax1.twinx()

    # Eje izquierdo: Retention Rate
    ax1.scatter(df_daily_mean["cohort_d"], df_daily_mean["Mean_Retention_Rate"],
                alpha=0.7, label="Datos Reales (Retención)", color="blue")
    ax1.plot(model_dates, best_y_pred_range,
             label=f"Modelo Retención (Grado {best_degree})", linewidth=2, color="green")

    ax1.set_xlabel("Fecha de Cohorte", fontsize=14)
    ax1.set_ylabel("Retention Rate Promedio", fontsize=14, color="blue")
    ax1.tick_params(axis='y', labelcolor="blue")
    ax1.set_yscale("log")
    ax1.grid(alpha=0.3)

    # Eje derecho: Tasa de Paro 
    ax2.plot(df_daily_tasa["cohort_d"], df_daily_tasa["Mean_Tasa_Paro"],
             color="magenta", label="Tasa de Paro", linewidth=2)
    ax2.set_ylabel("Tasa de Paro", fontsize=14, color="magenta")
    ax2.tick_params(axis='y', labelcolor="magenta")


    lines1, labels1 = ax1.get_legend_handles_labels()
    lines2, labels2 = ax2.get_legend_handles_labels()
    ax2.legend(lines1 + lines2, labels1 + labels2, loc="upper right")

    plt.title(f"Regresión Polinómica - {title} (Mejor Modelo: {best_degree})", fontsize=16)
    plt.xticks(rotation=45)
    plt.tight_layout()
    plt.show()

    return title, model_dates, best_y_pred_range

# Aplicar regresión a todos los subdatasets en sub_retention y almacenar las líneas de modelo
model_lines = {}
for key, df_churn in sub_retention.items():
    result = apply_regression(df_churn, f"Segmento - {key}")
    if result is not None:
        title, dates, model_line = result
        model_lines[title] = (dates, model_line)

# la media mensual de Tasa_Paro  
plt.figure(figsize=(12, 6))
for key, df in sub_retention.items():    
    df['cohort_d'] = pd.to_datetime(df['cohort_d']) 
    df_monthly = df.groupby(pd.Grouper(key="cohort_d", freq="M")).agg({'Tasa_Paro': 'mean'}).reset_index()
    plt.plot(df_monthly["cohort_d"], df_monthly["Tasa_Paro"], marker="o", label=f"{key}")

plt.title("Media Mensual de Tasa de Paro por Subdataset", fontsize=16)
plt.xlabel("Mes", fontsize=14)
plt.ylabel("Tasa de Paro (Media Mensual)", fontsize=14)
plt.xticks(rotation=45)
plt.grid(alpha=0.3)
plt.legend(loc="upper right")
plt.tight_layout()
plt.show()


plt.figure(figsize=(12, 6))
for idx, (title, (dates, model_line)) in enumerate(model_lines.items()):
    plt.plot(dates, model_line, label=title, linewidth=2)
plt.title("Comparación de Líneas de Modelo entre Subdatasets", fontsize=16)
plt.xlabel("Fecha de Cohorte", fontsize=14)
plt.ylabel("Retention Rate Promedio", fontsize=14)
plt.yscale("log")
plt.xticks(rotation=45)
plt.grid(alpha=0.3)
plt.legend(loc="upper right")
plt.tight_layout()
plt.show()
```

Evaluando regresión para: Segmento - Repayment_Defaulter
- Grado 3 - RMSE Train: 0.2807, R² Train: 0.3060, RMSE Test: 0.2971, R² Test: 0.1921, Dif. R²: 0.1138
- Grado 5 - RMSE Train: 0.2760, R² Train: 0.3287, RMSE Test: 0.2952, R² Test: 0.2025, Dif. R²: 0.1261
- Grado 7 - RMSE Train: 0.2616, R² Train: 0.3970, RMSE Test: 0.2818, R² Test: 0.2733, Dif. R²: 0.1236
- Grado 9 - RMSE Train: 0.2520, R² Train: 0.4407, RMSE Test: 0.2634, R² Test: 0.3649, Dif. R²: 0.0759
- Grado 10 - RMSE Train: 0.2475, R² Train: 0.4601, RMSE Test: 0.2486, R² Test: 0.4343, Dif. R²: 0.0258
- Grado 11 - RMSE Train: 0.2475, R² Train: 0.4602, RMSE Test: 0.2491, R² Test: 0.4322, Dif. R²: 0.0280
- Mejor modelo: Grado 10 - R² Test: 0.4343

![Image](https://github.com/user-attachments/assets/a34c0509-91eb-4b1e-819d-2d92100f4ba7)

Evaluando regresión para: Segmento - Repayment_Late Payer
- Grado 3 - RMSE Train: 0.2604, R² Train: 0.4256, RMSE Test: 0.2793, R² Test: 0.0614, Dif. R²: 0.3642
- Grado 5 - RMSE Train: 0.2520, R² Train: 0.4620, RMSE Test: 0.2761, R² Test: 0.0827, Dif. R²: 0.3793
- Grado 7 - RMSE Train: 0.2438, R² Train: 0.4967, RMSE Test: 0.2679, R² Test: 0.1366, Dif. R²: 0.3601
- Grado 9 - RMSE Train: 0.2336, R² Train: 0.5379, RMSE Test: 0.2596, R² Test: 0.1891, Dif. R²: 0.3488
- Grado 10 - RMSE Train: 0.2324, R² Train: 0.5424, RMSE Test: 0.2574, R² Test: 0.2032, Dif. R²: 0.3392
- Grado 11 - RMSE Train: 0.2298, R² Train: 0.5526, RMSE Test: 0.2526, R² Test: 0.2327, Dif. R²: 0.3198
- Mejor modelo: Grado 11 - R² Test: 0.2327

![Image](https://github.com/user-attachments/assets/d2aca54d-84c1-4943-9f48-4a0200a947e5)

Evaluando regresión para: Segmento - Repayment_On-time Payer
- Grado 3 - RMSE Train: 0.2721, R² Train: 0.3299, RMSE Test: 0.2763, R² Test: 0.3384, Dif. R²: 0.0085
- Grado 5 - RMSE Train: 0.2567, R² Train: 0.4037, RMSE Test: 0.2687, R² Test: 0.3741, Dif. R²: 0.0295
- Grado 7 - RMSE Train: 0.2514, R² Train: 0.4277, RMSE Test: 0.2604, R² Test: 0.4121, Dif. R²: 0.0156
- Grado 9 - RMSE Train: 0.2415, R² Train: 0.4723, RMSE Test: 0.2470, R² Test: 0.4711, Dif. R²: 0.0012
- Grado 10 - RMSE Train: 0.2316, R² Train: 0.5144, RMSE Test: 0.2464, R² Test: 0.4737, Dif. R²: 0.0408
- Grado 11 - RMSE Train: 0.2293, R² Train: 0.5240, RMSE Test: 0.2495, R² Test: 0.4603, Dif. R²: 0.0637
- Mejor modelo: Grado 10 - R² Test: 0.4737

![Image](https://github.com/user-attachments/assets/09026bd9-0abe-42f0-b457-56655fa66b34)

![Image](https://github.com/user-attachments/assets/05314411-70cc-4d33-95ee-e1cf3f971143)

![Image](https://github.com/user-attachments/assets/909eb7e1-0773-426b-89c5-36cfe46d0967)


## ANÁLISIS DE CORRELACIÓN

```python

from scipy.stats import pearsonr, spearmanr
from statsmodels.tsa.stattools import coint, grangercausalitytests


df_churn["Tasa_Paro"] = pd.to_numeric(df_churn["Tasa_Paro"], errors="coerce")

if isinstance(df_churn['cohort_d'].dtype, pd.PeriodDtype):
    df_churn['cohort_d'] = df_churn['cohort_d'].dt.to_timestamp()
else:
    df_churn['cohort_d'] = pd.to_datetime(df_churn['cohort_d'])


# 1. Agrupar la Retention Rate a nivel mensual

df_monthly_ret = df_churn.groupby(pd.Grouper(key="cohort_d", freq="M")).agg(
    Mean_Retention_Rate=("Retention_Rate", "mean")
).reset_index()


# 2. Agrupar la Tasa de Paro a nivel mensual

df_monthly_paro = df_churn.groupby(pd.Grouper(key="cohort_d", freq="M")).agg(
    Mean_Tasa_Paro=("Tasa_Paro", "mean")
).reset_index()

# 3. Unir ambos DataFrames mensuales en uno solo

df_monthly_merged = pd.merge(df_monthly_ret, df_monthly_paro, on="cohort_d", how="inner")
df_monthly_merged = df_monthly_merged.dropna(subset=["Mean_Retention_Rate", "Mean_Tasa_Paro"])

if len(df_monthly_merged) < 3:
    print("No hay suficientes datos mensuales para realizar las pruebas estadísticas.")
else:

    # 4. Calcular Correlaciones Pearson y Spearman

    pear_corr, pear_pval = pearsonr(df_monthly_merged["Mean_Tasa_Paro"], df_monthly_merged["Mean_Retention_Rate"])
    spear_corr, spear_pval = spearmanr(df_monthly_merged["Mean_Tasa_Paro"], df_monthly_merged["Mean_Retention_Rate"])
        

    # 5. Prueba de Cointegración Engle-Granger

    score, p_value_coin, _ = coint(df_monthly_merged["Mean_Retention_Rate"], df_monthly_merged["Mean_Tasa_Paro"])
        

    # 6. Prueba de Causalidad de Granger (maxlag=2)

    df_for_granger = df_monthly_merged[["Mean_Retention_Rate", "Mean_Tasa_Paro"]].dropna()        

    # 7. Impresión de Interpretaciones y Conclusiones

    print("\n--- Análisis Correlación ---\n")
    print("1. CORRELACIÓN:")
    print(f"   - Pearson: {pear_corr:.3f} (p-value = {pear_pval:.4f})")
    print(f"   - Spearman: {spear_corr:.3f} (p-value = {spear_pval:.4f})")
    print("   Estos valores indican una relación estadísticamente significativa entre la Tasa de Paro y la Retention Rate.")
        
    print("\n2. COINTEGRACIÓN (Engle-Granger):")
    print(f"   - Estadístico: {score:.3f} | p-value = {p_value_coin:.4f}")
    if p_value_coin < 0.05:
        print("   => Las series podrían estar cointegradas, lo que indica una relación de equilibrio a largo plazo.")
    else:
        print("   => No hay evidencia estadística de cointegración entre las series.")
        
    print("\n3. CAUSALIDAD DE GRANGER:")
    if len(df_for_granger) > 5:
        print("   Se realizan pruebas de causalidad de Granger con maxlag=2:")
        granger_results = grangercausalitytests(df_for_granger, maxlag=2, verbose=True)
        # Revisa los p-values en la salida; p-values < 0.05 indican evidencia de causalidad.
    else:
        print("   No hay suficientes datos para realizar la prueba de causalidad de Granger.")       


    # 8. Visualización
  
    plt.figure(figsize=(8, 5))
    sns.regplot(
        x="Mean_Tasa_Paro",
        y="Mean_Retention_Rate",
        data=df_monthly_merged,
        scatter_kws={"alpha": 0.7},
        line_kws={"color": "red"},
    )
    plt.title("Relación entre Tasa de Paro y Retention Rate (Mensual)")
    plt.xlabel("Tasa de Paro (Promedio Mensual)")
    plt.ylabel("Retention Rate (Promedio Mensual)")
    plt.grid(alpha=0.3)
    plt.show()
```

#### OUTPUT
--- Análisis Correlación ---

1. CORRELACIÓN:
   - Pearson: -0.651 (p-value = 0.0160)
   - Spearman: -0.591 (p-value = 0.0332)
   Estos valores indican una relación estadísticamente significativa entre la Tasa de Paro y la Retention Rate.

2. COINTEGRACIÓN (Engle-Granger):
   - Estadístico: -1.986 | p-value = 0.5356
   => No hay evidencia estadística de cointegración entre las series.

3. CAUSALIDAD DE GRANGER:
   Se realizan pruebas de causalidad de Granger con maxlag=2:

Granger Causality
number of lags (no zero) 1
ssr based F test:         F=2.6450  , p=0.1383  , df_denom=9, df_num=1
ssr based chi2 test:   chi2=3.5266  , p=0.0604  , df=1
likelihood ratio test: chi2=3.0918  , p=0.0787  , df=1
parameter F test:         F=2.6450  , p=0.1383  , df_denom=9, df_num=1

Granger Causality
number of lags (no zero) 2
ssr based F test:         F=2.3357  , p=0.1777  , df_denom=6, df_num=2
ssr based chi2 test:   chi2=8.5644  , p=0.0138  , df=2
likelihood ratio test: chi2=6.3340  , p=0.0421  , df=2
parameter F test:         F=2.3357  , p=0.1777  , df_denom=6, df_num=2



![Image](https://github.com/user-attachments/assets/eb468269-47c6-4381-805e-5fc7cc736d6a)

# Análisis de Causalidad por Correlación

## 1. CORRELACIÓN

- **Pearson:**
  - Valor: **-0.651**
  - p-value: **0.0160**
  
- **Spearman:**
  - Valor: **-0.591**
  - p-value: **0.0332**

**Interpretación:**  
Ambos coeficientes indican una relación negativa moderada a fuerte entre la Tasa de Paro y la Retention Rate. Esto sugiere que, en promedio, a medida que la Tasa de Paro aumenta, la Retention Rate tiende a disminuir. Los p-values menores a 0.05 confirman que la relación es estadísticamente significativa y no se debe al azar.


## 2. COINTEGRACIÓN (Engle-Granger)

- **Estadístico:** **-1.986**
- **p-value:** **0.5356**

**Interpretación:**  
El p-value es muy alto (0.5356 > 0.05), lo que indica que no se encontró evidencia de cointegración entre la Tasa de Paro y la Retention Rate. Esto significa que, aunque las dos series muestran una correlación significativa, no se observa que se muevan conjuntamente de forma equilibrada a largo plazo.



## 3. CAUSALIDAD DE GRANGER

Se realizaron pruebas de causalidad de Granger considerando dos lags:

### Para 1 lag:
- **SSR-based F test:** F = 2.6450, p = 0.1383  
- **SSR-based chi² test:** chi² = 3.5266, p = 0.0604  
- **Likelihood ratio test:** chi² = 3.0918, p = 0.0787  
- **Parameter F test:** p = 0.1383

**Interpretación:**  
Para un lag, ninguno de los tests alcanza significancia al nivel del 5%. Esto indica que, si solo se considera el valor inmediatamente anterior, no hay evidencia suficiente de que la Tasa de Paro cause (en el sentido de Granger) la Retention Rate.

### Para 2 lags:
- **SSR-based F test:** F = 2.3357, p = 0.1777  
- **SSR-based chi² test:** chi² = 8.5644, p = 0.0138  
- **Likelihood ratio test:** chi² = 6.3340, p = 0.0421  
- **Parameter F test:** p = 0.1777

**Interpretación:**  
Cuando se consideran dos lags, dos de los tests (el SSR-based chi² test y el likelihood ratio test) arrojan p-values inferiores a 0.05, lo que sugiere que los valores pasados de la Tasa de Paro (hace dos periodos) tienen capacidad predictiva sobre la Retention Rate. Aunque los tests basados en F no muestran significancia, la evidencia en algunos tests respalda la hipótesis de causalidad de Granger a 2 lags.



## Conclusiones Globales

- **Correlación:**  
  Los resultados de Pearson y Spearman indican una relación negativa y estadísticamente significativa: cuando la Tasa de Paro aumenta, la Retention Rate disminuye.

- **Cointegración:**  
  La ausencia de cointegración (p-value alto) indica que, a largo plazo, las series no comparten una tendencia de equilibrio, lo que implica que se comportan de forma independiente en el largo plazo.

- **Causalidad de Granger:**  
  Aunque para 1 lag no se encontró evidencia significativa, al considerar 2 lags algunos tests sugieren que los valores pasados de la Tasa de Paro pueden predecir la Retention Rate. Esto aporta evidencia de una relación causal en el sentido de Granger, es decir, la Tasa de Paro posee capacidad predictiva sobre la Retention Rate cuando se consideran dos periodos de retraso.

### Interpretación Final

- **La correlación** es fuerte y significativa, lo que respalda la existencia de una relación inversa entre la Tasa de Paro y la Retention Rate.
- **La falta de cointegración** sugiere que, a pesar de la correlación, las dos series no mantienen un equilibrio conjunto a largo plazo.
- **La causalidad de Granger** (especialmente a 2 lags) indica que los valores pasados de la Tasa de Paro pueden predecir la Retention Rate, lo que respalda la hipótesis de una relación causal en el sentido estadístico.

**Conclusión:**  
Aunque la correlación no implica causalidad por sí sola, el conjunto de evidencias—correlación significativa, cointegración ausente y resultados mixtos en la prueba de Granger—sugiere que existe una relación causal (en términos de capacidad predictiva) entre la Tasa de Paro y la Retention Rate.

#### APUNTE RESPECTO LA POSIBILIDAD DE INTERPOLACIÓN Y EXTRAPOLACIÓN
Igualmente esta relación causal es relativa porque hay que tener en cuenta:

Este paro fue en el contexto de la pandemia, que es un fenómeno anómalo. También es anómala la tasa de paro tan elevada en EEUU, que acostumbra a ser siempre baja. Entonces hay que tener en cuenta muchos más factores. A lo mejor tendriamos que predecir pandemias. 

También paso el fenómeno de la gran renuncia. La gran dimisión o gran renuncia​​ es una dimisión laboral generalizada que comenzó en Estados Unidos en julio de 2020, tras la pandemia de COVID-19, cuando millones de estadounidenses insatisfechos con su trabajo o su salario renunciaron a él. Esta tendencia laboral comenzó a replicarse en el mundo.

Como el fenómeno que ha provocado esto es anómalo **(Black Swan Event)** es difícil de predecir y las gráficas con extrapolación de Fourier o Regresión polinómica no son válidas claramente. Porque ese escenario no se repetirá a menos que pase otra pandemia o catástrofe y entonces suba la tasa de paro. También es difícil de predecir el clima de hartazgo generalizado que provoco la gran renuncia. Aunque la interpolación nos puede ayudar a ver que pasa, la extrapolación en este caso puede ser inútil.















