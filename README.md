# StreamView Analytics — Proyecto de Visualización de Datos (EP3)

Este repositorio contiene el pipeline completo de datos, análisis exploratorio (EDA) y documentación estratégica para la toma de decisiones en la plataforma de streaming ficticia **StreamView**. El objetivo del proyecto es desenmascarar los sesgos de calificación, analizar el verdadero cruce entre *engagement* y calidad percibida, y transparentar el Retorno de Inversión (ROI) del catálogo mediante un dashboard interactivo en Looker Studio.

## 🔗 Dashboard Interactivo

**[https://datastudio.google.com/reporting/598793da-33a9-405f-86dc-8e0d717c4835]**

---

## 📂 Estructura del Repositorio

El proyecto sigue estándares profesionales de ciencia de datos, separando claramente los datos crudos, los scripts de procesamiento, los cuadernos de análisis y los entregables finales:

```text
📁 EP3_visualizacion_datos/
├── 📁 data/
│   ├── raw/                 # Archivos originales inmutables (movies.csv, series.csv)
│   └── processed/           # Datasets limpios y Data Marts (looker_contenido.csv, kpis_*.csv)
├── 📁 src/                  # Scripts de producción (pipeline ETL)
│   ├── limpieza.py          # Integración, limpieza, cálculo de percentiles y manejo de nulos
│   └── kpis.py              # Generación de la tabla maestra para Looker y tablas de auditoría
├── 📁 notebooks/            # Entorno interactivo de análisis
│   ├── 01_integracion_y_limpieza.ipynb  # Documentación técnica de la limpieza
│   └── 02_eda_visualizaciones.ipynb     # Análisis Exploratorio y generación de gráficos G1-G8
├── 📁 images/               # Gráficos PNG exportados automáticamente por los notebooks
├── 📁 reports/              # Entregables escritos
│   └── informe_ejecutivo.md # Capítulo 2: Hallazgos, narrativa visual y limitaciones
└── README.md                # Este archivo
```

---

## 🛠️ Arquitectura de Datos y Auditoría (QA)

El flujo de procesamiento (ETL) fue diseñado para resolver el desafío crítico de **Integridad Financiera vs. Granularidad**: dado que la mayoría del catálogo posee múltiples géneros o países, cruzar estas variables en una herramienta de BI tradicional infla artificialmente las sumatorias de ingresos y presupuestos.

Para solucionarlo, `src/kpis.py` exporta dos tipos de archivos a `data/processed/`:

1. **La Tabla Maestra (`looker_contenido.csv`):** El único dataset que debe conectarse a Looker Studio. Ha sido aplanado deliberadamente para contener **estrictamente 1 fila por título** (31.991 filas), asignando el 100% del valor financiero a la categoría principal (`genero_principal`, `pais_principal`, `director_principal`). Esto garantiza que los filtros globales no se quiebren y los cálculos de ROI sean exactos.
2. **Las Tablas de Control o Data Marts (`kpis_genero.csv`, `kpis_pais.csv`, `kpis_resumen.csv`, etc.):** Archivos complementarios generados por Python que fungen como marco de validación (QA). Permiten auditar matemáticamente que el dashboard en Looker Studio está calculando las métricas correctamente.

---

## 🚀 Instrucciones de Ejecución (Reproducibilidad)

Para reproducir el pipeline completo desde cero y regenerar todos los CSVs y gráficos:

1. **Instalar dependencias:**
   Asegúrate de contar con Python 3.9+ e instala los requerimientos:
   ```bash
   pip install -r requirements.txt
   ```

2. **Ejecutar el Pipeline ETL:**
   Ejecuta los scripts en el siguiente orden desde la raíz del proyecto:
   ```bash
   python src/limpieza.py
   python src/kpis.py
   ```
   *Nota: La consola imprimirá los logs de validación de calidad de datos, confirmando que 300/300 métricas coinciden exactamente.*

3. **Ejecutar Notebooks (Opcional):**
   Abre y ejecuta `notebooks/01_integracion_y_limpieza.ipynb` y luego `notebooks/02_eda_visualizaciones.ipynb` para reconstruir los gráficos ubicados en la carpeta `images/`.

---

## 📊 Entregables en Looker Studio

Ambos entregables se construyen conectando de forma exclusiva la tabla maestra `data/processed/looker_contenido.csv`.

### 1. Dashboard Interactivo
Diseñado bajo los principios de reducción de carga cognitiva, operando como una ventana de exploración multidimensional.

**Controles Globales (Transversales a las 5 páginas)**
* **Listas desplegables:** `tipo_contenido`, `genero_principal`, `pais_principal`, `idioma`.
* **Rango numérico:** `release_year`.

**Tarjetas KPI (Fila superior global)**
* **Total Títulos:** `COUNT_DISTINCT(content_id)`
* **% del Catálogo:** `COUNT_DISTINCT(content_id) / 31991` *(Denominador base fijo).*
* **Calificación Promedio:** `AVG(vote_average_clean)`
* **% con Votos Suficientes:** `SUM(confiable_num) / COUNT_DISTINCT(content_id)`
* **% Alto Engagement:** `SUM(alto_engagement) / COUNT_DISTINCT(content_id)`
* **% Títulos Estrella:** `SUM(es_estrella) / COUNT_DISTINCT(content_id)`

**Estructura de Páginas**
1. **Resumen y Tendencias:** Dispersión (Calidad vs Engagement) por `genero_principal`. Líneas de tiempo desglosadas por `tipo_contenido`.
2. **Película vs. Serie:** Gráficos de barras comparativos de volumen, calidad y cobertura de votos fiables.
3. **Exploración por Categorías:** Top 7 de géneros y Top 7 de países por volumen (excluyendo "No especificado").
4. **Desempeño Financiero (Directores):** Tabla Top 10 directores (`director_principal`) ordenados por ingresos. Filtro interno: `financiero_num = 1` y mínimo 5 películas.
5. **Metodología y Limitaciones:** Texto declarando los sesgos del dataset.

### 2. Infografía (Resumen Ejecutivo)
Diseñada como una narrativa visual estática (Data Storytelling). No posee filtros dinámicos. Incluye textos conclusivos fijos integrados al diseño.

*   **Gráfico 1: "Muy visto no garantiza buena nota"**
    *   **Configuración:** Dispersión. Eje X (`vote_average_clean`, Promedio), Eje Y (`indice_engagement`, Mediana). Dimensión: `genero_principal`.
    *   **Filtros fijos:** `es_pelicula = 1`. Excluir `genero_principal` = "No especificado".
    *   **Nota al pie:** "Cada punto es un género principal (calidad promedio, engagement mediano)".
*   **Gráfico 2: "Drama frente a espectáculo"**
    *   **Configuración:** Dos gráficos de barras alineados horizontalmente. Dimensión en ambos: `genero_principal` (Limitado a: Drama, Comedia, Acción y Aventura, Terror, Animación, Ciencia ficción y Fantasía).
    *   **Métricas:** Gráfico A (Volumen) usa conteo de títulos. Gráfico B (Engagement) usa `indice_engagement` (Mediana o Promedio).
    *   **Filtros fijos:** `es_pelicula = 1`.
    *   **Nota al pie:** "Como género principal, el Drama es el 22,9 % de las películas; considerando todos sus géneros, aparece en el 43,2 %".
*   **Gráfico 3: "Entre los de mayor recaudación"**
    *   **Configuración:** Tabla de barras mostrando 10 filas. Dimensión: `director_principal`.
    *   **Métricas:** `SUM(ingresos_roi) / SUM(presupuesto_roi)`. Orden descendente por Ingresos Totales.
    *   **Filtros fijos:** `financiero_num = 1` y `COUNT_DISTINCT(content_id) >= 5`.
    *   **Nota al pie:** "ROI agregado = ingresos ÷ presupuesto; mínimo 5 películas con datos; primer director listado por título".
