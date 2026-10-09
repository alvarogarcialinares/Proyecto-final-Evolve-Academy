# Proyecto Final — Data Science e IA

Proyecto desarrollado para el Máster en Data Science e Inteligencia Artificial de Evolve Academy.

## Estado actual

El proyecto se encuentra todavía en una fase inicial de definición y preparación de datos.

Como línea de trabajo actual se ha seleccionado **SCANIA Component X**, un dataset real orientado a mantenimiento predictivo de vehículos industriales. La elección podrá evolucionar en próximas entregas si aparece una alternativa que encaje mejor con el objetivo final del proyecto.

## Objetivo provisional

Construir una solución basada en datos que permita estudiar el estado operativo de vehículos industriales y preparar la información necesaria para anticipar futuras reparaciones de un componente.

En esta fase todavía **no se entrena ningún modelo**. El trabajo se centra en dejar los datos reales, trazables y preparados mediante un flujo reproducible **Bronze → Silver → Gold**.

## Estructura del repositorio

```text
.
├── README.md
├── .gitignore
├── requirements.txt
├── docs/
│   └── entregas/
│       ├── 01_ideas_producto.md
│       └── 02_datos_y_preparacion.md
├── notebooks/
│   └── 01_preparacion_datos_scania.ipynb
└── data/
    ├── README.md
    ├── raw/
    │   └── scania/
    └── processed/
```

## Entrega 02 — Datos y preparación

La Entrega 02 desarrolla:

- qué datos necesita el producto;
- fuentes reales y viabilidad;
- uso, privacidad y condiciones de utilización;
- transformación Bronze → Silver → Gold;
- riesgos y limitaciones.

Documento principal:

```text
docs/entregas/02_datos_y_preparacion.md
```

Notebook reproducible:

```text
notebooks/01_preparacion_datos_scania.ipynb
```

## Datos

El dataset completo no se almacena en este repositorio debido a su tamaño.

La fuente utilizada es:

**SCANIA Component X Dataset: A Real-World Multivariate Time Series Dataset for Predictive Maintenance**

Fuente oficial:

https://researchdata.se/en/catalogue/dataset/2024-34/3

Las instrucciones para descargar y organizar los archivos necesarios se encuentran en:

```text
data/README.md
```

## Reproducibilidad

El notebook utiliza rutas relativas y está preparado para ejecutarse de arriba abajo una vez colocados los archivos originales en la estructura indicada.

Para esta entrega se utiliza una muestra técnica de las lecturas operativas para mantener el procesamiento manejable en un equipo personal.

## Tecnologías utilizadas

- Python
- pandas
- NumPy
- Jupyter Notebook

## Autor

**Álvaro García Linares**  
Máster en Data Science e Inteligencia Artificial — Evolve Academy
