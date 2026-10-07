# Capítulo 2: Diseño de Dashboards Interactivo

### 1. Descripción del problema de negocio
StreamView Analytics, plataforma de streaming internacional, requiere comprender en profundidad el desempeño de su catálogo histórico de películas y series para optimizar sus estrategias de retención, engagement y adquisición de nuevos títulos. 

**Audiencia Objetivo:** 
El informe está dirigido a dos actores clave:
* **Gerencia de Contenido:** Responsable de tomar decisiones de adquisición y renovación (qué géneros y países priorizar).
* **Dirección Ejecutiva:** Responsable de aprobar los presupuestos de inversión, validar la rentabilidad (ROI) y entender los riesgos estructurales de los datos.

**Objetivos de Comunicación:**
Persuadir al equipo directivo de que (1) las series y las películas presentan perfiles de consumo totalmente distintos, (2) el *engagement* (popularidad) y la *calidad percibida* (calificación) son métricas independientes que exigen estrategias de adquisición separadas, y (3) existen deficiencias críticas en los datos de la plataforma (ej. falta de finanzas) que limitan la visibilidad del negocio.

### 2. Fuentes de datos e Integración
La información se construyó a partir de dos fuentes crudas: `netflix_movies_detailed_up_to_2025.csv` y `netflix_tv_shows_detailed_up_to_2025.csv` (16.000 títulos cada una).

**Proceso de Integración y Variables Relevantes:**
* **Llave Compuesta:** Se detectó un solapamiento de 397 `show_id` distintos entre ambos archivos, por lo que se generó la llave única `content_id` ("M-" para películas, "S-" para series) asegurando la integridad relacional.
* **Limpieza de Ceros y Votos:** Se identificó que las calificaciones de "0" correspondían a títulos sin interacción, no a malas evaluaciones. Se creó `vote_average_clean` (que anula las notas sin votos) y `calificacion_confiable` para aislar títulos con un sustento de audiencia real (≥ 10 votos).
* **Normalización de Engagement:** Debido a que la escala de popularidad difería radicalmente (mediana de 10,9 en películas vs. 36,2 en series), se generó un `indice_engagement` (percentil de 0 a 100 intrapolo) para permitir comparativas justas.
* **Control de Géneros (Data Quality):** Se homologaron las dos taxonomías dispares de las fuentes originales a un mapa de géneros único en español. Adicionalmente, se extrajeron 628 títulos catalogados como "TV Movie", marcándolos como `es_telefilm` y excluyéndolos de la dimensión de géneros al tratarse de un formato y no de un atributo narrativo.
* **Salud Financiera:** Se aislaron 3.540 películas con datos no nulos en ingresos y presupuesto para el cálculo seguro del Retorno de Inversión (ROI).

### 3. Análisis exploratorio mediante visualizaciones
La exploración previa permitió identificar los patrones fundamentales que rigen la estrategia de visualización:
* **Sesgo de Calificación:** Las series ostentan un promedio de calidad percibida mucho mayor que las películas (7,02 frente a 6,31), pero una proporción dramáticamente menor de sus títulos ha logrado acumular suficientes votos de la audiencia (38,4 % vs. 88,6 %).
* **Asimetría Extrema:** La popularidad y los ingresos financieros presentan colas largas hacia la derecha (valores extremos muy altos). Esto invalidó el uso de promedios simples y forzó al equipo a utilizar medianas y percentiles para proteger los insights de los "outliers".
* **Limitación del Dataset:** El hallazgo de que el volumen de producción por año es artificial (exactamente 1.000 títulos anuales por tipo) impidió realizar análisis volumétricos temporales, reorientando el EDA exclusivamente al desempeño de las variables.

### 4. Diseño e implementación de visualizaciones
Las decisiones gráficas priorizaron la reducción de la carga cognitiva y el rápido escaneo visual por parte de los ejecutivos:
* **Codificación Visual (Accesibilidad):** Se implementó de forma transversal la paleta segura para daltónicos Okabe-Ito (azul `#0072B2` para películas, naranja `#E69F00` para series). Se evitaron los gráficos circulares con la categoría residual "Otros" para no distorsionar la interpretación proporcional del negocio.
* **Boxplot para Riesgo (G2):** Se reemplazó el tradicional gráfico de barras de promedio por Diagramas de Caja (Boxplot) para exponer la severa concentración de la popularidad y el riesgo de apostar por promedios engañosos.
* **Hexbin Logarítmico (G7):** Ante la superposición masiva (*overplotting*) de 16.000 títulos al cruzar popularidad contra calificación, se optó por un agrupamiento hexagonal de densidad, revelando el verdadero "núcleo" del catálogo, invisible en una gráfica de dispersión regular.
* **Dispersión Estratégica (G3):** En el mapeo de calidad vs engagement por género, el algoritmo de etiquetado se condicionó para rotular de forma inteligente únicamente los nichos más extremos y el de mayor tamaño, salvaguardando el área central de superposiciones de texto.


---

### 5. Construcción del dashboard interactivo
El núcleo operacional del proyecto es un dashboard interactivo en Looker Studio, diseñado como una "ventana única" de toma de decisiones.

* **Tarjetas KPI (Overview):** Panel estático de rápido consumo que consolida el total de títulos seleccionados, su peso en el catálogo, la calificación y popularidad típica, y métricas exclusivas como el `% de títulos estrella` (percentil ≥ 75, nota ≥ 7, votos fiables).
* **Filtros Globales de Control:** Un panel persistente con selectores dinámicos por Tipo de contenido, Año, Género, País e Idioma.
* **Interactividad y Navegación cruzada:** Cada componente gráfico (líneas de tiempo, barras de top géneros/países, ranking de directores) opera en red. Al interactuar con la barra de un país o director, todos los indicadores (KPIs y gráficas secundarias) recalculan automáticamente los perfiles de rentabilidad, permitiendo a la Gerencia de Contenido profundizar (*drill-down*) instantáneamente en micro-nichos sin romper la consistencia general.

### 6. Narrativa Visual (Data Storytelling)
La historia que cuentan los datos desafía los supuestos tradicionales de StreamView, organizándose en tres grandes capítulos narrativos:

1. **La Paradoja del Drama y la Acción:** Si bien el "Drama" acapara la mitad del inventario (dominancia absoluta en volumen y predictibilidad en calidad), son "Acción y Aventura" y "Ciencia Ficción" los que arrastran el mayor volumen de interacciones (*engagement*). La plataforma produce masivamente lo seguro, pero la audiencia consume masivamente el espectáculo.
2. **El Espejismo de la Unidimensionalidad:** La creencia de que un contenido muy visto es un buen contenido es falsa. Con una correlación estadística de apenas *r=0,25*, se demuestra que el *engagement* y la *calidad percibida* avanzan por vías separadas. 
3. **Rentabilidad Quirúrgica:** En el 22,1 % de las películas donde es posible medir finanzas (las series no reportan este dato), descubrimos que los mayores volúmenes de ingresos absolutos (ej. Hermanos Russo) ocultan la verdadera eficiencia. Directores enfocados en terror (James Wan) o animación familiar (Chris Renaud) entregan retornos medianos extraordinarios (>8x su costo), evidenciando que no hace falta el presupuesto más alto para dominar la rentabilidad del catálogo.

### 7. Evaluación crítica de la solución
**Fortalezas Metodológicas:**
La principal solidez del proyecto es la rigurosidad estadística aplicada a la limpieza. Aislar las calificaciones "cero" sin sustento de votos y utilizar medianas/percentiles intragrupos erradicó por completo el sesgo inflacionario, permitiendo conclusiones financieras objetivas.

**Limitaciones y Decisiones Críticas:**
* **Integridad vs. Granularidad (El Trade-Off del Dashboard):** Dado que un porcentaje mayoritario del catálogo pertenece a múltiples géneros (75 % de películas), la visualización interactiva enfrentaba el riesgo de multiplicar los títulos base en la sumatoria de los KPIs. Para salvaguardar la exactitud financiera y la fluidez de los filtros cruzados (haciendo que 1 título = 1 conteo de ingresos), **se tomó la decisión estratégica de limitar el dashboard en Looker a utilizar exclusivamente el *género principal***. Esto asegura consistencia técnica inquebrantable a costa de subestimar el volumen real de géneros transversales secundarios.
* **Ceguera Financiera de las Series:** La nulidad absoluta de ingresos y costos en el ecosistema de series restringe severamente el cálculo de un ROI unificado para la plataforma.

### 8. Conclusiones y recomendaciones
Para traducir estos hallazgos analíticos en impacto real, el equipo consultor eleva al directorio las siguientes recomendaciones:

* **Conclusión 1:** El engagement y la calidad responden a necesidades diferentes de la audiencia. La estrategia de curación no puede apoyarse en una única métrica maestra.
* **Recomendación Alta (Estrategia Bífida):** Diversificar los presupuestos. Destinar "budget" de alto riesgo a películas de *Acción y Aventura* o *Suspenso* exclusivamente para disparar la tracción de nuevos usuarios (Engagement), mientras se reserva inversión constante para *Documentales* y *Animación* (los de nota más alta) a fin de blindar el prestigio de la plataforma y la retención del suscriptor a largo plazo.

* **Conclusión 2:** Los mercados angloparlantes saturan el catálogo, ocultando el excelente desempeño cualitativo de otras regiones.
* **Recomendación Media (Diversificación Geográfica):** Priorizar adquisiciones selectivas de contenido Japonés y Francés. Ambos mercados probaron liderar el promedio global de calidad superando al bloque estadounidense, representando nichos de alta satisfacción con menor barrera competitiva.

* **Conclusión 3:** Las lagunas de recolección de datos amenazan la Inteligencia de Negocios futura.
* **Recomendación Urgente (Auditoría de Datos):** Intervenir inmediatamente el pipeline de captura de datos financieros. La imposibilidad de conocer el presupuesto del 77,9 % de las películas (y del 100 % de las series) inhabilita a la Dirección Ejecutiva de medir el éxito del grueso de su inversión, haciendo vital la integración urgente con herramientas de terceros (ej. IMDbPro) para enriquecer el repositorio histórico.
