# Glosario de Variables y Métricas — StreamView Analytics (EP3)

Fuente principal del dashboard y de la infografía (D4, D12), generada por `src/kpis.py`:
- `data/processed/looker_contenido.csv`: tabla maestra de 31.991 filas (una fila por título) y 22 columnas. Fuente única conectada a Looker Studio.
- `data/processed/looker_genero.csv`: tabla desagregada por género utilizada exclusivamente para contraste y verificación interna frente a los notebooks. No se conecta a Looker Studio.

Todos los indicadores responden a los filtros interactivos. Las proporciones agregadas se calculan como la suma del numerador dividida por el total de registros del subconjunto, evitando el promedio de porcentajes precalculados.

---

## 1. Variables del dataset maestro (`looker_contenido.csv`)

* **content_id:** Identificador alfanumérico único por título ("M-" para películas y "S-" para series). Llave primaria que resuelve el solapamiento de identificadores originales entre formatos.
* **title:** Nombre oficial de la obra audiovisual.
* **tipo_contenido:** Clasificación del formato entre Película y Serie.
* **es_pelicula:** Variable binaria que asigna valor 1 a películas y 0 a series.
* **release_year:** Año de estreno oficial de la obra, en formato numérico entero (2010 a 2025). Permite control temporal continuo.
* **idioma:** Código de idioma original de producción de la obra.
* **genero_principal:** Primer género registrado del título según la taxonomía homologada en español. Asigna "No especificado" si carece de registro. Excluye telefilms ("TV Movie").
* **pais_principal:** Primer país de producción registrado, normalizado al español. Asigna "No especificado" si carece de registro.
* **director_principal:** Primer director registrado. Unifica la variante "Anthony Russo / Joe Russo" y asigna "No especificado" en obras sin registro.
* **popularity:** Índice de popularidad bruto de TMDb, basado en volumen de interacciones, búsquedas y visitas recientes.
* **indice_engagement:** Métrica estandarizada de percentil (escala 0 a 100) calculada dentro de cada tipo de contenido para eliminar asimetrías de escala y permitir comparabilidad.
* **alto_engagement:** Indicador binario con valor 1 si el título pertenece al percentil 75 o superior de su tipo (cuartil superior de interacción), y 0 en caso contrario.
* **es_estrella:** Indicador binario con valor 1 si el título cumple conjuntamente: percentil de engagement ≥ 75, calificación promedio ≥ 7,0 y respaldo de al menos 10 votos.
* **vote_count:** Conteo total de evaluaciones emitidas por los usuarios.
* **vote_average_clean:** Calificación promedio de la audiencia en escala 0 a 10. Contiene valor nulo para títulos con 0 votos para evitar el sesgo de calificar con cero títulos sin evaluación.
* **confiable_num:** Indicador binario con valor 1 si la calificación cuenta con al menos 10 votos (`vote_count` ≥ 10), y 0 si tiene menos de 10 votos.
* **financiero_num:** Indicador binario con valor 1 si el título cuenta simultáneamente con presupuesto e ingresos mayores a cero, y 0 en caso contrario.
* **presupuesto_roi:** Presupuesto de producción en USD asignado al título, restringido a registros con datos financieros válidos.
* **ingresos_roi:** Recaudación global en taquilla en USD asignada al título, restringida a registros con datos financieros válidos.
* **roi:** Retorno de inversión individual de la obra, calculado como recaudación dividida por presupuesto (`ingresos_roi / presupuesto_roi`).
* **es_drama:** Indicador binario con valor 1 si el título incluye Drama entre cualquiera de sus géneros registrados (principales o secundarios), y 0 en caso contrario.
* **es_eeuu:** Indicador binario con valor 1 si el título incluye a Estados Unidos entre cualquiera de sus países de producción (principales o secundarios), y 0 en caso contrario.

---

## 2. Métricas e indicadores de Looker Studio

* **Títulos totales:** Conteo de obras únicas presentes en la selección activa (`COUNT_DISTINCT(content_id)`). Base: una fila por título.
* **% del catálogo:** Proporción de títulos seleccionados respecto al total fijo unificado de 31.991 obras (`COUNT_DISTINCT(content_id) / 31991`). Denominador fijo.
* **Calificación promedio:** Media aritmética de las calificaciones válidas (`AVG(vote_average_clean)`). Excluye títulos sin votos.
* **% con votos suficientes:** Proporción de títulos cuya evaluación posee respaldo estadístico suficiente (`SUM(confiable_num) / COUNT_DISTINCT(content_id)`). Umbral de confiabilidad: ≥ 10 votos.
* **% alto engagement:** Proporción de títulos pertenecientes al percentil 75 o superior de su formato (`SUM(alto_engagement) / COUNT_DISTINCT(content_id)`). Vale 25 % a nivel global por definición metodológica.
* **% de títulos estrella:** Proporción de obras de alto rendimiento simultáneo en interacción, evaluación crítica y respaldo muestral (`SUM(es_estrella) / COUNT_DISTINCT(content_id)`).
* **% con Drama (todos sus géneros):** Proporción de títulos que registran Drama en cualquiera de sus posiciones de género (`SUM(es_drama) / COUNT_DISTINCT(content_id)`). Coincide con G4 (43,2 % en películas, 49,1 % en series).
* **% con EE. UU. (todos los países):** Proporción de títulos con producción en Estados Unidos en cualquier orden de registro (`SUM(es_eeuu) / COUNT_DISTINCT(content_id)`). Coincide con G6 (48,5 % en películas, 20,0 % en series).
* **Cobertura financiera:** Porcentaje de películas que cuentan con registros válidos de presupuesto y recaudación (`SUM(financiero_num) / SUM(es_pelicula)`). Representa el 22,1 % de las películas; las series poseen 0 % de cobertura.
* **ROI agregado:** Retorno financiero acumulado del conjunto analizado (`SUM(ingresos_roi) / SUM(presupuesto_roi)`). Requiere un mínimo de 5 títulos con datos al evaluar directores.
* **ROI mediano:** Valor mediano del rendimiento financiero individual (`roi`) para títulos con información financiera. Representa 1,70× a escala global.

---

## 3. Criterios de asignación y limitaciones metodológicas

* **Atribución principal (género, país y director):** Para garantizar que cada título compute una sola vez en tablas y filtros cruzados, el dashboard emplea exclusivamente el primer valor listado en la fuente original. Las ausencias se agrupan bajo "No especificado" (1.088 títulos sin género, 2.261 sin país y 11.092 sin director), categoría excluida de los rankings de categorías.
* **Diferencia entre EDA y Dashboard:** En el análisis exploratorio (G4, G6) los títulos se cuentan en cada categoría secundaria que posean, mientras que en el dashboard computan una única vez. Por ello, las proporciones por atributo principal difieren de la cobertura transversal (Drama en películas: 22,9 % principal vs. 43,2 % transversal; EE. UU. en películas: 35,6 % principal vs. 48,5 % transversal; EE. UU. en series: 15,5 % principal vs. 20,0 % transversal). Las variables `es_drama` y `es_eeuu` restituyen la medición transversal sin duplicar registros.
* **Exclusión de telefilms:** Las producciones catalogadas como "TV Movie" (628 títulos) constituyen un formato de emisión televisiva y no un género temático, por lo cual se excluyen de la dimensión de género (D11).
* **Directores unificados:** Se homologan las variantes del dúo de realizadores a "Anthony Russo / Joe Russo" y se depuran espacios y formatos de mayúsculas.
* **Confiabilidad en evaluaciones:** Las calificaciones de obras con menos de 10 votos presentan alta dispersión y riesgo de manipulación muestral, por lo que los análisis de dispersión y correlación calidad-engagement aíslan los títulos con `confiable_num = 1`.