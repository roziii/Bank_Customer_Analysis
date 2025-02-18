# Proyecto de Segmentación de Clientes

## Descripción del Proyecto

## Objetivos
- **Analizar el comportamiento transaccional**: Comprender cómo los clientes interactúan financieramente con el negocio.
- **Segmentar clientes**: Agrupar a los clientes en función de patrones en los pagos de tarifas y solicitudes de efectivo.
- **Derivar conocimientos prácticos**: Utilizar los resultados de la segmentación para optimizar la oferta de servicios y estrategias de marketing.

## Metodología
1. **Limpieza de Datos**: Eliminar duplicados, manejar valores faltantes y estandarizar formatos.
2. **Análisis Exploratorio de Datos (EDA)**: Identificar tendencias clave, patrones y anomalías.
3. **Ingeniería de Características**: Crear variables relevantes para mejorar los modelos de segmentación.
4. **Técnicas de Clustering**: Aplicar algoritmos como K-Means o Clustering Jerárquico para segmentación.
5. **Interpretación y Visualización**: Generar informes y paneles para facilitar la interpretación.

## Herramientas y Tecnologías
- **Python (Pandas, NumPy, Scikit-learn, Matplotlib, Seaborn)** para análisis de datos y aprendizaje automático.
- **Excel** para la revisión inicial de datos.
- **Jupyter Notebook** para análisis interactivo.El **Proyecto de Segmentación de Clientes** tiene como objetivo analizar y categorizar a los clientes en función de su comportamiento transaccional, tarifas y solicitudes de efectivo. Esta segmentación ayudará a obtener información valiosa para mejorar las estrategias comerciales, optimizar los servicios y mejorar la experiencia del cliente.


## Uso

Estos conjuntos de datos ayudan a analizar las tarifas impuestas en transacciones y a monitorear las solicitudes de efectivo para obtener mejores conocimientos financieros.
## Fuentes de Datos
El proyecto utiliza los siguientes conjuntos de datos:
1. **Extract - Fees - Data Analyst.csv**: Contiene datos sobre tarifas cobradas a los clientes.
2. **Extract - Cash Request - Data Analyst.csv**: Contiene datos sobre solicitudes de efectivo de los clientes.
3. **Lexique - Data Analyst.xlsx**: Proporciona un glosario de términos relevantes para los conjuntos de datos.

## Análisis Exploratorio de Datos (EDA)


### **Datos de Solicitudes de Efectivo** (23,970 registros, 16 columnas)
- **Estadísticas clave:**
  - Rastrea transacciones de solicitudes de efectivo, incluyendo estados, marcas de tiempo y detalles financieros.
  - Campos clave: `amount`, `status`, `transfer_type`, `reimbursement_date`.
- **Valores faltantes:**
  - `user_id`: 2,103 valores faltantes.
  - `moderated_at`: 7,935 valores faltantes.
  - `deleted_account_id`: 21,866 valores faltantes.
  - `cash_request_received_date`: 7,681 valores faltantes.
  - `money_back_date`: 7,427 valores faltantes.
  - `send_at`: 7,329 valores faltantes.
  - `recovery_status`, `reco_creation` y `reco_last_update`: Más de **20,000 valores faltantes** cada uno.

---

## Campos de Datos de Solicitudes de Efectivo
| Nombre de la Columna  | Descripción |
|-----------------------|-------------|
| **id** | Identificador único de la solicitud de efectivo. |
| **amount** | El monto total de la solicitud de efectivo. |
| **status** | Estado de la solicitud de efectivo. Los valores posibles incluyen: `approved`, `pending`, `rejected`. |
| **reason** | Si fue revisada manualmente y rechazada, este campo proporciona la razón. |
| **created_at** | La marca de tiempo cuando se creó la solicitud de efectivo. |
### Histograma de id:
![Image](https://github.com/user-attachments/assets/36a1d126-4091-4fa3-94c0-2a2d79c03420)
Representa la distribución de los identificadores únicos de solicitudes de efectivo.Se observa una distribución uniforme, lo que indica que los identificadores han sido generados de manera secuencial sin grandes vacíos o agrupaciones anómalas.

### Histograma de amount (Monto de Solicitud de Efectivo):
![Image](https://github.com/user-attachments/assets/889b56fd-0ba1-47cf-8c54-db295ded7b98)
Se identifican picos pronunciados en valores específicos, especialmente en 50 y 100 unidades.
Esto sugiere que los usuarios suelen solicitar montos estandarizados, posiblemente debido a restricciones del sistema o preferencias comunes.

### Histograma de user_id:
![Image](https://github.com/user-attachments/assets/8ed86429-4268-4042-820e-09bc34d39214)
Muestra la distribución de solicitudes de efectivo por usuario.
Se observa que ciertos rangos de usuarios han realizado más solicitudes que otros, lo que podría indicar diferentes patrones de uso entre segmentos de usuarios.
### Histograma De Deleted_account_id:
![output (5)](https://github.com/user-attachments/assets/03c6acc5-277a-4c03-9aa9-be4ee40cabab)
Representa la distribución de los identificadores únicos de solicitudes de efectivo.Se observa una distribución uniforme, lo que indica que los identificadores han sido generados de manera secuencial sin grandes vacíos o agrupaciones anómalas.
### Box Plot de amount (Monto de Solicitud de Efectivo):
![Image](https://github.com/user-attachments/assets/ac691b3e-53e8-482e-b2c2-b3b88727fa03)
La mediana se encuentra dentro de un rango estándar, lo que indica que la mayoría de las solicitudes tienen montos similares.Múltiples valores atípicos sugieren que algunos usuarios han solicitado montos significativamente más altos o más bajos que el promedio.El rango intercuartílico (IQR) muestra que la mayoría de las transacciones se mantienen dentro de un límite específico, con algunas excepciones.

### Box Plot de user_id (ID de Usuario):
![Image](https://github.com/user-attachments/assets/c8c493a4-fdf7-4363-bf7b-a6a3456b35b3)
Representa la distribución de las solicitudes de efectivo entre los distintos usuarios.Los valores atípicos sugieren que algunos usuarios han realizado muchas más solicitudes que otros.Esto puede indicar la presencia de usuarios altamente activos o patrones de uso inusuales.

### Box Plot de deleted_account_id (ID de Cuenta Eliminada):
![Image](https://github.com/user-attachments/assets/e7ea246c-0e35-43db-ba54-83af75bb7deb)
La mayoría de los valores están en un rango pequeño, pero algunos valores atípicos muestran cuentas que realizaron muchas solicitudes antes de ser eliminadas.Puede ayudar a identificar tendencias en la eliminación de cuentas con un historial elevado de solicitudes de efectivo.

### Violin Plot de amount (Monto de Solicitud de Efectivo):
![Image](https://github.com/user-attachments/assets/a94a1025-7c08-4044-a724-274e8d81e83f)
Muestra la distribución y densidad de los montos solicitados. Se observa que la mayoría de las solicitudes se concentran en ciertos valores específicos, con algunos extremos dispersos. La forma del gráfico sugiere que hay valores atípicos, con montos mucho más altos o bajos que la mayoría.


### Violin Plot de user_id (ID de Usuario):
![Image](https://github.com/user-attachments/assets/3c0b68a5-2d1e-43ee-a4bf-c24d39b121ab)
Representa la frecuencia y distribución de usuarios que han realizado solicitudes de efectivo. Existen usuarios con una alta concentración de solicitudes, mientras que otros tienen una presencia mínima. La distribución no es uniforme, lo que indica que ciertos usuarios son más activos en la plataforma que otros.

Violin Plot de deleted_account_id (ID de Cuenta Eliminada):
![Image](https://github.com/user-attachments/assets/4ec1cf64-ff8b-466f-8ec1-cc3720a7cfd3)
La forma del gráfico muestra que la mayoría de los datos están concentrados en valores bajos, con algunos valores atípicos elevados. Esto podría indicar que ciertas cuentas realizaron múltiples solicitudes antes de ser eliminadas.

### **Datos de Tarifas** (21,061 registros, 13 columnas)
- **Estadísticas clave:**
  - Contiene transacciones de tarifas con tipos, estados, categorías y marcas de tiempo.
  - Campos clave: `total_amount`, `status`, `category`, `charge_moment`.
- **Valores faltantes:**
  - `category`: 18,865 valores faltantes.
  - `paid_at`: 5,530 valores faltantes.
  - `from_date` y `to_date`: 13,295 valores faltantes cada uno.
  - `cash_request_id`: 4 valores faltantes.


## Campos de Datos de Tarifas
| Nombre de la Columna  | Descripción |
|-----------------------|-------------|
| **id** | Identificador único del objeto de tarifa. |
| **type** | Tipo de tarifa. Los valores posibles incluyen: `instant_fee`, `recurring_fee`, `one_time_fee`, etc. |
| **status** | Estado de la tarifa, que indica si fue procesada con éxito, falló o está pendiente. |
| **category** | Describe la razón detrás de la tarifa del incidente. Ejemplos de valores pueden ser `late_payment`, `service_charge`, etc. |
| **reason** | Descripción adicional de la tarifa específica. |

---

### Histograma De Id:
![Image](https://github.com/user-attachments/assets/ad740798-c172-4ced-a961-843d96dcf11a)
Representa la distribución de los identificadores únicos de tarifas.
La distribución es uniforme, lo que indica que los registros se generan de manera secuencial sin grandes variaciones en la cantidad de tarifas procesadas a lo largo del tiempo.

### Histograma De Cash_request_id:
![Image](https://github.com/user-attachments/assets/4b1f62dd-7905-438d-92e3-51d31e599b96)
Muestra la relación entre las tarifas y las solicitudes de efectivo asociadas.
Se observan ciertos picos en valores específicos, lo que puede indicar que algunas solicitudes están vinculadas a múltiples tarifas.

### Histograma de total_amount (Monto Total de la Tarifa):
![Image](https://github.com/user-attachments/assets/f103bc72-d7cb-412e-b57e-4b6d45eef7cc)
La distribución está altamente concentrada en un solo valor, con muy poca variabilidad.Esto sugiere que la mayoría de las tarifas tienen un monto estandarizado, con muy pocas variaciones en los valores aplicados.

### Box Plot de total_amount (Monto Total de la Tarifa):
![Image](https://github.com/user-attachments/assets/ccd86298-1b56-4604-9b1f-8d3d01c67ffd)
La mediana se encuentra en un rango específico, lo que indica que la mayoría de las tarifas tienen montos similares. Se observan valores atípicos que sugieren la presencia de tarifas excepcionalmente altas en comparación con la mayoría.

### Box Plot de cash_request_id (ID de Solicitud de Efectivo Asociada a la Tarifa):
![Image](https://github.com/user-attachments/assets/e19d961d-93b9-45c9-808e-253d50ad949d)


### Distribución de Categorías en Tarifas y Solicitudes de Efectivo
![Image](https://github.com/user-attachments/assets/aff0aec9-8320-4ff0-9c8c-f0f1ba2a51bd)

![Image](https://github.com/user-attachments/assets/2bfa8358-8db0-4a1c-b287-1007b325639e)

![Image](https://github.com/user-attachments/assets/ae04a9f8-7eb1-496e-b1df-f3a1ba9dc761)

Estos gráficos de pastel representan la distribución de algunas variables categóricas en las tarifas y solicitudes de efectivo.

Distribución del Tipo de Transferencia (transfer_type)

57.9% de las transferencias son instantáneas, mientras que 42.1% son regulares.
Esto indica una preferencia mayor por las transferencias rápidas, aunque una cantidad considerable de usuarios sigue usando transferencias estándar.
Distribución del Estado de Recuperación (recovery_status)

74.1% de los casos están en estado "completed", lo que significa que la mayoría de las recuperaciones de efectivo se han realizado con éxito.
25.4% siguen pendientes, lo que sugiere que hay una cantidad considerable de casos en espera de procesamiento.
Los estados "pending_direct_debit" y "cancelled" representan menos del 1%, lo que indica que las cancelaciones o demoras en débitos automáticos son poco comunes.
Distribución del Estado de Solicitudes (status)

68.4% de las solicitudes resultan en "money_back", indicando que la mayoría de las solicitudes de efectivo son exitosas.
27.4% han sido rechazadas, lo que podría deberse a restricciones del sistema o incumplimientos por parte del usuario.
Otras categorías como "direct_debit_rejected", "active", "transaction_declined" y "canceled" tienen poca representación, pero reflejan situaciones específicas dentro del sistema


### Analysis de Cohortes:
Para verificar las cohortes, primero creamos la tasa de retención según el mes en que el cliente presentó su solicitud por primera vez y luego verificamos el comportamiento de los clientes en los meses siguientes para poder obtener un modelo lineal adecuado para la predicción. Al principio, comenzamos con un patrón lineal simple y luego utilizamos diferentes métodos para mejorar este patrón, incluidos cresta, lazo, elástico y transformada de Fourier. El criterio de desempeño de cada modelo fue la diferencia entre las puntuaciones R2 de los grupos de prueba y entrenamiento. 
![download](https://github.com/user-attachments/assets/21d45ea9-bfcf-487d-a030-645e9c54f582)

#### Linear Regression
``` python 
def evaluate_polynomial_regression(df, feature_col, target_col, poly_degree=[1, 2, 3, 4, 5],extrapolation_size=0.2 , random_state=42):
    """
    Trains and evaluates Polynomial Linear Regression models for different degrees.
    Splits data into interpolation and extrapolation sets, computes metrics, and plots results.

    Parameters:
        df (DataFrame): Data containing 'Month_Num' and 'Retention_Rate' columns.
        poly_degree (list): List of polynomial degrees to test.
        random_state (int): Seed for reproducibility.

    Returns:
        Best linear model (Plots results and prints performance metrics)
    """
    df_train = df.dropna()  # Remove NaN values

    # Convert to numpy arrays
    X = np.array(df_train[feature_col]).reshape(-1, 1)
    y = np.array(df_train[target_col])
    
    # Split dataset into 80% interpolation and 20% extrapolation
    X_interp, X_extrap, y_interp, y_extrap = train_test_split(X, y, test_size=extrapolation_size, random_state=random_state, shuffle=False)
    X_train, X_test, y_train, y_test = train_test_split(X_interp, y_interp, test_size=0.2, shuffle=True, random_state=random_state)

    best_linear_model = None
    best_linear_mse = float('inf')
    best_degree = 0 
    best_r2_gap = float('inf')
    # Loop through each polynomial degree
    for degree in poly_degree:
        # Create and fit the Linear Regression Model with Polynomial Features
        linear_model = make_pipeline(PolynomialFeatures(degree), LinearRegression())
        linear_model.fit(X_train, y_train)  # Train model
    
        # Predictions
        linear_train_preds = linear_model.predict(X_train)
        linear_test_preds = linear_model.predict(X_test)
        
        linear_y_predict_interp = linear_model.predict(X_interp)
        linear_y_predict_extra = linear_model.predict(X_extrap)


        # Compute residual for confidence intervals
        
        linear_residuals = y_interp - linear_y_predict_interp

        # Standard deviation of residuals
        linear_std = np.std(linear_residuals)

        # 95% confidence interval (1.96 * std)
        linear_ci = 1.96 * linear_std
        
    
        # Compute Performance Metrics
        linear_mse_train = mean_squared_error(y_train, linear_train_preds)
        linear_mse_test = mean_squared_error(y_test, linear_test_preds)
        R2_sklearn_train = r2_score(y_train, linear_train_preds)
        R2_sklearn_test = r2_score(y_test, linear_test_preds)
        r2_score_gap_linear = R2_sklearn_train - R2_sklearn_test
        R2_sklearn_extrap = r2_score(y_extrap, linear_y_predict_extra)
        
        if  r2_score_gap_linear < best_r2_gap:
            best_linear_model = linear_model
            best_degree = degree
            best_r2_gap = r2_score_gap_linear

        
        # Plot results for this degree
        plt.figure(figsize=(10, 6))
        plt.scatter(X_train, y_train, label='Train (Interpolation)', color='blue', alpha=0.5)
        plt.scatter(X_test, y_test, label='Test (Interpolation)', color='green', alpha=0.5)
        plt.scatter(X_extrap, y_extrap, label='Extrapolation Real', color='orange', alpha=0.5)
        plt.plot(X_interp, linear_y_predict_interp, label='Linear Trend (Interpolation)', color='red', linewidth=2)
        plt.plot(X_extrap, linear_y_predict_extra, label='Linear Trend (Extrapolation)', color='gray', linewidth=2)
    
        # Add standard deviation bands
        plt.fill_between(X_interp.flatten(), linear_y_predict_interp - linear_ci, linear_y_predict_interp + linear_ci, color='red', alpha=0.2)
        plt.fill_between(X_extrap.flatten(), linear_y_predict_extra - linear_ci, linear_y_predict_extra + linear_ci, color='gray', alpha=0.2)
    
        plt.title(f'Linear Regression (Degree {degree})')
        plt.legend()
        plt.show()
    
        # Print Performance Metrics
        print(f"Degree {degree}:")
        print(f" - Train MSE: {linear_mse_train:.4f}")
        print(f" - Test MSE: {linear_mse_test:.4f}")
        print(f" - R² Score (Train): {R2_sklearn_train:.4f}")
        print(f" - R² Score (Test): {R2_sklearn_test:.4f}")
        print(f"The gap between the R2 scores of train and test is: {r2_score_gap_linear:.4f}")
        print("=" * 60)
        
    print(f'The best linear regression model was for  {best_degree} degree')
    return best_linear_model

```
---
#### Polynomial Regression Con Ridge, Lasso i Elastic Regression
``` python
def evaluate_regression_models(df, feature_col, target_col, poly_degrees=[1, 2, 3, 4, 5], 
                              alpha_values=[0.001, 0.01, 0.1, 1, 1.1, 1.5, 2], 
                              n_splits=5, random_state=42, test_size=0.2, extrapolation_size=0.2, l1_ratio=0.5):
    """
    Evaluate Ridge, Lasso, ElasticNet, and Linear Regression models with polynomial features.
    Includes 95% confidence intervals in the plots.

    Parameters:
    - df: pandas DataFrame, the dataset containing the feature and target columns.
    - feature_col: str, the name of the feature column.
    - target_col: str, the name of the target column.
    - poly_degrees: list, polynomial degrees to test.
    - alpha_values: list, regularization strengths for Ridge, Lasso, and ElasticNet.
    - n_splits: int, number of folds for cross-validation.
    - random_state: int, random seed for reproducibility.
    - test_size: float, proportion of the dataset to include in the test split.
    - extrapolation_size: float, proportion of the dataset to include in the extrapolation split.
    - l1_ratio: float, the mixing parameter for ElasticNet (0 <= l1_ratio <= 1).

    Returns:
    - Best predictive model (plots and prints evaluation metrics).
    """
    
    # Remove NaN values
    df_train = df.dropna()

    # Convert to numpy arrays
    X = np.array(df_train[feature_col]).reshape(-1, 1)
    y = np.array(df_train[target_col])

    # Split dataset into interpolation and extrapolation sets
    X_interp, X_extrap, y_interp, y_extrap = train_test_split(X, y, test_size=extrapolation_size, 
                                                              random_state=random_state, shuffle=False)
    X_train, X_test, y_train, y_test = train_test_split(X_interp, y_interp, test_size=test_size, 
                                                        shuffle=True, random_state=random_state)

    # K-Fold Cross Validation
    kf = KFold(n_splits=n_splits, shuffle=True, random_state=random_state)
    best_model = None 
    # Loop through each polynomial degree
    for degree in poly_degrees:
        best_ridge_alpha, best_lasso_alpha, best_elastic_alpha = None, None, None
        best_ridge_model, best_lasso_model, best_elastic_model, best_linear_model = None, None, None, None
        best_ridge_mse, best_lasso_mse, best_elastic_mse, best_linear_mse = float('inf'), float('inf'), float('inf'), float('inf')
        best_r2score = float('inf')
        best_r2_gap = float('inf')

        # Loop through different alpha values for Ridge, Lasso, and ElasticNet
        for alpha in alpha_values:
            # Ridge Regression
            ridge_model = make_pipeline(PolynomialFeatures(degree), Ridge(alpha=alpha))
            ridge_preds = cross_val_predict(ridge_model, X_train, y_train, cv=kf)
            ridge_mse = mean_squared_error(y_train, ridge_preds)

            if ridge_mse < best_ridge_mse:
                best_ridge_mse = ridge_mse
                best_ridge_alpha = alpha
                best_ridge_model = ridge_model

            # Lasso Regression
            lasso_model = make_pipeline(PolynomialFeatures(degree), Lasso(alpha=alpha, max_iter=10000))
            lasso_preds = cross_val_predict(lasso_model, X_train, y_train, cv=kf)
            lasso_mse = mean_squared_error(y_train, lasso_preds)

            if lasso_mse < best_lasso_mse:
                best_lasso_mse = lasso_mse
                best_lasso_alpha = alpha
                best_lasso_model = lasso_model

            # ElasticNet Regression
            elastic_model = make_pipeline(PolynomialFeatures(degree), ElasticNet(alpha=alpha, l1_ratio=l1_ratio, max_iter=10000))
            elastic_preds = cross_val_predict(elastic_model, X_train, y_train, cv=kf)
            elastic_mse = mean_squared_error(y_train, elastic_preds)

            if elastic_mse < best_elastic_mse:
                best_elastic_mse = elastic_mse
                best_elastic_alpha = alpha
                best_elastic_model = elastic_model

        # Linear Regression (No Regularization)
        linear_model = make_pipeline(PolynomialFeatures(degree), LinearRegression())
        linear_preds = cross_val_predict(linear_model, X_train, y_train, cv=kf)
        best_linear_mse = mean_squared_error(y_train, linear_preds)
        best_linear_model = linear_model

        # Train the best models for this degree
        best_ridge_model.fit(X_interp, y_interp)
        best_lasso_model.fit(X_interp, y_interp)
        best_elastic_model.fit(X_interp, y_interp)
        best_linear_model.fit(X_interp, y_interp)
        # Predictions
        ridge_y_predict_interp = best_ridge_model.predict(X_interp)
        ridge_y_predict_extrap = best_ridge_model.predict(X_extrap)
        lasso_y_predict_interp = best_lasso_model.predict(X_interp)
        lasso_y_predict_extrap = best_lasso_model.predict(X_extrap)
        elastic_y_predict_interp = best_elastic_model.predict(X_interp)
        elastic_y_predict_extrap = best_elastic_model.predict(X_extrap)
        linear_y_predict_interp = best_linear_model.predict(X_interp)
        linear_y_predict_extrap = best_linear_model.predict(X_extrap)
        
        ridge_y_predict_train = best_ridge_model.predict(X_train)
        ridge_y_predict_test = best_ridge_model.predict(X_test)
        lasso_y_predict_train = best_lasso_model.predict(X_train)
        lasso_y_predict_test = best_lasso_model.predict(X_test)
        elastic_y_predict_train = best_elastic_model.predict(X_train)
        elastic_y_predict_test = best_elastic_model.predict(X_test)
        linear_y_predict_train = best_linear_model.predict(X_train)
        linear_y_predict_test = best_linear_model.predict(X_test)

        # Compute residuals for confidence intervals
        ridge_residuals = y_interp - ridge_y_predict_interp
        lasso_residuals = y_interp - lasso_y_predict_interp
        elastic_residuals = y_interp - elastic_y_predict_interp
        linear_residuals = y_interp - linear_y_predict_interp

        # Standard deviation of residuals
        ridge_std = np.std(ridge_residuals)
        lasso_std = np.std(lasso_residuals)
        elastic_std = np.std(elastic_residuals)
        linear_std = np.std(linear_residuals)

        # 95% confidence interval (1.96 * std)
        ridge_ci = 1.96 * ridge_std
        lasso_ci = 1.96 * lasso_std
        elastic_ci = 1.96 * elastic_std
        linear_ci = 1.96 * linear_std

        # Compute R² Scores for Extrapolation
        R2_ridge_gap_train = r2_score(y_train, ridge_y_predict_train)
        R2_ridge_gap_test = r2_score(y_test, ridge_y_predict_test)
        R2_ridge_gap = R2_ridge_gap_train - R2_ridge_gap_test
        if R2_ridge_gap < best_r2_gap:
            best_model = best_ridge_model
            best_r2score = R2_ridge_gap

        
        R2_lasso_gap_train = r2_score(y_train, lasso_y_predict_train)
        R2_lasso_gap_test = r2_score(y_test, lasso_y_predict_test)
        R2_lasso_gap = R2_lasso_gap_train - R2_lasso_gap_test
        if R2_lasso_gap < best_r2_gap:
            best_model = best_lasso_model
            best_r2score = R2_lasso_gap

        
        R2_elastic_gap_train = r2_score(y_train, elastic_y_predict_train)
        R2_elastic_gap_test = r2_score(y_test, elastic_y_predict_test)
        R2_elastic_gap= R2_elastic_gap_train - R2_elastic_gap_test
        if R2_elastic_gap < best_r2_gap:
            best_model = best_elastic_model
            best_r2score = R2_elastic_gap
            
        
        R2_linear_gap_train = r2_score(y_train, linear_y_predict_train)
        R2_linear_gap_test = r2_score(y_test, linear_y_predict_test)
        R2_linear_gap = R2_linear_gap_train - R2_linear_gap_test
        if R2_linear_gap < best_r2_gap:
            best_model = best_linear_model
            best_r2score = R2_linear_gap

        
        # Plot Results
        plt.figure(figsize=(24, 5))

        # Ridge Plot
        plt.subplot(1, 4, 1)
        plt.scatter(X_train, y_train, label='Train', color='blue', alpha=0.5)
        plt.scatter(X_test, y_test, label='Test', color='green', alpha=0.5)
        plt.scatter(X_extrap, y_extrap, label='Extrapolation', color='orange', alpha=0.5)
        plt.plot(X_interp, ridge_y_predict_interp, label='Ridge (Interp)', color='red', linewidth=2)
        plt.fill_between(X_interp.flatten(), ridge_y_predict_interp - ridge_ci, ridge_y_predict_interp + ridge_ci, 
                         color='red', alpha=0.2, label='95% CI')
        plt.plot(X_extrap, ridge_y_predict_extrap, label='Ridge (Extrap)', color='gray', linewidth=2)
        plt.fill_between(X_extrap.flatten(), ridge_y_predict_extrap - ridge_ci, ridge_y_predict_extrap + ridge_ci, 
                         color='gray', alpha=0.2)
        plt.title(f'Ridge (Degree {degree}, α={best_ridge_alpha})')
        plt.legend()

        # Lasso Plot
        plt.subplot(1, 4, 2)
        plt.scatter(X_train, y_train, label='Train', color='blue', alpha=0.5)
        plt.scatter(X_test, y_test, label='Test', color='green', alpha=0.5)
        plt.scatter(X_extrap, y_extrap, label='Extrapolation', color='orange', alpha=0.5)
        plt.plot(X_interp, lasso_y_predict_interp, label='Lasso (Interp)', color='red', linewidth=2)
        plt.fill_between(X_interp.flatten(), lasso_y_predict_interp - lasso_ci, lasso_y_predict_interp + lasso_ci, 
                         color='red', alpha=0.2, label='95% CI')
        plt.plot(X_extrap, lasso_y_predict_extrap, label='Lasso (Extrap)', color='gray', linewidth=2)
        plt.fill_between(X_extrap.flatten(), lasso_y_predict_extrap - lasso_ci, lasso_y_predict_extrap + lasso_ci, 
                         color='gray', alpha=0.2)
        plt.title(f'Lasso (Degree {degree}, α={best_lasso_alpha})')
        plt.legend()

        # ElasticNet Plot
        plt.subplot(1, 4, 3)
        plt.scatter(X_train, y_train, label='Train', color='blue', alpha=0.5)
        plt.scatter(X_test, y_test, label='Test', color='green', alpha=0.5)
        plt.scatter(X_extrap, y_extrap, label='Extrapolation', color='orange', alpha=0.5)
        plt.plot(X_interp, elastic_y_predict_interp, label='ElasticNet (Interp)', color='red', linewidth=2)
        plt.fill_between(X_interp.flatten(), elastic_y_predict_interp - elastic_ci, elastic_y_predict_interp + elastic_ci, 
                         color='red', alpha=0.2, label='95% CI')
        plt.plot(X_extrap, elastic_y_predict_extrap, label='ElasticNet (Extrap)', color='gray', linewidth=2)
        plt.fill_between(X_extrap.flatten(), elastic_y_predict_extrap - elastic_ci, elastic_y_predict_extrap + elastic_ci, 
                         color='gray', alpha=0.2)
        plt.title(f'ElasticNet (Degree {degree}, α={best_elastic_alpha})')
        plt.legend()

        # Linear Regression Plot
        plt.subplot(1, 4, 4)
        plt.scatter(X_train, y_train, label='Train', color='blue', alpha=0.5)
        plt.scatter(X_test, y_test, label='Test', color='green', alpha=0.5)
        plt.scatter(X_extrap, y_extrap, label='Extrapolation', color='orange', alpha=0.5)
        plt.plot(X_interp, linear_y_predict_interp, label='Linear (Interp)', color='red', linewidth=2)
        plt.fill_between(X_interp.flatten(), linear_y_predict_interp - linear_ci, linear_y_predict_interp + linear_ci, 
                         color='red', alpha=0.2, label='95% CI')
        plt.plot(X_extrap, linear_y_predict_extrap, label='Linear (Extrap)', color='gray', linewidth=2)
        plt.fill_between(X_extrap.flatten(), linear_y_predict_extrap - linear_ci, linear_y_predict_extrap + linear_ci, 
                         color='gray', alpha=0.2)
        plt.title(f'Linear Regression (Degree {degree})')
        plt.legend()

        plt.tight_layout()
        plt.show()

        print(f"Degree {degree}:")
        print(f"  Ridge (α={best_ridge_alpha}, MSE={best_ridge_mse:.4f}), ΔR² train & test={R2_ridge_gap:.4f}")
        print(f"  Lasso (α={best_lasso_alpha}, MSE={best_lasso_mse:.4f}), ΔR² train & test={R2_lasso_gap:.4f}")
        print(f"  ElasticNet (α={best_elastic_alpha}, MSE={best_elastic_mse:.4f}), ΔR² train & test={R2_elastic_gap:.4f}")
        print(f"  Linear Regression (MSE={best_linear_mse:.4f}), ΔR² train & test={R2_linear_gap:.4f}")
        print()  
    return best_model

```
#### Fourier Transform 
``` python 
def fourier_transform(X, N=5):
    """Transforms X into Fourier basis functions up to N terms."""
    X = X.flatten()
    features = [np.ones_like(X)]  # Intercept term (bias term a0)
    
    for n in range(1, N + 1):
        features.append(np.sin(n * X))  # Sine terms
        features.append(np.cos(n * X))  # Cosine terms
    
    return np.column_stack(features)
```
``` python
def evaluate_best_fourier_model(df, feature_col, target_col, max_fourier_terms=5, random_state=42):
    """
    Evaluates Fourier models with different numbers of terms and selects the best based on extrapolation R².

    Parameters:
        df (DataFrame): Input dataset.
        feature_col (str): Column name of the feature (X).
        target_col (str): Column name of the target (Y).
        max_fourier_terms (int): Maximum number of Fourier terms to test.
        random_state (int): Random seed for reproducibility.

    Returns:
        best_model: The best Fourier model.
    """
    df = df.dropna()

    # Prepare the data
    X = np.array(df[feature_col]).reshape(-1, 1)
    y = np.array(df[target_col])

    # Split the data: 80% for interpolation, 20% for extrapolation
    X_interp, X_extrap, y_interp, y_extrap = train_test_split(
        X, y, test_size=0.2, random_state=random_state, shuffle=False
    )
    X_train, X_test, y_train, y_test = train_test_split(
        X_interp, y_interp, test_size=0.2, shuffle=True, random_state=random_state
    )

    # Initialize variables to track the best model
    best_model = None
    best_r2_extrap = -np.inf
    best_r2_gap = float('inf')
    best_predictions = None
    best_confidence_interval = None
    best_N = -1

    for N in range(1, max_fourier_terms + 1):
        # Define the Fourier feature transformer
        fourier_transformer = FunctionTransformer(lambda x: fourier_transform(x, N=N), validate=True)

        # Create and fit the Fourier model
        fourier_model = make_pipeline(fourier_transformer, LinearRegression())
        fourier_model.fit(X_train, y_train)

        # Predict for train, test, and extrapolation sets
        y_train_pred = fourier_model.predict(X_train)
        y_test_pred = fourier_model.predict(X_test)
        y_extrap_pred = fourier_model.predict(X_extrap)

        # Calculate R² scores
        R2_train = r2_score(y_train, y_train_pred)
        R2_test = r2_score(y_test, y_test_pred)
        R2_extrap = r2_score(y_extrap, y_extrap_pred)
        R2_gap = R2_train - R2_test  # Measure overfitting

        # Update the best model if it has better extrapolation R²
        if R2_extrap > best_r2_extrap or (R2_extrap == best_r2_extrap and R2_gap < best_r2_gap):
            best_model = fourier_model
            best_r2_extrap = R2_extrap
            best_r2_gap = R2_gap
            best_predictions = (fourier_model.predict(X_interp), y_extrap_pred)
            best_confidence_interval = 1.96 * np.std(y_interp - best_predictions[0])  # 95% CI
            best_N = N

    # Visualization
    best_interp_pred, best_extrap_pred = best_predictions
    plt.figure(figsize=(10, 6))
    plt.scatter(X_train, y_train, label='Train (Interpolation)', color='blue', alpha=0.5)
    plt.scatter(X_test, y_test, label='Test (Interpolation)', color='green', alpha=0.5)
    plt.scatter(X_extrap, y_extrap, label='Extrapolation Real', color='orange', alpha=0.5)
    plt.plot(X_interp, best_interp_pred, label='Interpolation Prediction', color='red', linewidth=2)
    plt.plot(X_extrap, best_extrap_pred, label='Extrapolation Prediction', color='gray', linewidth=2)

    if best_confidence_interval is not None:
        plt.fill_between(X_interp.flatten(), best_interp_pred - best_confidence_interval, best_interp_pred + best_confidence_interval, color='red', alpha=0.2)
        plt.fill_between(X_extrap.flatten(), best_extrap_pred - best_confidence_interval, best_extrap_pred + best_confidence_interval, color='gray', alpha=0.2)

    plt.title(f'Best Fourier Model: N={best_N}')
    plt.legend()
    plt.show()

    print(f"\n Best Fourier Model: N={best_N} with Extrapolation R² = {best_r2_extrap:.4f} and ΔR² = {best_r2_gap:.4f}\n")

    return best_model
```
![download](https://github.com/user-attachments/assets/647769ee-0266-417e-a0f1-e10c15a50c3b)
Degree 1:
 - Train MSE: 0.0427
 - Test MSE: 0.0257
 - R² Score (Train): 0.4321
 - R² Score (Test): -20.3091
The gap between the R2 scores of train and test is: 20.7412
============================================================

![download](https://github.com/user-attachments/assets/d161115b-8c2b-45bf-af8e-8b65af105179)
Degree 2:
 - Train MSE: 0.0122
 - Test MSE: 0.0447
 - R² Score (Train): 0.8379
 - R² Score (Test): -36.0504
The gap between the R2 scores of train and test is: 36.8883
============================================================

![download](https://github.com/user-attachments/assets/3c000eea-4eed-4042-addb-ca82cf0eb2e0)
Degree 3:
 - Train MSE: 0.0013
 - Test MSE: 0.0101
 - R² Score (Train): 0.9827
 - R² Score (Test): -7.3389
The gap between the R2 scores of train and test is: 8.3217
============================================================

![download](https://github.com/user-attachments/assets/454db1bf-2e0d-47d1-9144-1274b13f32bb)
Degree 4:
 - Train MSE: 0.0003
 - Test MSE: 0.0019
 - R² Score (Train): 0.9963
 - R² Score (Test): -0.6002
The gap between the R2 scores of train and test is: 1.5965
============================================================
![download](https://github.com/user-attachments/assets/e0b8c66f-3722-4a79-afa7-92d8508cfc94)
Degree 5:
 - Train MSE: 0.0000
 - Test MSE: 0.0040
 - R² Score (Train): 1.0000
 - R² Score (Test): -2.3229
The gap between the R2 scores of train and test is: 3.3229
============================================================

![download](https://github.com/user-attachments/assets/f2cb67f2-153e-41c9-8fcc-44765efe9d95)

Degree 1:
  Ridge (α=2, MSE=0.1427), ΔR² train & test=9.7757
  Lasso (α=1, MSE=0.1024), ΔR² train & test=1.5287
  ElasticNet (α=1.5, MSE=0.1024), ΔR² train & test=1.5287
  Linear Regression (MSE=0.1498), ΔR² train & test=10.7835

============================================================

![download](https://github.com/user-attachments/assets/b140a9b1-ce03-4a91-b49b-627d800bf45d)
Degree 2:
  Ridge (α=1, MSE=0.1269), ΔR² train & test=13.3308
  Lasso (α=2, MSE=0.1128), ΔR² train & test=1.3239
  ElasticNet (α=2, MSE=0.1193), ΔR² train & test=2.2173
  Linear Regression (MSE=0.1555), ΔR² train & test=20.3391

![download](https://github.com/user-attachments/assets/25062774-d010-423a-8f54-9da337f1e212)
Degree 3:
  Ridge (α=0.01, MSE=0.1333), ΔR² train & test=4.1866
  Lasso (α=0.001, MSE=0.1227), ΔR² train & test=4.1847
  ElasticNet (α=0.001, MSE=0.1230), ΔR² train & test=4.1626
  Linear Regression (MSE=0.2401), ΔR² train & test=3.9812
  
  ![download](https://github.com/user-attachments/assets/d8efdafe-e531-475e-a154-72ee5d390d62)
Degree 4:
  Ridge (α=2, MSE=0.1284), ΔR² train & test=12.9431
  Lasso (α=0.001, MSE=0.1241), ΔR² train & test=4.7568
  ElasticNet (α=0.01, MSE=0.1245), ΔR² train & test=9.0555
  Linear Regression (MSE=0.1870), ΔR² train & test=0.3951
  
![download](https://github.com/user-attachments/assets/84db0e6a-a630-45e7-bb06-2d795cf28433)
Degree 5:
  Ridge (α=0.1, MSE=0.1784), ΔR² train & test=4.8118
  Lasso (α=0.1, MSE=0.1330), ΔR² train & test=15.0556
  ElasticNet (α=0.01, MSE=0.1355), ΔR² train & test=9.0731
  Linear Regression (MSE=0.2233), ΔR² train & test=0.0335

![download](https://github.com/user-attachments/assets/d8efef87-fc6a-4a09-b39b-dcba0738c14b)
Best Fourier Model: N=1 with Extrapolation R² = 0.6605 and ΔR² = 21.6003

## Segmentación de clientes
Para segmentar a los clientes, primero creamos un nuevo campo llamado segmento de gasto y los clientes se dividieron según el monto en el campo de monto.
``` python
# Create 3 group based on the spenditures
bins =[cash_request['amount'].min(), 50, 150, cash_request['amount'].max()]
labels=['Low', 'Medium', 'High']
merged_df['spend_segment'] = pd.cut(cash_request['amount'], bins=bins, labels=labels, include_lowest=True)



# Perform cohort analysis for each spending segment
cohort_pivot_spend = merged_df.pivot_table(index=['Month_cash_request', ],
                                                 columns='spend_segment',
                                                 values='user_id',
                                                 aggfunc='nunique')
cohort_pivot_spend
```
![image](https://github.com/user-attachments/assets/ca2c7c7d-58f5-4675-b09f-ba82a584d570)
![download](https://github.com/user-attachments/assets/17f3cb22-4c5a-44ed-b1db-dc5c6d2809f1)
``` python
# Aggregate user-level data

user_features = merged_df.groupby('user_id').agg(
    num_requests=('user_id', 'count'),  # Total requests per user
    avg_amount=('amount', 'mean'),  # Average amount per request
    total_amount=('amount', 'sum'),  # Total borrowed amount
    first_request_month=('Month_cash_request', 'min'),  # First request date
    last_request_month=('Month_cash_request', 'max'),  # Last request date
    fees_paid=('total_amount', 'sum')  # Total fees paid
).reset_index()

# Convert first request month into categorical time periods
user_features['first_request_month'] = pd.to_datetime(user_features['first_request_month'], errors='coerce')
user_features['last_request_month'] = pd.to_datetime(user_features['last_request_month'], errors='coerce')


# Calculate user activity duration in days
user_features['user_lifetime'] = (user_features['last_request_month'] - user_features['first_request_month']).dt.days

# Convert first request month into categorical time periods (YYYY-MM format)
user_features['first_request_month'] = user_features['first_request_month'].dt.to_period('M')

# Classify users into spending segments
def classify_segment(amount):
    if amount < 100:  # Define thresholds based on data distribution
        return 'Low'
    elif amount < 500:
        return 'Medium'
    else:
        return 'High'

user_features['spend_segment'] = user_features['total_amount'].apply(classify_segment)

# Drop unnecessary columns
user_features = user_features.drop(columns=['last_request_month'])

user_features
```

### Prediccion con lo mejor SVM model 
``` python
# AUC for each class 
lb_svm= LabelBinarizer()
y_test_bin= lb_svm.fit_transform(y_test)
y_pred_prob_test= best_svm_model.predict_proba(X_test)

y_extrap_bin= lb_svm.fit_transform(y_extrap)
y_pred_prob_extrap= best_svm_model.predict_proba(X_extrap)


# Calculate the AUC for each class 
auc_scores_test=[]

for i in range(y_test_bin.shape[-1]):
    auc_test= roc_auc_score(y_test_bin[:,i], y_pred_prob_test[:,i])
    auc_scores_test.append(auc_test)

auc_scores_extrap=[]

for i in range(y_extrap_bin.shape[1]):
    auc_extrapt= roc_auc_score(y_extrap_bin[:,i], y_pred_prob_extrap[:,i])
    auc_scores_extrap.append(auc_test)

# Print the AUC for Each Class in The Test 
print('-'*50)
print('Interpolation')
for i , auc_test in enumerate(auc_scores_test):
    print(f"AUC pf the class{i}: {auc_test}")
print('-'*50)
print('Extrapolation')
# Print the AUC for Each Class in The Test 
for i , auc_extrap in enumerate(auc_scores_extrap):
    print(f"AUC pf the class{i}: {auc_extrap}")
```
![download](https://github.com/user-attachments/assets/c216bf68-f60b-499f-ac8a-16fb9564fcc2)

### Encuentra el mejor modelo KNN
``` python
from sklearn.neighbors import KNeighborsClassifier

k_values_knn = range(1, 20)
accuracies_knn = []

for k_knn in k_values_knn:
    knn_model = KNeighborsClassifier(n_neighbors=k_knn)  # Consistent naming
    scores_knn = cross_val_score(knn_model, X_train, y_train, cv=5, scoring='accuracy')  # Added scoring='accuracy'
    accuracies_knn.append(scores_knn.mean())

# Plot the results
plt.figure(figsize=(10, 6))
plt.plot(k_values_knn, accuracies_knn, marker='o')
plt.title('K-Nearest Neighbors Parameter Optimization')
plt.xlabel('Number of Neighbors (k)')
plt.ylabel('Average Accuracy')
plt.show()
```
![download](https://github.com/user-attachments/assets/a196cf58-573e-4ffe-9add-f3f116f3e2a2)

La mejor cantidad para el vecino KNN es 2. Creamos un modelo con este parámetro y aquí están los resultados.
![download](https://github.com/user-attachments/assets/cb90b070-bd14-432c-855f-1c0bcec430a1)
Classification Report:
              precision    recall  f1-score   support

           0       0.96      0.99      0.98       182
           1       1.00      0.99      0.99       363
           2       1.00      0.99      1.00      1318

    accuracy                           0.99      1863
   macro avg       0.98      0.99      0.99      1863
weighted avg       0.99      0.99      0.99      1863

              precision    recall  f1-score   support

           0       1.00      1.00      1.00        13
           1       1.00      1.00      1.00      1300
           2       1.00      1.00      1.00      1349

    accuracy                           1.00      2662
   macro avg       1.00      1.00      1.00      2662
weighted avg       1.00      1.00      1.00      2662

### Matriz de confusión para los datos de prueba:
![download](https://github.com/user-attachments/assets/c3e586ac-a155-4ed2-84c2-29aa3422c27c)


### Matriz de confusión para los datos de extrapolación:
![download](https://github.com/user-attachments/assets/bb66ac54-5878-4af6-bd3d-d41a900c41bb)

### AUC de Interpolacion:
![download](https://github.com/user-attachments/assets/f4282002-5c9b-4c19-9b36-6073df35b1fc)

### AUC of Extrapolacion:
![download](https://github.com/user-attachments/assets/982ac5f5-821c-4406-b5fd-6f5ea2a8e889)
AUC pf the class0: 0.9947801088666453
AUC pf the class1: 0.9947801088666453
AUC pf the class2: 0.9947801088666453

### Análisis de recuperación de la inversión
Para este análisis, después de fusionar los conjuntos de datos, primero creamos un nuevo campo llamado Fee_rejection que cuenta y luego categoriza a los usuarios según el rechazo alto y bajo.
``` python
# create a categorical field for the 
merged_df['fees_rejeted'] = merged_df.groupby('cash_request_id')['category'].count()
merged_df['fees_rejeted']= merged_df['fees_rejeted'].fillna(0)
merged_df['fees_rejeted']=merged_df['fees_rejeted'].sort_values()
merged_df['fees_rejeted'].unique()
bins = [0 ,2, 5]
labels =['Low' , 'High']
merged_df['fees_rejection_segment'] = pd.cut(merged_df['fees_rejeted'] , bins = bins , labels = labels, include_lowest = True  )
```
También calculamos el retraso en el pago.

``` python

# Create a new feature: repayment delay in days
merged_df['money_back_date'] = pd.to_datetime(merged_df['money_back_date'], errors='coerce')
merged_df["reimbursement_date"] = pd.to_datetime(merged_df["reimbursement_date"], errors='coerce')
merged_df["repayment_delay"] = (merged_df["money_back_date"] - merged_df["reimbursement_date"]).dt.days


```

![download](https://github.com/user-attachments/assets/be345d9d-335f-4fe4-b942-eacad9b651bf)

![download](https://github.com/user-attachments/assets/7c6906f1-eb1e-4217-b4b1-31f36ca8feaf)

#### Situación económica en noviembre de 2020:
Recuperación desigual – La economía global mejoraba, pero con diferencias. China se recuperó rápido, mientras EE.UU. y Europa enfrentaban nuevas olas de COVID-19.
Alto desempleo – EE.UU. tenía un 6.7% de desempleo (bajando desde 14.8% en abril), pero sectores como turismo y comercio seguían afectados.
Alza en los mercados – Las bolsas alcanzaron récords por el optimismo con las vacunas de Pfizer y Moderna.
Estímulos y deuda – Los gobiernos mantuvieron tasas de interés bajas y aumentaron el gasto público.
Inflación y cadenas de suministro – La inflación seguía baja, pero retrasos y escasez comenzaban a afectar los precios.


Luego creamos la columna Anomalía mediante el uso de dos algoritmos: IsolationForest y LocalOutlierFactor

![download](https://github.com/user-attachments/assets/a07f2cd5-b401-466b-8cdb-277f6510a7f6)
![download](https://github.com/user-attachments/assets/416afcbd-7943-4d91-ac96-91825c3869b9)
![download](https://github.com/user-attachments/assets/d217547b-5cc9-4e96-b460-793c01515847)
![download](https://github.com/user-attachments/assets/fc529d4b-de47-4f7f-836c-7a4e4db731a2)


##### Fee_rejection_segment in Anomalous data
![download](https://github.com/user-attachments/assets/44573820-b441-4997-9074-9886fa23a68b)
#### User_ID in Anomalous data
![download](https://github.com/user-attachments/assets/f2a8f163-28d0-4482-897e-f270b91a5b27)
![download](https://github.com/user-attachments/assets/97bb9055-577c-4bbd-9bea-f75348cc842b)
![download](https://github.com/user-attachments/assets/01cce82b-ae21-41d4-8038-ff68fbd4eaff)

##### Repayment_status with the Anomaly Hue
![download](https://github.com/user-attachments/assets/8cf42e5b-63ee-41d4-8b4e-ea86e28d037d)
![download](https://github.com/user-attachments/assets/f5a9e8ec-3e5c-4b69-be2a-814cf6e0bdd2)
![download](https://github.com/user-attachments/assets/2c0c7247-44e2-4d5d-9c7c-f052a9868d34)
![download](https://github.com/user-attachments/assets/8902e1ae-12b2-4551-b95d-3c5dcb2f3151)
![download](https://github.com/user-attachments/assets/1a427d1a-9824-4d3f-8051-253a869e015f)
![download](https://github.com/user-attachments/assets/77498c37-7896-4753-8001-fd38f4e5f652)
![download](https://github.com/user-attachments/assets/2b98eb6a-c601-4ff7-a4b7-d6b5c2157586)
![download](https://github.com/user-attachments/assets/beb6e167-ddb7-4649-8769-8605f640ad4b)
# Merged Dataset Heatmap
![download](https://github.com/user-attachments/assets/50acce8b-2b88-40de-9888-0377806900f5)

Existe una alta correlación negativa entre Transfer_type y Cash_request_id:
![download](https://github.com/user-attachments/assets/5be958b3-87a3-4baf-9cdb-4e24bf3aac9f)


