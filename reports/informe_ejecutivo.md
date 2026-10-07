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

* **Tarjetas KPI (Overview):** Panel estático de rápido consumo que consolida el total de títulos, calificación, cobertura de votos, alto engagement y métricas exclusivas como el `% de títulos estrella`.
* **Interactividad y Navegación cruzada:** Panel de filtros globales (Tipo, Año, Género, País, Idioma) que opera en red con todos los componentes, garantizando consistencia técnica en el cálculo de KPIs al profundizar (*drill-down*) en micro-nichos.
* **Estructura en 5 Páginas Estratégicas:** 
  1. **Resumen y Tendencias:** Incluye un gráfico de dispersión (Calidad vs Engagement) por género para respaldar visualmente el hallazgo central del informe (la independencia de ambas variables, r=0,25). Adicionalmente, líneas de tiempo desglosadas por tipo de contenido usando la paleta segura para daltónicos.
  2. **Película vs. Serie:** Comparativa volumétrica y cualitativa. Incorpora la *cobertura de votos fiables* para advertir que el 7,02 de promedio de las series está apoyado en menos del 40 % de su catálogo.
  3. **Exploración de Categorías:** Top 7 de géneros y países filtrados para excluir el ruido estadístico.
  4. **Desempeño Financiero:** Foco en directores (con filtros de mínimo 5 películas), exponiendo su recaudación total y su ROI agregado, con tarjetas financieras específicas de cobertura.
  5. **Metodología y Limitaciones:** Sección declarativa para transparentar los sesgos de visualización adoptados.

### 6. Narrativa Visual (Data Storytelling)
La historia que cuentan los datos desafía los supuestos tradicionales de StreamView, organizándose en tres grandes capítulos narrativos:

1. **La Paradoja del Drama y la Acción:** Si bien el "Drama" acapara la mitad del inventario (dominancia absoluta en volumen y predictibilidad en calidad), son "Acción y Aventura" y "Ciencia Ficción" los que arrastran el mayor volumen de interacciones (*engagement*). La plataforma produce masivamente lo seguro, pero la audiencia consume masivamente el espectáculo.
2. **El Espejismo de la Unidimensionalidad:** La creencia de que un contenido muy visto es un buen contenido es falsa. Con una correlación estadística de apenas *r=0,25*, se demuestra que el *engagement* y la *calidad percibida* avanzan por vías separadas. 
3. **Rentabilidad Quirúrgica:** En el 22,1 % de las películas donde es posible medir finanzas (las series no reportan este dato), descubrimos que los mayores volúmenes de ingresos absolutos (ej. Hermanos Russo) ocultan la verdadera eficiencia. Directores enfocados en terror (James Wan) o animación familiar (Chris Renaud) entregan retornos medianos extraordinarios (>8x su costo), evidenciando que no hace falta el presupuesto más alto para dominar la rentabilidad del catálogo.

### 7. Evaluación crítica de la solución
**Fortalezas Metodológicas:**
La principal solidez del proyecto es la rigurosidad estadística aplicada a la limpieza. Aislar las calificaciones "cero" sin sustento de votos y utilizar medianas/percentiles intragrupos erradicó por completo el sesgo inflacionario, permitiendo conclusiones financieras objetivas.

**Limitaciones y Decisiones Críticas:**
* **Integridad vs. Granularidad (El Trade-Off del Dashboard):** Dado que un porcentaje mayoritario del catálogo pertenece a múltiples géneros (75 % de películas), la visualización interactiva enfrentaba el riesgo de multiplicar los títulos base en la sumatoria de KPIs. Para salvaguardar la exactitud (haciendo que 1 título = 1 conteo de ingresos), **se limitó el dashboard a utilizar exclusivamente la primera categoría listada (género, país y director principal)**. Esto subestima las apariciones secundarias y asume, sin verificación, que el primer elemento listado es el dominante (ej. el "Drama" baja del 43,2 % de participación real al 22,9 % en películas por este efecto).
* **Ausencias ("No especificado") y Exclusiones:** Se aislaron volúmenes sustanciales de información ausente: 1.088 títulos sin género, 2.261 sin país, y 11.092 sin director (este último afectando al 68,5 % del catálogo de series). Adicionalmente, 628 películas para televisión ("TV Movie") fueron excluidas del listado de géneros por generar ruido estadístico.
* **Corte Artificial de Volumen Temporal:** Se detectó que el dataset fue muestreado artificialmente para contener *exactamente* 1.000 títulos por año y tipo de contenido. Esta restricción invalidó por completo el análisis de volúmenes de producción a lo largo del tiempo, forzando a estudiar únicamente las promedios de variables.
* **Ceguera Financiera de las Series:** La nulidad absoluta de ingresos y costos en el ecosistema de series restringe severamente el cálculo de un ROI unificado para la plataforma.

### 8. Conclusiones y recomendaciones
Para traducir estos hallazgos analíticos en impacto real, el equipo consultor eleva al directorio las siguientes recomendaciones:

* **Conclusión 1:** El engagement y la calidad responden a necesidades diferentes de la audiencia. La estrategia de curación no puede apoyarse en una única métrica maestra.
* **Recomendación Alta (Estrategia Bífida):** Diversificar los presupuestos. Destinar "budget" de alto riesgo a películas de *Acción y Aventura* o *Suspenso* exclusivamente para disparar la tracción de nuevos usuarios (Engagement), mientras se reserva inversión constante para *Documentales* y *Animación* (los de nota más alta) a fin de blindar el prestigio de la plataforma y la retención del suscriptor a largo plazo.

* **Conclusión 2:** Los mercados angloparlantes saturan el catálogo, ocultando el excelente desempeño cualitativo de otras regiones.
* **Recomendación Media (Diversificación Geográfica):** Priorizar adquisiciones selectivas de contenido Japonés y Francés. Ambos mercados probaron liderar el promedio global de calidad superando al bloque estadounidense, representando nichos de alta satisfacción con menor barrera competitiva.

* **Conclusión 3:** Las lagunas de recolección de datos amenazan la Inteligencia de Negocios futura.
* **Recomendación Urgente (Auditoría de Datos):** Intervenir inmediatamente el pipeline de captura de datos financieros. La imposibilidad de conocer el presupuesto del 77,9 % de las películas (y del 100 % de las series) inhabilita a la Dirección Ejecutiva de medir el éxito del grueso de su inversión, haciendo vital la integración urgente con herramientas de terceros (ej. IMDbPro) para enriquecer el repositorio histórico.
