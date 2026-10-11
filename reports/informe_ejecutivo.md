# Capítulo 2: Diseño de Dashboards Interactivo

### 1. Descripción del problema de negocio
**Contexto organizacional:**
StreamView Analytics es una plataforma internacional de streaming digital que requiere comprender en profundidad el desempeño de su catálogo histórico de películas y series para optimizar sus estrategias de retención, engagement y adquisición de nuevos títulos. Para este proyecto se trabajó con dos fuentes que describen el catálogo (popularidad, calificaciones de la audiencia y, solo en películas, datos financieros); por ello el análisis se centra en el desempeño del contenido y no en usuarios individuales.

**Pregunta de negocio:**
¿Cómo debería StreamView repartir su inversión en adquisición entre películas y series, y en qué géneros y países?

**Audiencia Objetivo:** 
El informe está dirigido a dos actores clave:
* **Gerencia de Contenido:** Responsable de tomar decisiones de adquisición y renovación (qué géneros y países priorizar).
* **Dirección Ejecutiva:** Responsable de aprobar los presupuestos de inversión, validar la rentabilidad (ROI) y entender los riesgos estructurales de los datos.

**Objetivos de Comunicación:**
Persuadir al equipo directivo de que (1) las series y las películas presentan perfiles de consumo totalmente distintos, (2) el *engagement* (popularidad) y la *calidad percibida* (calificación) están solo débilmente asociados (r = 0,25 en películas y 0,13 en series), por lo que exigen estrategias de adquisición separadas, y (3) existen deficiencias críticas en los datos de la plataforma (ej. falta de finanzas) que limitan la visibilidad del negocio.

### 2. Fuentes de datos e Integración
La información se construyó a partir de dos fuentes crudas: `netflix_movies_detailed_up_to_2025.csv` y `netflix_tv_shows_detailed_up_to_2025.csv` (16.000 títulos cada una). Tras eliminar 9 series con `show_id` repetido (todas de 2025), el catálogo integrado queda en 31.991 títulos: 16.000 películas y 15.991 series.

**Proceso de Integración y Variables Relevantes:**
* **Llave Compuesta:** Se detectó un solapamiento de 397 `show_id` distintos entre ambos archivos, por lo que se generó la llave única `content_id` ("M-" para películas, "S-" para series) asegurando la integridad relacional.
* **Limpieza de Ceros y Votos:** Se identificó que las calificaciones de "0" correspondían a títulos sin interacción, no a malas evaluaciones. Se creó `vote_average_clean` (que anula las notas sin votos) y `calificacion_confiable` para aislar títulos con un sustento de audiencia real (≥ 10 votos).
* **Normalización de Engagement:** Debido a que la escala de popularidad difería radicalmente (mediana de 10,9 en películas vs. 36,2 en series), se generó un `indice_engagement` (percentil de 0 a 100 intrapolo) para permitir comparativas justas.
* **Control de Géneros (Data Quality):** Se homologaron las dos taxonomías dispares de las fuentes originales a un mapa de géneros único en español. Adicionalmente, se extrajeron 628 títulos catalogados como "TV Movie", marcándolos como `es_telefilm` y excluyéndolos de la dimensión de géneros al tratarse de un formato y no de un atributo narrativo.
* **Salud Financiera:** Se aislaron 3.540 películas con datos no nulos en ingresos y presupuesto para el cálculo seguro del Retorno de Inversión (ROI).
* **Tabla del dashboard:** A partir del catálogo integrado se generó `looker_contenido.csv` (una fila por título, 22 columnas), fuente única del dashboard y de la infografía. Incluye el género, el país y el director principal, los indicadores calculados (`alto_engagement`, `es_estrella`, `confiable_num`, `financiero_num`) y, para los dos hallazgos centrales, las marcas `es_drama` y `es_eeuu`.

### 3. Análisis exploratorio mediante visualizaciones
La exploración se realizó en Python (notebook 02) con ocho gráficos, G1 a G8. Cada uno responde a una pregunta de negocio y se presenta a continuación con su hallazgo. En estos gráficos un título cuenta en cada género y país que lista.
* **G1 · Sesgo de calificación:** Las series ostentan un promedio de calidad percibida mucho mayor que las películas (7,02 frente a 6,31), pero una proporción dramáticamente menor de sus títulos ha logrado acumular suficientes votos de la audiencia (38,4 % vs. 88,6 %).
[IMAGEN — insertar aquí: images/g1_calidad_y_cobertura.png | Figura 1. Calificación promedio y % de títulos con 10 o más votos, por tipo de contenido.]
* **G2 · Asimetría extrema:** La popularidad y los ingresos presentan colas largas hacia la derecha (valores extremos muy altos). La popularidad promedio (20,4 en películas y 64,9 en series) queda muy por encima de la mediana (10,9 y 36,2), y el ROI promedio (781,5×) está distorsionado por unos pocos éxitos, frente a una mediana de 1,70×. Esto invalidó el uso de promedios simples y forzó al equipo a utilizar medianas y percentiles para proteger los insights de los "outliers".
[IMAGEN — insertar aquí: images/g2_popularidad_boxplot_iqr.png | Figura 2. Distribución de la popularidad por tipo (boxplot; los atípicos se excluyen del dibujo y se cuantifican).]
* **G3 · Engagement y calidad por género:** Los géneros más vistos no son los mejor calificados. En películas, Acción y Aventura y Ciencia ficción y Fantasía lideran el engagement entre los géneros grandes, mientras que Documental y Música lideran la calidad. Animación es la excepción, porque combina ambos.
[IMAGEN — insertar aquí: images/g3_engagement_calidad_genero.png | Figura 3. Engagement mediano vs calificación promedio (≥ 10 votos) por género, para películas y series.]
* **G4 · Volumen por género:** El Drama es el género más frecuente en películas (43 %) y en series (49 %) contando todos los géneros de cada título; la oferta está concentrada en pocos géneros.
[IMAGEN — insertar aquí: images/g4_volumen_por_genero.png | Figura 4. % de títulos por género (top 7), por tipo. Un título puede tener varios géneros, por lo que los porcentajes no suman 100 %.]
* **G5 · Tendencia por año:** Entre 2010 y 2024 la calificación promedio se mueve en rangos estrechos (6,1 a 6,5 en películas y 6,8 a 7,4 en series), sin quiebres marcados. La cobertura de votos es alta en películas (82 % a 98 %) y creciente en series (30 % a 50 %). Los títulos de 2025 casi no tienen votos (9 % de las películas y 7 % de las series con 10 o más votos), por lo que su nota aún no es fiable.
[IMAGEN — insertar aquí: images/g5_evolucion_por_anio.png | Figura 5. Calificación promedio y % de títulos con 10 o más votos, por año de estreno y tipo.]
* **G6 · Países de producción:** EE. UU. concentra el 48,5 % de las películas pero solo el 20,0 % de las series, donde China y Japón pesan casi lo mismo entre sí.
[IMAGEN — insertar aquí: images/g6_paises.png | Figura 6. % de títulos por país (top 7), por tipo.]
* **G7 · Relación entre popularidad y calificación:** La asociación es débil pero existe (r = 0,25 en películas y 0,13 en series, solo títulos con 10 o más votos): un título muy visto no garantiza una buena nota.
[IMAGEN — insertar aquí: images/g7_engagement_vs_calidad.png | Figura 7. Engagement (percentil) vs calificación, con densidad por hexágono en escala logarítmica.]
* **G8 · ROI por director (solo películas):** Entre los directores con al menos 5 películas con datos financieros, el mayor ROI agregado es de 8,8× (Chris Renaud).
[IMAGEN — insertar aquí: images/g8_roi_por_director.png | Figura 8. ROI agregado por director (mínimo 5 películas con datos), con línea de referencia de 1×.]
* **Limitación del Dataset:** El hallazgo de que el volumen de producción por año es artificial (1.000 títulos por año y tipo; 991 en series de 2025) impidió realizar análisis volumétricos temporales, reorientando el EDA exclusivamente al desempeño de las variables.

### 4. Diseño e implementación de visualizaciones
Los gráficos G1 a G8 se implementaron con Python (pandas y matplotlib) en el notebook `02_eda_visualizaciones.ipynb`, y el dashboard y la infografía con Looker Studio. Las decisiones gráficas priorizaron la reducción de la carga cognitiva y el rápido escaneo visual por parte de los ejecutivos:
* **Codificación Visual (Accesibilidad):** Se implementó de forma transversal la paleta segura para daltónicos Okabe-Ito (azul `#0072B2` para películas, naranja `#E69F00` para series) y gris para lo no destacado. Se evitaron los gráficos circulares con la categoría residual "Otros" para no distorsionar la interpretación proporcional del negocio.
* **Criterios de representación:** Una métrica corresponde a un solo criterio (el orden, el largo y el título de un gráfico usan la misma medida), los porcentajes se calculan sobre una base fija y los gráficos de categorías muestran solo categorías reales, con el Top N rotulado en el título.
* **Barras (G1, G4, G6, G8):** Permiten comparar valores por categoría desde cero. En G1 muestran dos valores por tipo; en G4, barras agrupadas permiten comparar película y serie por género; en G6 ordenan países con nombres largos; en G8 ordenan el ROI con una línea de 1× como punto de equilibrio.
* **Boxplot para Riesgo (G2):** Se reemplazó el tradicional gráfico de barras de promedio por Diagramas de Caja (Boxplot) para exponer la severa concentración de la popularidad y el riesgo de apostar por promedios engañosos.
* **Dispersión Estratégica (G3):** En el mapeo de calidad vs engagement por género, el algoritmo de etiquetado se condicionó para rotular de forma inteligente únicamente los nichos más extremos y el de mayor tamaño, salvaguardando el área central de superposiciones de texto.
* **Líneas (G5):** Muestran la tendencia temporal, con etiquetas directas al final de cada línea y una franja gris en el año reciente, cuya nota aún no es fiable.
* **Hexbin Logarítmico (G7):** Ante la superposición masiva (*overplotting*) de 16.000 títulos al cruzar popularidad contra calificación, se optó por un agrupamiento hexagonal de densidad, revelando el verdadero "núcleo" del catálogo, invisible en una gráfica de dispersión regular.


---

### 5. Construcción del dashboard interactivo
El núcleo operacional del proyecto es un dashboard interactivo en Looker Studio, diseñado como una "ventana única" de toma de decisiones.
[ENLACE — pegar aquí el enlace público del dashboard, verificado en una ventana de incógnito]

* **Tarjetas KPI (Overview):** Panel dinámico de rápido consumo que reacciona a todos los filtros activos y consolida seis indicadores: total de títulos, peso en el catálogo, calificación promedio, porcentaje con votos suficientes, porcentaje de alto engagement (percentil ≥ 75 dentro de su tipo) y porcentaje de títulos estrella (percentil ≥ 75, nota ≥ 7 y al menos 10 votos).
* **Filtros y navegación:** Panel de filtros globales (Tipo, Año, Género, País, Idioma) en la parte superior de cada página, y menú de navegación entre las cinco páginas del dashboard.
* **Interactividad y Navegación cruzada:** Los filtros operan en red con todos los componentes, garantizando consistencia técnica en el cálculo de KPIs al profundizar (*drill-down*) en micro-nichos.
La estructura del dashboard se organiza en cinco páginas estratégicas:

* **Página 1, Resumen y Tendencias:** Incluye un gráfico de dispersión (Calidad vs Engagement) por género, con la calidad medida solo en títulos de al menos 10 votos, para respaldar visualmente que la asociación es débil (r = 0,25 en películas y 0,13 en series). Adicionalmente, líneas de tiempo desglosadas por tipo de contenido usando la paleta segura para daltónicos (azul películas, naranja series).
[IMAGEN — insertar aquí: dashboard/pagina_1_resumen.png | Figura 9. Dashboard, página 1: Resumen y Tendencias.]
* **Página 2, Película vs. Serie:** Comparativa volumétrica y cualitativa. Incorpora la *cobertura de votos fiables* para advertir que el 7,02 de promedio de las series está apoyado en menos del 40 % de su catálogo.
[IMAGEN — insertar aquí: dashboard/pagina_2_pelicula_vs_serie.png | Figura 10. Dashboard, página 2: Película vs. Serie.]
* **Página 3, Exploración de Categorías:** Top 7 de géneros por número de títulos (excluyendo "No especificado"), y Top 7 de países también por volumen (los 7 primeros son EE. UU., Japón, China, Corea del Sur, Reino Unido, Canadá y Francia), junto a una tabla con la calificación (con cualquier voto y con al menos 10 votos) y el porcentaje de títulos con votos suficientes de esos países. Incluye además tarjetas con el porcentaje de títulos que contienen Drama y EE. UU. entre todos sus géneros y países.
[IMAGEN — insertar aquí: dashboard/pagina_3_categorias.png | Figura 11. Dashboard, página 3: Exploración de Categorías.]
* **Página 4, Desempeño Financiero:** Foco en directores con mínimo 5 películas con datos financieros, exponiendo recaudación total y ROI agregado. Incluye tarjetas de cobertura financiera y ROI global.
[IMAGEN — insertar aquí: dashboard/pagina_4_financiero.png | Figura 12. Dashboard, página 4: Desempeño Financiero.]
* **Página 5, Metodología y Limitaciones:** Sección declarativa para transparentar las decisiones y sesgos de visualización adoptados.
[IMAGEN — insertar aquí: dashboard/pagina_5_metodologia.png | Figura 13. Dashboard, página 5: Metodología y Limitaciones.]

### 6. Narrativa Visual (Data Storytelling)
La historia que cuentan los datos desafía los supuestos tradicionales de StreamView, organizándose en tres grandes capítulos narrativos:

1. **La Paradoja del Drama y la Acción:** Si bien el Drama acapara gran parte del inventario (43 % de las películas y 49 % de las series considerando todos los géneros) (dominancia absoluta en volumen y predictibilidad en calidad), son Acción y Aventura y Ciencia ficción y Fantasía (junto a Animación, con 60,3) los que, entre los géneros grandes de películas, arrastran el mayor volumen de interacciones (*engagement*; índices promedio de 60,6 y 60,4). En series, en cambio, lideran Telenovela (70,9) y Talk show (60,4). La plataforma produce masivamente lo seguro, pero la audiencia consume masivamente el espectáculo.
2. **El Espejismo de la Unidimensionalidad:** La creencia de que un contenido muy visto es un buen contenido es falsa. Al observar que la asociación es débil (r = 0,25 en películas y 0,13 en series), se demuestra que un título muy visto no garantiza una buena nota. 
3. **Rentabilidad Quirúrgica:** En el 22,1 % de las películas donde es posible medir finanzas (las series no reportan este dato), descubrimos que los mayores volúmenes de ingresos absolutos (ej. Hermanos Russo) ocultan la verdadera eficiencia. Directores enfocados en terror (James Wan, ROI agregado 6,2×, mediana 8,0×) o animación familiar (Chris Renaud, ROI agregado 8,8×, mediana 8,8×) lideran la eficiencia financiera [G8]. En el dashboard los valores varían levemente porque se usa el primer director listado por título, no la lista completa.

**Adaptación del mensaje a la audiencia y recursos de comunicación:**
* **Gerencia de Contenido** (qué géneros y países priorizar): recibe los capítulos 1 y 2, apoyados en las páginas 1 a 3 del dashboard.
* **Dirección Ejecutiva** (presupuestos, rentabilidad y riesgos de los datos): recibe el capítulo 3 y las limitaciones, apoyados en las páginas 4 y 5 del dashboard.
* **Recursos escritos, visuales y orales:** el informe entrega el detalle con cifras y notas metodológicas; el dashboard permite explorar los datos; y la infografía de una página, con la paleta accesible y la conclusión escrita junto a cada gráfico, resume los hallazgos y sirve de apoyo para la exposición oral.
[IMAGEN — insertar aquí: dashboard/infografia.png | Figura 14. Infografía "Radiografía de StreamView: Decisiones de Inversión".]

### 7. Evaluación crítica de la solución
**Fortalezas Metodológicas:**
La principal solidez del proyecto es la rigurosidad estadística aplicada a la limpieza. Aislar las calificaciones "cero" sin sustento de votos y utilizar medianas/percentiles intragrupos redujo sustancialmente el sesgo inflacionario en películas (88,6 % con votos fiables). En series, el sesgo persiste: solo el 38,4 % tiene votos suficientes y la mediana es de apenas 4 votos por título (frente a 138 en películas), por lo que sus promedios de calificación deben leerse con reserva. Además, cada indicador se calculó por dos vías independientes (pandas y un cálculo al estilo Looker) y se contrastó en la base completa, por tipo y en 30 selecciones aleatorias, y las cifras del EDA, del dashboard y del informe se contrastaron entre sí.

**Limitaciones y Decisiones Críticas:**
* **Integridad vs. Granularidad (El Trade-Off del Dashboard):** Dado que un porcentaje mayoritario del catálogo pertenece a múltiples géneros (75 % de películas), la visualización interactiva enfrentaba el riesgo de multiplicar los títulos base en la sumatoria de KPIs. Para salvaguardar la exactitud (haciendo que 1 título = 1 conteo de ingresos), **se limitó el dashboard a utilizar exclusivamente la primera categoría listada (género, país y director principal)**. Esto subestima las apariciones secundarias y asume, sin verificación, que el primer elemento listado es el dominante (ej. el "Drama" baja del 43,2 % de participación real al 22,9 % en películas por este efecto). El mismo efecto ocurre con el país: EE. UU. pasa de 48,5 % de las películas (contando todos los países de producción) a 35,6 % como país principal. Para no perder el valor real en estos dos hallazgos centrales, el dashboard incluye tarjetas que cuentan todos los géneros y países de cada título (Drama y EE. UU.) sin duplicar el conteo de títulos.
* **Ausencias ("No especificado") y Exclusiones:** Se aislaron volúmenes sustanciales de información ausente: 1.088 títulos sin género, 2.261 sin país, y 11.092 sin director (este último afectando al 68,5 % del catálogo de series). Adicionalmente, 628 películas para televisión ("TV Movie") fueron excluidas del listado de géneros por ser un formato de distribución, no un género narrativo (Decisión D11).
* **Corte Artificial de Volumen Temporal:** Se detectó que el dataset fue muestreado artificialmente para contener 1.000 títulos por año y tipo (991 en series de 2025). Esta restricción invalidó por completo el análisis de volúmenes de producción a lo largo del tiempo, forzando a estudiar únicamente los promedios de las variables.
* **Ceguera Financiera de las Series:** La nulidad absoluta de ingresos y costos en el ecosistema de series restringe severamente el cálculo de un ROI unificado para la plataforma.

**Justificación de las principales decisiones de diseño:**
* **Ceros de calificación:** un cero con cero votos significa "sin votos" y no una mala nota; por eso se excluyó de los promedios y se declaró el cambio respecto de la entrega anterior.
* **Fuente única con una fila por título:** garantiza que los filtros cruzados y los KPIs sean consistentes; su costo es el uso del género, país y director principal, declarado en las limitaciones.
* **Porcentajes sobre base fija:** todo porcentaje es la suma del numerador dividida por la cuenta de registros, y el peso en el catálogo usa el total fijo de 31.991 títulos; así las cifras no cambian de significado al filtrar.
* **Sin "engagement promedio" como indicador:** por construcción vale 50 en cada tipo y no informa; se usan el porcentaje de alto engagement y el de títulos estrella.
* **Gráficos de categorías:** solo categorías reales, con el Top N en el título y sin "Otros" ni "No especificado", para no distorsionar la lectura proporcional.
* **Telefilms:** "TV Movie" es un formato y no un género, por lo que se marca con `es_telefilm` y se excluye de la dimensión de género.
* **Marcas `es_drama` y `es_eeuu`:** recuperan el valor real de los dos hallazgos centrales sin duplicar títulos.

**Oportunidades de mejora:**
* **Completar los datos financieros:** integrar fuentes externas (por ejemplo IMDbPro) para ampliar la cobertura del 22,1 % de las películas y obtener ROI también para las series.
* **Contar todas las apariciones sin duplicar:** modelar género, país y director como relaciones de muchos a muchos (por ejemplo, combinando la tabla larga con la de títulos en Looker) o validar si el primer elemento listado es realmente el dominante.
* **Sumar datos de comportamiento:** reproducciones, suscripciones o abandono permitirían medir la retención de forma directa, y no solo la popularidad y la calificación.
* **Calificaciones más robustas:** ponderar la nota por número de votos y reevaluar los títulos de 2025 cuando acumulen votos.
* **ROI mediano por director:** incorporarlo al dashboard junto al ROI agregado, como ya se hace en el análisis exploratorio.
* **Actualización automática:** conectar el dashboard a una fuente que se actualice sola, en lugar de un archivo CSV estático.

### 8. Conclusiones y recomendaciones
Para traducir estos hallazgos analíticos en impacto real, el equipo consultor eleva al directorio las siguientes recomendaciones:

* **Conclusión 1:** El engagement y la calidad responden a necesidades diferentes de la audiencia. La estrategia de curación no puede apoyarse en una única métrica maestra.
* **Recomendación Alta (Estrategia Bífida):** Diversificar los presupuestos. Destinar "budget" de alto riesgo a películas de *Acción y Aventura* o *Ciencia ficción y Fantasía* exclusivamente para disparar la tracción de nuevos usuarios (Engagement), mientras se reserva inversión constante para *Documentales* y *Animación* (los de nota más alta) a fin de blindar el prestigio de la plataforma y la retención del suscriptor a largo plazo.

* **Conclusión 2:** Los mercados angloparlantes saturan el catálogo, ocultando el excelente desempeño cualitativo de otras regiones.
* **Recomendación Media (Diversificación Geográfica):** Priorizar adquisiciones selectivas de contenido japonés, chino y coreano. Dentro de cada tipo de contenido, estos mercados superan consistentemente a EE. UU.: en películas (Japón 6,92, Corea 6,85, China 6,53 vs. EE. UU. 6,21) y en series (China 7,53, Japón 7,46, Corea 7,34 vs. EE. UU. 7,19). La comparación a nivel global mezcla tipos y debe leerse con cautela, dado que los mercados asiáticos concentran mayor proporción de series, que puntúan más en promedio. China, además, tiene solo el 28 % de sus títulos con votos suficientes (Japón 71 %, Corea del Sur 51 %), por lo que su ventaja debe leerse con cautela. (Nota: estas cifras usan el país principal, solo títulos con al menos 10 votos y separan por tipo de contenido; la tabla del dashboard incluye cualquier título con votos y mezcla ambos tipos, por lo que sus valores son distintos.)

* **Conclusión 3:** Las lagunas de recolección de datos amenazan la Inteligencia de Negocios futura.
* **Recomendación Urgente (Auditoría de Datos):** Intervenir inmediatamente el pipeline de captura de datos financieros. La imposibilidad de conocer presupuesto e ingresos simultáneamente para el 77,9 % de las películas (sin presupuesto solo: 69,7 %; en series: 100 %) inhabilita a la Dirección Ejecutiva de medir el éxito del grueso de su inversión, haciendo vital la integración urgente con herramientas de terceros (ej. IMDbPro) para enriquecer el repositorio histórico.
