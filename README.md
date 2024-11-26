# Descripción del Dataset de Retrasos y Cancelaciones de Vuelos
##(20242Q) MLE.01 - Machine Learning Engineering - Cloud Data Engineering ITBA 2024

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
# Proyecto de Machine Learning con AWS - CONSIGNA TP FINAL

## Objetivo
Crear una solución completa de Machine Learning utilizando los servicios de AWS, desde la ingestión de datos hasta la visualización de resultados.

---
