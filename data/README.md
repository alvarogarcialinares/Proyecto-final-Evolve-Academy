# Datos

Los datasets originales utilizados en este proyecto **no se incluyen en el repositorio** debido a su tamaño.

## Fuente

El proyecto utiliza el dataset:

**SCANIA Component X Dataset: A Real-World Multivariate Time Series Dataset for Predictive Maintenance**

Fuente oficial:

https://researchdata.se/en/catalogue/dataset/2024-34/3

Publicación asociada:

https://doi.org/10.1038/s41597-025-04802-6

## Archivos necesarios

Para reproducir la Entrega 02 se utilizan estos tres archivos del conjunto de entrenamiento:

```text
train_operational_readouts.csv
train_tte.csv
train_specifications.csv
```

## Estructura esperada

Descarga los archivos originales y colócalos de esta forma:

```text
data/
├── README.md
├── raw/
│   └── scania/
│       ├── train_operational_readouts.csv
│       ├── train_tte.csv
│       └── train_specifications.csv
└── processed/
```

El notebook espera encontrar los datos en:

```text
../data/raw/scania/
```

cuando se ejecuta desde la carpeta `notebooks/`.

## Datos procesados

El notebook genera una muestra reproducible de la capa Gold en:

```text
data/processed/scania_gold_sample.csv
```

Esta muestra se utiliza únicamente como demostración del flujo de preparación de datos.

Los archivos originales y los CSV procesados no deben subirse al repositorio.

## Nota sobre licencia y privacidad

La fuente oficial indica que el dataset se distribuye bajo licencia **CC BY 4.0** y que no contiene datos personales.

Debe mantenerse la atribución correspondiente a la fuente original.
