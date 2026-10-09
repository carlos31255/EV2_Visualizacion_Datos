# Glosario de KPIs — StreamView Analytics (EP3)

Fuente única del dashboard y de la infografía (D4, D12), generada por `src/kpis.py`:
- `data/processed/looker_contenido.csv`: una fila por título (22 columnas). Es lo único que se conecta a Looker Studio.
- `data/processed/looker_genero.csv`: tabla larga (una fila por título y género) que `kpis.py` genera solo para verificar cifras contra el notebook. No se conecta a Looker.

Todos los KPIs responden a los filtros del dashboard. Los porcentajes se calculan como suma del numerador dividida por la cuenta de registros, nunca como promedio de porcentajes.

| KPI | Definición | Cálculo en Looker | Base / límites |
|---|---|---|---|
| Títulos | Cantidad de títulos en la selección | COUNT_DISTINCT(content_id) | Una fila por título |
| % del catálogo | Peso de la selección en el catálogo | COUNT_DISTINCT(content_id) / 31991 | Total unificado (denominador fijo) |
| Calificación promedio | Nota promedio de la audiencia (0–10) | AVG(vote_average_clean) | Solo títulos con ≥ 1 voto; los 0 sin votos no son nota |
| % con votos suficientes | Títulos cuya nota es confiable | SUM(confiable_num) / COUNT_DISTINCT(content_id) | Confiable = ≥ 10 votos. Todo gráfico de calidad debe mostrar esta cobertura |
| % de alto engagement | Títulos entre el 25 % más popular de su tipo | SUM(alto_engagement) / COUNT_DISTINCT(content_id) | Alto = percentil ≥ 75 dentro del tipo; vale 25 % por construcción en el catálogo completo, informa al filtrar |
| % de títulos estrella | Títulos estrella (engagement y calificación) | SUM(es_estrella) / COUNT_DISTINCT(content_id) | Estrella = percentil ≥ 75, nota ≥ 7, ≥ 10 votos |
| % con Drama (todos sus géneros) | Títulos que incluyen Drama entre sus géneros | SUM(es_drama) / COUNT_DISTINCT(content_id) | Cuenta el Drama aunque no sea el género principal; películas 43,2 %, series 49,1 % (igual que G4). No suma 100 % con otros géneros |
| % con EE. UU. (todos los países) | Títulos con EE. UU. entre sus países de producción | SUM(es_eeuu) / COUNT_DISTINCT(content_id) | Cuenta EE. UU. aunque no sea el país principal; películas 48,5 %, series 20,0 % (igual que G6) |
| ROI agregado | Retorno financiero del conjunto | SUM(ingresos_roi) / SUM(presupuesto_roi) | Solo películas con presupuesto e ingresos > 0. Por director: mínimo 5 películas con datos |
| ROI mediano | Retorno de la película típica | Mediana de ingresos/presupuesto (`roi`) | Solo películas con datos; robusto a atípicos; si Looker no ofrece MEDIAN, usar el valor de `kpis_resumen.csv` (global 1,70×) |
| Cobertura financiera | % de películas con datos financieros | SUM(financiero_num) / SUM(es_pelicula) | Las series no tienen datos financieros |

## Género, país y director principal
Para que cada título cuente una sola vez, el dashboard usa `genero_principal`, `pais_principal` y `director_principal`: el primer elemento listado de cada título. Es un supuesto no verificado (el primero listado podría no ser el dominante). Reglas:
- Sin dato: "No especificado" (1.088 títulos sin género, 2.261 sin país, 11.092 sin director). Se excluye de los gráficos de categorías.
- `director_principal`: se unifica a "Anthony Russo / Joe Russo" y se limpian espacios y mayúsculas.
- Los telefilms ("TV Movie", 628 títulos) no cuentan como género.

## Diferencia entre el EDA y el dashboard (limitación declarada)
Los gráficos del notebook (G4, G6) cuentan todos los géneros y países de cada título; el dashboard usa solo el principal. Por eso las cifras difieren:

| Cifra | EDA (todos) | Dashboard (principal) |
|---|---|---|
| Drama, % de películas | 43,2 % | 22,9 % |
| EE. UU., % de películas | 48,5 % | 35,6 % |
| EE. UU., % de series | 20,0 % | 15,5 % |

Para no perder el valor real en los dos hallazgos centrales, el CSV incluye `es_drama` y `es_eeuu` (1 si el título tiene ese género o país en su lista completa), que alimentan las tarjetas "% con Drama" y "% con EE. UU.". El resto de las cifras por género o país del dashboard son "principal" y así deben rotularse. Los directores también usan solo el primero: en el EDA (G8) Renaud tiene 6 películas y ROI 8,8×, y en el dashboard 5 y 9,0×.

## Definiciones de calificación
- G1 y G5 del notebook, y `AVG(vote_average_clean)` en Looker: cualquier título con votos (se acompaña del % con ≥ 10 votos).
- G3 y G7 del notebook, y la dispersión de Looker (filtro `confiable_num = 1`): solo títulos con ≥ 10 votos.

## Cambios respecto a EP1
Las calificaciones de EP1 incluían ceros que en realidad eran títulos sin votos; aquí se excluyen.