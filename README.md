# Descripción del Dataset de Retrasos y Cancelaciones de Vuelos
## (20242Q) MLE.01 - Machine Learning Engineering - Cloud Data Engineering ITBA 2024

### Alumnos:

*   D. Nicolás Morelli
*   Facundo G. Argibay
*   Lucas Franco
*   José Ignacio López Sáez

## Contexto del Negocio

Este dataset aborda el problema de los retrasos en los vuelos, un desafío significativo tanto para aerolíneas como para pasajeros. Los retrasos impactan en costos operativos, satisfacción del cliente y la eficiencia general de los aeropuertos. Para las aerolíneas y organismos de aviación, poder predecir con precisión la probabilidad de retraso de un vuelo es esencial para mejorar la experiencia del pasajero y la optimización de recursos.

La variable objetivo en este análisis es **DEP_DEL15**, un valor binario que indica si un vuelo se retrasó 15 minutos o más en su partida. La meta es utilizar esta variable para entrenar modelos de machine learning que puedan predecir futuros retrasos en los vuelos.

## Información del Dataset

Este dataset contiene información detallada de vuelos, condiciones meteorológicas, datos del aeropuerto y características de la aerolínea para el año 2019. Incluye datos mensuales y variables relacionadas con el clima y con la operación de los aeropuertos, y además permite realizar ajustes en el dataset, como agregar razones de cancelación y retrasos de llegada para problemas multicategoría.

| Característica                    | Descripción                                                                                                                                                                 |
|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Clasificación del problema**    | Clasificación binaria (evaluando retraso en la salida)                                                                                                                      |
| **Total de registros**            | 6,489,062 registros                                                                                                                                                        |
| **Total de columnas**             | 26 columnas, que abarcan variables numéricas, categóricas y de tipo `float`                                                                                                |
| **Variables meteorológicas**      | Incluyen precipitaciones (PRCP), nieve (SNOW), cobertura de nieve (SNWD), temperatura máxima (TMAX), velocidad del viento (AWND)                                           |
| **Variables de aerolínea y vuelo**| Datos de cantidad de vuelos por mes, aerolínea, aeropuerto, segmento del vuelo, grupo de distancia, entre otros                                                            |
| **Variables de personal**         | Cantidad promedio de asistentes de vuelo por pasajero (FLT_ATTENDANTS_PER_PASS) y personal de tierra por pasajero (GROUND_SERV_PER_PASS)                                    |
| **Variables geográficas**         | Aeropuertos de origen y destino, latitud y longitud de los aeropuertos de salida                                                                                           |

El archivo original del dataset está disponible en Kaggle bajo el nombre [2019 Airline Delays and Cancellations](https://www.kaggle.com/datasets/threnjen/2019-airline-delays-and-cancellations/data).

## Objetivo del Proyecto

El objetivo es entrenar modelos predictivos para anticipar la variable binaria **DEP_DEL15**. El desarrollo y la implementación del modelo se llevarán a cabo en **Amazon SageMaker** en AWS, donde se buscará construir y optimizar un modelo de machine learning que permita prever la probabilidad de retrasos en vuelos y, eventualmente, generar insights prácticos para la toma de decisiones en la gestión de operaciones de aerolíneas y aeropuertos.

---

## Pasos Seguidos

### 1. Desarrollo Local
- Se creó un pipeline de Data Science inicial utilizando **Pandas**, **XGBoost** y **TensorFlow**. 
- El pipeline fue diseñado para abarcar desde el preprocesamiento de datos hasta el ajuste de hiperparámetros.
- Tras probar exitosamente el proceso en un entorno local, se procedió a adaptarlo a la infraestructura de AWS para mejorar su escalabilidad y rendimiento.

### 2. Exploración de Datos
- Un **Dashboard de EDA** se generó para visualizar tendencias y comportamientos clave en los datos >> [2019 Airline Delays and Cancellations - Dashboard](https://nachols1986.github.io/infovis/airport_delays.html).
- Se identificaron características relevantes para alimentar los modelos predictivos.

---

## Infraestructura en AWS

### 1. Almacenamiento en S3

- El dataset original, en formato CSV, fue almacenado en un bucket S3 denominado `data-bucket-itba-delays`. Esta configuración permite un acceso seguro y escalable para el procesamiento y modelado de datos.
- En un contexto real, para optimizar costos, podría configurarse una política de almacenamiento **Glacier** en S3. Esto sería ideal para datos históricos que no requieran acceso frecuente, como datasets utilizados únicamente para auditorías o análisis retrospectivos.
- A futuro, si se dispusiera de datos en tiempo real que incluyeran todas las variables necesarias, se podría implementar un flujo de **ingesta continua**. Esto permitiría alimentar un pipeline que:
  - Integre datos en tiempo real a través de servicios como **Kinesis Data Streams** o **AWS IoT Core**.
  - Procese los datos entrantes utilizando **AWS Lambda** o **AWS Glue Streaming ETL**.
  - Almacene los datos preprocesados en formato **parquet** o **delta** para garantizar eficiencia en consultas y modelado.
- Este esquema permitiría desarrollar un **reentrenamiento periódico del modelo**, donde:
  - Los datos en tiempo real se acumulen en el bucket de S3.
  - Un job automatizado de **Glue o Sagemaker Pipelines** se encargue de realizar el preprocesamiento, ajuste de hiperparámetros y reentrenamiento del modelo según intervalos definidos.
  - Los modelos actualizados sean desplegados automáticamente como endpoints en Sagemaker, asegurando que las predicciones reflejen patrones recientes de comportamiento en los vuelos.

---

### 2. Roles IAM

- **GlueRole:** Este rol se creó con permisos completos para acceder al bucket S3, permitiendo tanto la lectura del archivo CSV original como la escritura del archivo en formato **parquet** generado tras el preprocesamiento. Estos permisos son fundamentales para que **AWS Glue** pueda ejecutar los jobs de transformación de datos y registrar la tabla procesada en el catálogo de Glue. 
  - **Ampliación futura:** En un entorno de producción, se podría optimizar la política de permisos asignada limitando el acceso a rutas específicas del bucket o usando **condiciones basadas en etiquetas**. Por ejemplo, restringir el acceso únicamente a archivos que contengan una etiqueta `dataset:flights`.
  - **Rotación de roles:** También se podrían implementar procesos automáticos para revisar y rotar las claves de acceso asociadas periódicamente, cumpliendo con políticas de seguridad organizacionales.

- **SMRole:** Este rol se diseñó para otorgar permisos de **solo lectura** al bucket S3, permitiendo que los notebooks y jobs en **Sagemaker** ingieran el dataset procesado y continúen con el pipeline de modelado. Esto asegura que los recursos de Sagemaker puedan consumir datos necesarios sin exponer permisos innecesarios.
  - **Ampliación futura:** En un flujo más avanzado, este rol podría integrarse con **Sagemaker Feature Store**, permitiendo la lectura no solo de datasets completos, sino también de **características específicas** preprocesadas y almacenadas en Feature Store, optimizando el uso de recursos.

- **Buenas prácticas y ampliaciones generales:**
  - **Principio de menor privilegio:** Se recomienda siempre restringir permisos al mínimo necesario. Por ejemplo, se podría usar una política específica que limite las acciones a `s3:GetObject` y `s3:PutObject` para las rutas necesarias del bucket.
  - **Monitoreo y auditoría:** Se pueden activar logs de acceso con **AWS CloudTrail** para rastrear el uso de estos roles, identificar patrones sospechosos y garantizar que no se usen para actividades no autorizadas.
  - **Integración con otros servicios:** Ambos roles podrían ampliarse para colaborar con servicios adicionales. Por ejemplo:
    - **EventBridge** para orquestar eventos basados en el flujo de datos.
    - **Athena** para ejecutar consultas SQL directamente sobre el parquet generado por Glue, reduciendo la necesidad de mover datos entre servicios.
    - **KMS (AWS Key Management Service)** para cifrar tanto el bucket como las comunicaciones entre servicios, asegurando el cumplimiento de normativas.

---

### 3. AWS Glue

El preprocesamiento de datos se migró exitosamente a **AWS Glue**, adaptando los pasos previamente realizados en **Pandas** al entorno de procesamiento distribuido de **PySpark**. Esta decisión permitió aprovechar la capacidad de Glue para manejar grandes volúmenes de datos de manera eficiente y escalable, algo crucial cuando se trabaja con datasets que pueden crecer significativamente en tamaño.

#### Transformaciones realizadas:
- **Creación de nuevas columnas:** 
  - Generación de columnas derivadas como **estaciones del año** basadas en la latitud y el mes, lo que facilita la identificación de patrones estacionales.
  - Segmentación de horarios y días para capturar tendencias específicas.
- **Escalado y transformación de variables:**
  - Estandarización de variables numéricas.
  - Conversión de columnas categóricas en formato adecuado para su uso en modelos de machine learning (e.g., encoding).
- **Manejo de valores nulos:** Implementación de estrategias específicas como imputación o eliminación dependiendo del impacto en las variables clave.

#### Beneficios del procesamiento en Glue:
El uso de Glue permitió no solo trasladar las transformaciones realizadas inicialmente de manera local, sino también escalar el pipeline para manejar datasets mucho más grandes. Además, Glue simplifica la integración con otros servicios de AWS, como el catálogo de datos y S3, optimizando el flujo de trabajo.

#### Detalles y Notebook relacionado:
- Todo el proceso está documentado y ejecutado en el **Notebook** correspondiente, disponible en este repositorio:  
  [Preprocessing Notebook en GitHub](https://github.com/nachols1986/MLE_Flights_Delay/blob/main/01_Preprocessing.ipynb).
  
Este notebook contiene:
1. La configuración inicial de Glue.
2. Detalles de las transformaciones realizadas.
3. La salida final en formato **parquet**, registrada en el catálogo de Glue para facilitar la consulta desde Athena o Sagemaker.

---

### 4. AWS Sagemaker

El desarrollo y despliegue del modelo de machine learning se realizó utilizando **AWS Sagemaker**, adaptando el pipeline local para aprovechar la infraestructura gestionada de Sagemaker, que facilita tanto el entrenamiento como el despliegue en producción.

#### Consumo de datos preprocesados:
- Los datos procesados previamente en **Glue** fueron ingeridos desde **Athena**, utilizando la tabla catalogada en el catálogo de Glue.
- Este enfoque permite una integración eficiente y evita duplicar etapas de procesamiento, asegurando consistencia en los datos utilizados para entrenar el modelo.

#### Pipeline de entrenamiento:
- Se reutilizó el pipeline desarrollado localmente, que incluye:
  - Pruebas con múltiples algoritmos, como **XGBoost** y redes neuronales.
  - Selección de los mejores modelos basándose en métricas de rendimiento (AUC).
  - Ajuste de hiperparámetros mediante búsqueda automatizada para maximizar la precisión y robustez de los modelos.
  
#### Despliegue del modelo:
- El modelo seleccionado (basado en **red neuronal**) fue desplegado como un **endpoint gestionado en Sagemaker**, permitiendo su integración directa con aplicaciones o sistemas en tiempo real.
- Este endpoint ofrece predicciones rápidas y escalables, adaptándose a volúmenes de consultas crecientes.

#### Detalles y Notebook relacionado:
- Todo el pipeline, desde el entrenamiento hasta el despliegue, está documentado y ejecutado en el **Notebook** correspondiente, disponible en este repositorio:  
  [Training & Deploy Notebook en GitHub](https://github.com/nachols1986/MLE_Flights_Delay/blob/main/02_Training%20%26%20Deploy.ipynb).

Este notebook incluye:
1. Configuración inicial de Sagemaker.
2. Comparativa de algoritmos entrenados.
3. Proceso de tuning de hiperparámetros.
4. Pasos para el despliegue del endpoint.

#### Futuras mejoras:
- **Automatización del reentrenamiento:** Implementación de un pipeline continuo utilizando servicios como **Step Functions** para reentrenar el modelo periódicamente con nuevos datos.
- **Monitoreo del endpoint:** Incorporar **Sagemaker Model Monitor** para detectar posibles sesgos o degradación en el rendimiento del modelo a lo largo del tiempo.
- **Uso de múltiples endpoints:** Según el caso de uso, se podrían desplegar diferentes modelos para escenarios específicos, optimizando la precisión en distintas condiciones.

---

## Conclusiones

- El modelo más efectivo fue **XGBoost**, debido a su robustez frente a outliers presentes en las columnas del dataset.
- Resultados principales:
  - **XGBoost:** AUC = **0.9356**
  - **Red Neuronal:** AUC = **0.6779**
- Si bien la red neuronal mostró resultados inferiores, el pipeline está diseñado para adaptarse a nuevas iteraciones y mejorar el rendimiento en el futuro.


