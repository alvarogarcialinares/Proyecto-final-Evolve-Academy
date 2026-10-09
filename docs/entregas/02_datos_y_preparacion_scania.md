# Entrega 02 — Datos y preparación
## Opción A: Sistema de mantenimiento predictivo para anticipar reparaciones en vehículos industriales

### 1. Qué datos necesita el producto

El producto propuesto pretende anticipar reparaciones de un componente mecánico en vehículos industriales a partir del historial de funcionamiento del vehículo.

La unidad básica de análisis será una **observación temporal de un vehículo**, identificada por `vehicle_id` y `time_step`. En cada instante se utilizarán los valores operativos disponibles hasta ese momento, junto con características estáticas de configuración del vehículo. La información de reparación se utilizará únicamente para construir la variable objetivo de la siguiente fase y no como variable predictora.

Los datos imprescindibles son:

- lecturas operativas del vehículo a lo largo del tiempo;
- identificador anónimo del vehículo;
- instante o paso temporal de cada lectura;
- información sobre si el componente fue reparado y cuándo;
- características estáticas del vehículo, como las especificaciones anonimizadas disponibles en el dataset.

Como información deseable podrían incorporarse en un producto real variables adicionales como kilometraje, condiciones ambientales, carga, rutas, historial completo de mantenimiento o coste de reparación. Sin embargo, no son necesarias para construir una primera versión del proyecto.

El histórico es especialmente importante porque se trata de un problema temporal: no basta con conocer el estado de un vehículo en un único momento, sino que interesa observar cómo evolucionan sus variables antes de una reparación.

### 2. Fuentes reales y viabilidad

La fuente principal será el dataset **SCANIA Component X Dataset: A Real-World Multivariate Time Series Dataset for Predictive Maintenance**, publicado por Scania CV AB y Stockholm University a través de Swedish National Data Service / Researchdata.se.

Fuente oficial:
https://researchdata.se/en/catalogue/dataset/2024-34/3

Artículo descriptivo:
https://doi.org/10.1038/s41597-025-04802-6

El conjunto completo contiene 11 archivos y ocupa aproximadamente 1,54 GiB. Para esta entrega se utilizarán principalmente tres ficheros del conjunto de entrenamiento:

- `train_operational_readouts.csv`: 1.122.452 observaciones de 23.550 vehículos. Incluye `vehicle_id`, `time_step` y variables operativas anonimizadas. El fichero tiene 107 columnas en total.
- `train_tte.csv`: información de tiempo hasta el evento o fin del estudio, con `vehicle_id`, `length_of_study_time_step` e `in_study_repair`.
- `train_specifications.csv`: características categóricas anonimizadas del vehículo (`Spec_0` a `Spec_7`), asociadas a `vehicle_id`.

El dataset también proporciona conjuntos de validación y test preparados para simular un escenario real en el que solo se conoce el histórico del vehículo hasta el momento de la predicción.

La viabilidad es alta porque los datos están publicados de forma estable, documentados y disponibles mediante descarga directa. Para no subir datos pesados al repositorio, se incluirán instrucciones de acceso y, si es necesario, una muestra pequeña generada por el notebook.

Como alternativa, si la fuente principal dejara de estar disponible, podría utilizarse otro dataset público de mantenimiento predictivo con series temporales industriales, manteniendo la misma estructura conceptual de datos operativos + información de evento/fallo.

### 3. Uso, privacidad y datos personales

Researchdata.se indica expresamente que el dataset **no contiene datos personales**. Los vehículos y las variables están anonimizados y las especificaciones se representan mediante categorías genéricas.

La licencia publicada es **Creative Commons Attribution 4.0 International (CC BY 4.0)**. Esto permite reutilizar y adaptar los datos siempre que se atribuya correctamente la fuente.

En el repositorio se incluirá la referencia al dataset y a su publicación científica. No se intentará identificar el significado real de las variables anonimizadas ni reconstruir información confidencial.

Desde el principio de minimización se conservarán únicamente las variables necesarias para el objetivo del producto: identificador técnico anónimo, tiempo, variables operativas, especificaciones y datos de reparación.

### 4. Del dato bruto al dato utilizable

#### Bronze / Raw

La capa Bronze conservará los archivos originales sin modificar:

- `train_operational_readouts.csv`
- `train_tte.csv`
- `train_specifications.csv`

No se subirán los archivos completos a GitHub por su tamaño. El repositorio incluirá la URL oficial, instrucciones de descarga y una estructura de carpetas esperada.

Ejemplo:

```text
data/
├── raw/
│   └── scania/
│       ├── train_operational_readouts.csv
│       ├── train_tte.csv
│       └── train_specifications.csv
└── processed/
```

#### Silver

La capa Silver aplica las transformaciones necesarias para disponer de tablas consistentes:

1. Comprobación de columnas y tipos.
2. Conversión de `vehicle_id` y variables temporales a tipos adecuados.
3. Ordenación de las lecturas por `vehicle_id` y `time_step`.
4. Detección y eliminación de duplicados exactos.
5. Revisión de valores nulos. En la muestra técnica utilizada para esta entrega, las variables operativas mantienen porcentajes de ausencia inferiores al 1 % por columna, coherentes con la documentación del dataset, por lo que no se realiza una imputación automática en esta fase.
6. Comprobación de que los valores temporales son coherentes y no existen observaciones posteriores al final del periodo estudiado.
7. Unión de las especificaciones estáticas mediante `vehicle_id`.
8. Unión de la información de reparación mediante `vehicle_id`.

Para la demostración reproducible se han cargado 250.000 lecturas operativas, correspondientes a 4.125 vehículos, mientras que `train_tte.csv` y `train_specifications.csv` se han cargado completos con 23.550 vehículos cada uno. No se han detectado duplicados exactos en las tres tablas.

Los `join` se han validado como relaciones `many_to_one`. Tras la unión, el notebook confirma **0 filas sin especificaciones** y **0 filas sin información TTE**, por lo que todas las observaciones de la muestra operativa han encontrado correctamente su información asociada.

Un aspecto crítico será evitar **data leakage**. Las variables que describen el resultado futuro (`in_study_repair`, `length_of_study_time_step` o cualquier variable derivada del tiempo restante hasta la reparación) se mantienen separadas de las variables que podrían utilizarse como predictores.

#### Gold

La primera estructura Gold tendrá **una fila por vehículo y paso temporal observado**.

Columnas principales:

- `vehicle_id`
- `time_step`
- variables operativas de ese instante;
- `Spec_0` ... `Spec_7`;
- `in_study_repair`;
- `length_of_study_time_step`;
- `time_to_event_from_row`, calculada únicamente cuando existe una reparación futura conocida;
- `is_censored`, para identificar vehículos sin reparación observada durante el periodo de estudio.

Conceptualmente:

```text
vehicle_id | time_step | variables_operativas | Spec_* | in_study_repair | time_to_event_from_row | is_censored
```

Esta estructura permite que la siguiente fase pueda plantearse como clasificación de riesgo, regresión de tiempo restante o análisis de supervivencia sin tener que rehacer toda la preparación de datos.

La ejecución realizada para esta entrega genera una tabla Gold de **250.000 filas y 119 columnas**. Para el repositorio se guarda una muestra de **10.000 filas** seleccionadas de forma aleatoria y reproducible mediante `random_state=42`, evitando limitar la muestra a los primeros vehículos del archivo.

Esta muestra se utiliza únicamente como ejemplo reproducible del Gold. En la fase de modelado no se realizará una división aleatoria indiscriminada por filas: las observaciones de un mismo vehículo y su orden temporal deberán mantenerse controlados para evitar fuga de información entre entrenamiento y evaluación.

Para esta Entrega 02 no se entrena ningún modelo. El objetivo es dejar el flujo reproducible y el Gold preparado.

### 5. Riesgos y limitaciones

La principal limitación es que las variables operativas y las especificaciones están anonimizadas, por lo que la interpretación mecánica de cada señal es limitada. Además, el evento de reparación está claramente desbalanceado: **2.272 de los 23.550 vehículos de entrenamiento presentan una reparación observada**, aproximadamente un **9,65 %**. Al tratarse de datos históricos de una flota concreta de Scania, el comportamiento puede no generalizar a otros fabricantes o componentes. Si la fuente dejara de estar disponible, se mantendría el mismo pipeline y se sustituiría por otro dataset industrial de mantenimiento predictivo con estructura temporal equivalente.

## Reproducibilidad

El repositorio incluirá:

```text
docs/entregas/02_datos_y_preparacion.md
notebooks/01_preparacion_datos_scania.ipynb
data/README.md
```

El notebook reproduce la carga de los tres ficheros, la inspección de tipos y calidad, la limpieza básica, los joins entre tablas y la generación de una muestra reproducible de la capa Gold.

## Referencias

- Swedish National Data Service / Researchdata.se. SCANIA Component X Dataset: https://researchdata.se/en/catalogue/dataset/2024-34/3
- Kharazian et al. (2025). *SCANIA Component X dataset: a real-world multivariate time series dataset for predictive maintenance*. Scientific Data. https://doi.org/10.1038/s41597-025-04802-6
