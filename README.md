# StreamView Analytics — Proyecto de Visualización de Datos (EP3)

Este repositorio contiene el pipeline completo de datos, análisis exploratorio (EDA) y documentación estratégica para la toma de decisiones en la plataforma de streaming ficticia **StreamView**. El objetivo del proyecto es desenmascarar los sesgos de calificación, analizar el verdadero cruce entre *engagement* y calidad percibida, y transparentar el Retorno de Inversión (ROI) del catálogo mediante un dashboard interactivo en Looker Studio.

## 🔗 Dashboard Interactivo

👉 **[PEGAR_AQUI_LINK_LOOKER_STUDIO]** 👈

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

## 📊 Especificación Técnica del Dashboard (Looker Studio)

El dashboard ha sido diseñado bajo los principios de reducción de carga cognitiva, operando como una ventana de navegación interconectada.

### Controles Globales (Transversales a las 5 páginas)
* **Listas desplegables:** `tipo_contenido`, `genero_principal`, `pais_principal`, `idioma`.
* **Rango numérico:** `release_year`.

### Tarjetas KPI (Fila superior global)
Reaccionan dinámicamente a los filtros. Fórmulas configuradas en Looker:
* **Total Títulos:** `COUNT_DISTINCT(content_id)`
* **% del Catálogo:** `COUNT_DISTINCT(content_id) / 31991` *(Denominador base fijo).*
* **Calificación Promedio:** `AVG(vote_average_clean)`
* **% con Votos Suficientes:** `SUM(confiable_num) / COUNT_DISTINCT(content_id)`
* **% Alto Engagement:** `SUM(alto_engagement) / COUNT_DISTINCT(content_id)`
* **% Títulos Estrella:** `SUM(es_estrella) / COUNT_DISTINCT(content_id)`

### Estructura de Páginas

1. **Resumen y Tendencias:**
   * Gráfico de dispersión (Calidad vs Engagement) por `genero_principal`.
   * Líneas de tiempo desglosadas por `tipo_contenido` mostrando la evolución del Engagement y Calidad. (Uso de paleta daltónica: Películas Azul `#0072B2`, Series Naranja `#E69F00`).
2. **Película vs. Serie:**
   * Gráficos de barras comparativos de volumen y calidad.
   * Gráfico de barras de **Cobertura de votos fiables**, vital para evidenciar la fragilidad del puntaje promedio de las series.
3. **Exploración por Categorías:**
   * Top 7 de géneros por cantidad de títulos.
   * Top 7 de países por cantidad de títulos, con línea de calidad promedio cruzada. (Excluyendo "No especificado" en ambos casos).
4. **Desempeño Financiero (Directores):**
   * Tabla con el Top 10 directores (`director_principal`) ordenados por ingresos.
   * Filtro interno obligatorio: `financiero_num = 1` y mínimo 5 películas (`COUNT_DISTINCT(content_id) >= 5`).
   * Incluye tarjetas financieras exclusivas de esta página: Cobertura Financiera y ROI Agregado global.
5. **Metodología y Limitaciones:**
   * Página de texto declarando los sesgos del dataset (ej: 628 telefilms excluidos, "No especificados", límite artificial de 1.000 títulos/año, ceguera financiera de las series).
