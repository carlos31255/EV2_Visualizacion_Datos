# NOTA 02 — Retroalimentación de EP1 y foco en atributos visuales / carga cognitiva

**Fecha:** 05/10/2026 · **Depende de:** `01_analisis_y_plan.md` (no la reemplaza; la complementa) · **Estado:** diagnóstico hecho, nada construido todavía.

---

## 1. Retroalimentación recibida en EP1

1. El enlace al dashboard de Looker Studio quedó como placeholder en el informe; la docente no pudo evaluar la interactividad real.
2. El gráfico **"Top géneros"** del dashboard en vivo no reflejaba lo del notebook y el informe (Drama dominando).
3. Puntaje no máximo en dos indicadores:

| Indicador | Peso | Puntaje EP1 | Categoría | Comentario de la docente |
|---|---|---|---|---|
| IE5. Atributos visuales (color, tamaño, posición, contraste, forma) | 16 % | 12,8 | Buen desempeño (80 %) | Uso correcto con leves oportunidades de mejora |
| IE6. Visualizaciones que favorecen la comprensión y minimizan la carga cognitiva | 20 % | 16 | Buen desempeño (80 %) | Visualizaciones adecuadas con pequeñas dificultades |

**Decisión del usuario:** lo del enlace de Looker Studio se deja **para el final**. La decisión de herramienta del dashboard para EP3 (Plotly + Streamlit/Dash, Power BI o Looker) sigue **abierta** (ver nota 01, sección 6).

---

## 2. Diagnóstico de la discrepancia de "Top géneros" (verificado con el CSV de películas)

En el notebook de EP1 el gráfico 3.2 hace dos cosas a la vez:
- **Ordena** los 10 géneros por *densidad* (calificación prom / popularidad prom).
- **Dibuja** la longitud de las barras con la *cantidad de títulos*.

Resultado real del ranking por densidad (películas): TV Movie (0,596), Documentary (0,547), Music (0,438), History (0,414), **Drama (0,368, puesto 5 de 20)**, Western, Mystery, Comedy, Romance, War. Es decir, el notebook **no** muestra a Drama arriba, aunque sí tiene la barra más larga (6.910 títulos).

En Looker, el orden por defecto es por la métrica graficada (títulos), por eso allí Drama aparece primero. Cada versión es "correcta" a su manera, pero el lector ve resultados distintos y además el gráfico mezcla dos criterios: **el orden dice una cosa y el largo de la barra dice otra**. Eso es exactamente un problema de carga cognitiva (IE6).

**Segunda inconsistencia relacionada (hallada al revisar el código):** el boxplot (3.4) toma `top5 = top_generos_tabla.index[:5]`, o sea los 5 primeros por **densidad** (TV Movie, Documentary, Music, History, Drama). Pero el informe (7.5) dice "5 géneros más frecuentes" y comenta Drama, Comedy, Thriller y Action. Hay que decidir qué géneros se muestran realmente y que texto, gráfico y dashboard digan lo mismo.

**Regla para EP3 — "una métrica, un criterio":** lo que ordena, lo que codifica la longitud y lo que dice el título del gráfico deben ser la misma métrica. Si se quiere mostrar volumen *y* densidad, usar dos gráficos o un solo gráfico con ambas métricas visibles (ej. barras = títulos, punto/marcador = densidad, claramente rotulado).

---

## 3. Checklist de diseño para subir IE5 e IE6 (aplicar en todos los gráficos y en el dashboard)

### IE5 — Atributos visuales
- **Color con significado fijo y único en todo el proyecto:** Película = un color, Serie = otro (ej. azul y naranja), usado igual en gráficos estáticos, dashboard e informe. No reutilizar esos colores para otra cosa.
- **Paleta apta para daltonismo** (evitar rojo/verde como único diferenciador) y verificar contraste del texto sobre fondo (mínimo 4,5:1).
- **Color secuencial para magnitudes, categórico para tipos:** no mezclar.
- **Posición y longitud para comparar** (lo más legible); tamaño solo como apoyo (burbujas) y siempre con leyenda; forma solo para distinguir 2–3 categorías.
- **Resaltar lo importante:** un color de énfasis y el resto en gris neutro (ej. destacar la barra del género del mensaje).
- Escalas: barras siempre desde cero; escala logarítmica solo con aviso visible en el eje.
- Sin 3D, sin degradados decorativos, sin gráficos de torta con más de 3–4 categorías.

### IE6 — Carga cognitiva
- **Título = mensaje** (ej. "Las series tienen mayor calificación, pero el 38 % de ellas tiene votos suficientes") y subtítulo con métrica, unidad y base de cálculo.
- **Un mensaje por gráfico**, máximo 5–7 categorías visibles (top N), resto agrupado en "Otros".
- **Rotular directamente** valores clave en vez de depender de leyendas lejanas; ejes con unidades; quitar líneas de grilla y bordes innecesarios.
- **Orden consistente** (mayor a menor o cronológico) y declarado en el subtítulo.
- **Dashboard:** jerarquía visual (KPIs arriba, detalle abajo), una cuadrícula alineada, mismo orden y ubicación de filtros en todas las páginas, valores por defecto razonables, y una página de "cómo leer este dashboard / limitaciones" breve.
- **Cada gráfico del informe declara** problema de negocio y audiencia (feedback previo) **y** por qué ese tipo de gráfico minimiza el esfuerzo de lectura.

### Control de consistencia (evita repetir la discrepancia de EP1)
- Antes de cerrar cada fase, comparar **3 cifras clave por gráfico** entre notebook, dashboard e informe (mismo valor, mismo orden). Dejar el resultado en la nota de esa fase.
- Generar las tablas del dashboard y las figuras del informe **desde el mismo código/CSV** (`src/` + `data/processed/`), nunca recalcular a mano en la herramienta.

---

## 4. Cambios al plan de la nota 01

- **Fase 2 (EDA/estáticos) y Fase 4 (dashboard):** incorporar el checklist de la sección 3 como criterio de aceptación; cada gráfico lleva su ficha (problema, audiencia, mensaje, métrica de orden = métrica visual).
- **Fase 5 (informe):** incluir una subsección en "Justificación de las representaciones gráficas" que explique las decisiones de atributos visuales y carga cognitiva (esto apunta directo a IE5/IE6 de la rúbrica de EP1 y es la evaluación crítica de EP3).
- **Fase 6 (QA):** agregar tarea "cruzar cifras notebook ↔ dashboard ↔ informe" y, al final, **completar el enlace/entrega del dashboard** (el pendiente de Looker queda aquí si se mantiene esa herramienta).
- Sigue pendiente la decisión de herramienta y las otras preguntas abiertas de la nota 01.

---

## 5. Pendiente y cómo continuar
Primer paso para el siguiente agente: esperar la decisión sobre la herramienta del dashboard y, luego, ejecutar la **Fase 0** (estructura de carpetas) y la **Fase 1** (integración y limpieza), dejando la nota `03_...`.
