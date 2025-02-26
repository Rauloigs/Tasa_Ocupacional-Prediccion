# Tasa Ocupacional - Predicción con Sarima 

## 1. Selección de la Serie Temporal
- Elige una serie temporal de datos macroeconómicos de tu interés, que puede ser de México o de cualquier otra región internacional. Algunos ejemplos incluyen el PIB, tasas de inflación, tasas de interés, tipos de cambio, entre otros.
- Asegúrate de que los datos sean de frecuencia mensual, trimestral o anual y que cubran un periodo suficientemente largo para hacer predicciones fiables.

## 2. Preparación de los Datos:
- Importa la serie temporal seleccionada a tu herramienta de análisis (por ejemplo, R, Python, Excel).
- Realiza la limpieza de los datos, asegurándote de que no haya valores faltantes, valores atípicos, o problemas de formato.
- Si es necesario, ajusta los datos para la estacionalidad y asegúrate de que la serie esté bien preparada para el análisis.

	tasa_ocupacion	index_actividad_e
mes		
2024-08-01	2.762963	105.274053
2024-09-01	2.731746	105.375325
2024-10-01	2.444433	104.555690
2024-11-01	2.689342	104.873048
2024-12-01	2.592921	103.773558

![image](https://github.com/user-attachments/assets/d93eafed-9c12-4a39-95dd-18271517beb1)

**Gráficos ACF y PACF:**
   * **ACF (Autocorrelación):** Muestra la correlación entre la serie y sus valores pasados (retrasos). Nos ayuda a identificar el orden de la componente MA (q).
   * **PACF (Autocorrelación parcial):** Muestra la correlación directa entre la serie y un valor pasado específico, sin considerar los valores intermedios.  Nos ayuda a identificar el orden de la componente AR (p).

### Análisis de Residuos 

**Gráfico de línea de residuos:**

Este gráfico muestra los residuos a lo largo del tiempo. Idealmente, deberíamos ver un conjunto de puntos dispersos aleatoriamente alrededor de cero, sin patrones o tendencias claras. En este caso, los residuos parecen fluctuar alrededor de cero sin patrones significativos, excepto por algunos valores atípicos. Esto es una buena señal, ya que sugiere que el modelo está capturando la tendencia general y la estacionalidad de los datos.

![image](https://github.com/user-attachments/assets/5f2c69e1-6fb8-4dc8-be1d-51b83e3cfd9e)

**Histograma de residuos:**

Este gráfico muestra la distribución de frecuencias de los residuos. Idealmente, debería parecerse a una curva de campana, lo que indica una distribución normal. Para este caso, el histograma muestra una distribución algo sesgada, con una cola más larga en el lado derecho. Esto indica que los residuos podrían no tener una distribución perfectamente normal.

![image](https://github.com/user-attachments/assets/f1c7d1bd-de1f-4904-8c06-14cbdd1efab4)

**Gráfico Q-Q de residuos:**

Este gráfico compara los cuantiles de la distribución de residuos con los cuantiles de una distribución normal. Si los residuos tienen una distribución normal, los puntos deberían caer aproximadamente a lo largo de una línea recta. Los puntos se desvían de la línea recta, especialmente en las colas. Esto confirma que los residuos podrían no tener una distribución perfectamente normal.

![image](https://github.com/user-attachments/assets/15e5daa1-540d-4605-b2ac-bec7a46d6802)


**En general, el análisis de los residuos sugiere que el modelo SARIMA es un ajuste razonable para los datos, capturando los patrones principales en la serie temporal. Sin embargo, podría haber margen de mejora, ya que los residuos muestran algunas desviaciones de la normalidad.**


## 3. Modelo SARMA (Seasonal Autoregressive Moving Average):
- Aplica un modelo SARMA a la serie temporal seleccionada, identificando y estimando los parámetros adecuados para la componente autoregresiva (AR), la media móvil (MA) y la estacionalidad (S).
- Realiza un análisis diagnóstico de los residuos del modelo para verificar la calidad del ajuste.

**Resumen de los resultados: (SARIMA)**

El modelo SARIMA que se ajustó tiene esta estructura: SARIMAX(1, 0, 1)x(1, 0, 1, 12). 

* **AR(1):** Se usa un valor pasado de la serie (retraso 1) para predecir el valor actual.
* **MA(1):** Se usa un error de pronóstico pasado (retraso 1) para corregir la predicción actual.
* **Estacionalidad (S):** Se incluye un componente estacional con AR(1) y MA(1) que se repite cada 12 periodos (mensual).

**Estimación de los parámetros:**

**Análisis de la descomposición:** La descomposición de la serie muestra que hay una tendencia y una componente estacional clara. Necesitamos un modelo que capture ambas.

**Ajuste del modelo SARIMA:** Con base en el análisis anterior, se ajustó un modelo SARIMA con los parámetros (1, 0, 1)x(1, 0, 1, 12). El modelo estima los coeficientes para cada componente (AR, MA, estacional) que mejor se ajusten a los datos.

**Estimados para cada componente del modelo (ar.L1, ma.L1, ar.S.L12, ma.S.L12).**  

* **ar.L1 (0.9995):** Coeficiente para el componente AR(1). **el valor de la tasa de ocupación en el mes anterior tiene un peso muy alto en la predicción del valor actual.**
* **ma.L1 (-0.3671):** Coeficiente para el componente MA(1).  Indica que el error de pronóstico del mes anterior se usa para corregir la predicción actual.
* **ar.S.L12 (0.9941) y ma.S.L12 (-0.9974):** Coeficientes para los componentes AR(1) y MA(1) de la estacionalidad.  Capturan la influencia de los valores y errores de pronóstico de hace 12 meses.

## 4. Modelo DLM (Dynamic Linear Model):
- Implementa un modelo DLM para la serie temporal seleccionada. Este modelo debe permitir modelar las tendencias y las componentes estocásticas de los datos.
- Ajusta el modelo DLM para mejorar la predicción a través de la optimización de los parámetros.

### Análisis DLM

**1. Tendencia Suave y Estable:**

El modelo ha identificado una tendencia en la `tasa_ocupacion`, lo cual significa que, en general, la tasa ha ido cambiando a lo largo del tiempo. Sin embargo, esta tendencia se caracteriza por ser suave y estable. Esto lo sabemos porque la varianza del componente de tendencia (`sigma2.trend`) es prácticamente cero. 

En otras palabras, la `tasa_ocupacion` no ha experimentado cambios bruscos o repentinos en su tendencia, sino que ha seguido un patrón relativamente predecible y constante a lo largo del tiempo.

**2. Nivel con Cambios Graduales:**

El modelo también incluye un componente de nivel, que representa el valor promedio de la `tasa_ocupacion` en cada momento del tiempo. En este caso, el nivel se ha modelado como estocástico, lo que significa que puede variar de forma aleatoria.

Sin embargo, la varianza del componente de nivel (`sigma2.level`) es bastante pequeña (0.0002). Esto nos indica que, aunque el nivel puede cambiar, estos cambios tienden a ser graduales y no muy pronunciados. Es decir, el valor promedio de la `tasa_ocupacion` no fluctúa de manera drástica de un período a otro.

**3. Predominio de Factores Predecibles:**

**3. Predominio de Factores Predecibles:**

En general, los componentes estocásticos (tanto el nivel como la tendencia) parecen tener una influencia relativamente menor en comparación con la tendencia determinista. Esto sugiere que el comportamiento de la `tasa_ocupacion` está determinado en gran medida por factores predecibles y no tanto por eventos aleatorios o inesperados.

**En resumen, el modelo DLM nos muestra que la `tasa_ocupacion` ha seguido una tendencia suave y estable, con cambios graduales en su nivel promedio, y que su comportamiento está influenciado principalmente por factores predecibles.**

Esta información nos puede ser muy útil para:

* **Comprender mejor la dinámica de la `tasa_ocupacion`:** El modelo nos ayuda a identificar los patrones subyacentes en los datos y a entender cómo ha evolucionado la tasa a lo largo del tiempo.
* **Realizar pronósticos:** Podemos utilizar el modelo ajustado para predecir valores futuros de la `tasa_ocupacion`, lo cual puede ser de gran ayuda para la toma de decisiones.
* **Evaluar la efectividad de intervenciones:** Si se implementan medidas para influir en la `tasa_ocupacion`, el modelo nos puede ayudar a evaluar si estas medidas han tenido el efecto deseado.

## 5. Combinación de Pronósticos:
- Combina los pronósticos generados por ambos modelos (SARMA y DLM) utilizando técnicas adecuadas de combinación, como el promedio ponderado o la integración de resultados a través de un modelo de mezcla.
- Compara los resultados obtenidos con los pronósticos individuales de cada modelo y evalúa cuál combinación proporciona el mejor ajuste a los datos históricos.

**Resumen de los resultados Combinación SARIMA y DLM**

* **Modelo SARIMA:**
    * El modelo SARIMA(1, 1, 1)x(1, 1, 1, 12) se ajustó a la serie de tiempo de la tasa de desocupación.
    * Los coeficientes estimados para los componentes AR, I y MA, tanto no estacionales como estacionales, son significativos (valores p < 0.05).
    * El modelo explica una parte significativa de la varianza de la serie, pero aún hay cierta autocorrelación en los residuos.

* **Modelo DLM:**
    * El modelo DLM se ajustó a los residuos del modelo SARIMA para capturar la estacionalidad dinámica.
    * Los coeficientes de los componentes de nivel, tendencia y estacionalidad son significativos.
    * Las varianzas de los errores de los componentes son relativamente pequeñas.

* **Pronósticos combinados:**
    * Se generaron pronósticos para 12 meses utilizando la combinación de los modelos SARIMA y DLM.
    * Los pronósticos muestran una tendencia a la baja en la tasa de desocupación, con cierta fluctuación estacional.

**Interpretación:**

* **Tendencia:** La tendencia general de la tasa de desocupación es a la baja, lo que sugiere que el mercado laboral está mejorando.
* **Estacionalidad:** Hay un patrón estacional claro en la tasa de desocupación, con picos en ciertos meses del año. El modelo DLM captura esta estacionalidad dinámica, lo que significa que el patrón estacional puede cambiar con el tiempo.
* **Relación con el índice de actividad económica:** El modelo DLM incluye el índice de actividad económica como una variable independiente, lo que sugiere que hay una relación entre estas dos variables. Sin embargo, la naturaleza exacta de esta relación no se puede determinar solo con estos resultados. Se necesita un análisis más profundo para comprender cómo el índice de actividad económica.
* **Precisión del modelo:** Los pronósticos combinados parecen ser razonables, pero es importante evaluar su precisión utilizando métricas de error como RMSE y MAE. También es importante realizar un análisis de residuos para verificar si se cumplen los supuestos del modelo.

## 6. Evaluación del Modelo:
- Evalúa la precisión de los pronósticos utilizando métricas como el Error Cuadrático Medio (RMSE), el Error Absoluto Medio (MAE), o el Coeficiente de Determinación (R²).
- Discute las fortalezas y debilidades de los modelos SARMA, DLM y su combinación en función de los resultados obtenidos.


**Métricas del modelo SARIMA vs DLM:**

**Comparación de las métricas:**

| Métrica | DLM | SARIMA |
|---|---|---|
| RMSE | 0.249 | 0.302 |
| MAE | 0.198 | 0.241 |
| R² | 0.944 | 0.907 |

Observando esta tabla, podemos concluir que **el modelo DLM ha superado al modelo SARIMA en las tres métricas.** * El DLM tiene un RMSE y un MAE menores, lo que indica una mayor precisión en las predicciones.
* El DLM tiene un R² más alto, lo que sugiere un mejor ajuste a los datos.

**Análisis de la gráfica del SARIMA:**

La gráfica del modelo SARIMA muestra un comportamiento similar al DLM en cuanto al seguimiento de la tendencia general de la `tasa_ocupacion`. Sin embargo, podemos observar algunas diferencias:

* **Mayor error en algunos periodos:** En algunos puntos, las predicciones del SARIMA se alejan más de los valores reales de la tasa, especialmente en los últimos años. Esto se refleja en los valores más altos de RMSE y MAE.
* **Intervalos de confianza más amplios:** Los intervalos de confianza del SARIMA son más anchos que los del DLM, lo que indica una mayor incertidumbre en las predicciones.

**Posibles razones del mejor rendimiento del DLM:**

* **Flexibilidad en la tendencia:** El DLM permite que la tendencia varíe en el tiempo, lo que puede ser crucial para capturar la dinámica de la `tasa_ocupacion`, que parece haber experimentado cambios en su tendencia en los últimos años.
* **Adaptación a fluctuaciones:** El DLM se ajusta mejor a las fluctuaciones a corto plazo de la tasa, lo que sugiere una mayor capacidad de adaptación a cambios en la dinámica de la serie.

**Conclusión preliminar:**

Basándonos en las métricas y las gráficas, podemos concluir que **el modelo DLM ha mostrado un mejor rendimiento que el modelo SARIMA para predecir la `tasa_ocupacion`.** El DLM ha logrado una mayor precisión en las predicciones y un mejor ajuste a los datos, lo que sugiere que es más adecuado para capturar la dinámica de esta serie temporal.

**Próximos pasos:**

* **Analizar los residuos:** Sería útil analizar los residuos de ambos modelos para identificar posibles patrones no capturados y evaluar si se cumplen los supuestos de los modelos.
* **Considerar la combinación de modelos:** Aunque el DLM ha mostrado un mejor rendimiento individual, podría ser interesante explorar la combinación de SARIMA y DLM para intentar aprovechar las fortalezas de ambos modelos. Por ejemplo, se podría usar SARIMA para modelar la estacionalidad y DLM para la tendencia.

**Interpretación de los resultados del test de Sensibilidad:**

- **Impacto de los parámetros:** Observando la tabla, podemos ver cómo el cambio en los valores de los parámetros afecta el RMSE. Por ejemplo, se puede notar que el parámetro level tiene un mayor impacto en el RMSE que los parámetros trend y seasonal. Esto significa que la precisión del modelo es más sensible a los cambios en el parámetro level.

- **Valores óptimos:** El test de sensibilidad te ayuda a identificar las combinaciones de parámetros que producen el menor RMSE. En este caso, la combinación con level = 0.98, trend = 0.95 y seasonal = 0.95 parece producir el RMSE más bajo.

- **Robustez del modelo:** Si el RMSE no cambia significativamente al variar los parámetros, esto indica que el modelo es robusto a las variaciones en los parámetros. Por otro lado, si el RMSE cambia mucho, esto sugiere que el modelo es sensible a los parámetros y que la elección de los valores de los parámetros es crucial para la precisión del modelo.
