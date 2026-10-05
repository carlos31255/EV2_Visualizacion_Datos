# StreamView Analytics — EP3 / EP4: Visualización de Datos
**Asignatura:** ADY1104 Visualización de Datos  
**Institución:** Duoc UC  
**Proyecto:** StreamView Analytics (Evaluación Práctica 3 y 4)  

---

## 📌 Descripción del Proyecto

StreamView Analytics es una plataforma internacional de streaming digital. Este proyecto aborda la **integración de múltiples fuentes de datos** (unificación de catálogos heterogéneos de **películas** y **series** con más de 31.900 títulos totales), la resolución de colisiones de identificadores y taxonomías de género, el análisis exploratorio (EDA), la definición de indicadores clave de rendimiento (**KPIs**) y la construcción de un **dashboard interactivo** junto con una narrativa visual (*Data Storytelling*) para respaldar decisiones ejecutivas de adquisición, inversión y retención de audiencias.

---

## 📂 Estructura del Repositorio

El proyecto sigue una arquitectura modular y profesional orientada a la reproducibilidad:

```text
EV2/
├── data/
│   ├── raw/          # Datasets crudos originales (inmutables)
│   │   ├── netflix_movies_detailed_up_to_2025.csv
│   │   └── netflix_tv_shows_detailed_up_to_2025.csv
│   └── processed/    # Datasets unificados, tablas largas y agregaciones limpias
│       ├── contenido_unificado.csv
│       ├── contenido_por_genero.csv
│       ├── contenido_por_pais.csv
│       ├── contenido_por_director.csv
│       ├── mapa_generos.csv
│       └── control_calidad.csv
├── notebooks/        # Jupyter Notebooks numerados cronológicamente
│   └── 01_integracion_y_limpieza.ipynb
├── src/              # Módulos Python reutilizables (pipeline de datos y lógica)
│   └── limpieza.py
├── dashboard/        # Aplicación, componentes y capturas del dashboard interactivo
├── images/           # Gráficos estáticos exportados en alta resolución (PNG)
├── reports/          # Entregables formales: informe ejecutivo (PDF), resumen visual
├── .gitignore        # Exclusión de entornos virtuales, cachés y archivos temporales
├── requirements.txt  # Lista de librerías y dependencias necesarias
└── README.md         # Documentación general e instrucciones de despliegue
```

---

## ⚙️ Guía de Configuración del Entorno Virtual (`venv`)

Para garantizar la reproducibilidad y evitar conflictos entre librerías, se debe utilizar un entorno virtual de Python (**Python 3.10 o superior** recomendado).

### 1. Clonar el repositorio y posicionarse en la carpeta raíz
```bash
git clone https://github.com/carlos31255/EV2_Visualizacion_Datos.git
cd EV2_Visualizacion_Datos
```
*(O si ya tienes el repositorio descargado, simplemente abre una terminal en la raíz del proyecto).*

### 2. Crear el entorno virtual
Ejecutar el módulo estándar `venv` para crear una carpeta local `.venv`:
```bash
python -m venv .venv
```

### 3. Activar el entorno virtual

* **En Windows (PowerShell):**
  ```powershell
  .\.venv\Scripts\Activate.ps1
  ```
  > *Nota:* Si PowerShell restringe la ejecución de scripts en tu sistema, ejecuta previamente en la misma consola:
  > ```powershell
  > Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
  > ```

* **En Windows (Símbolo del sistema / CMD):**
  ```cmd
  .\.venv\Scripts\activate.bat
  ```

* **En macOS / Linux:**
  ```bash
  source .venv/bin/activate
  ```

*(Una vez activado, verás el prefijo `(.venv)` al inicio de la línea de comandos).*

### 4. Actualizar `pip` e instalar dependencias
Con el entorno virtual activo, instala todas las dependencias del proyecto:
```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 5. Registrar el kernel en Jupyter (para Notebooks)
Para que Jupyter Notebook reconozca el entorno virtual con todas sus librerías instaladas:
```bash
python -m ipykernel install --user --name=ev2-streamview --display-name "Python (.venv - StreamView EV2)"
```

---

## 🚀 Guía de Ejecución Reproducible

### Paso 1: Ejecutar el Pipeline de Integración y Limpieza
El procesamiento completo de unificación de fuentes se ejecuta desde la raíz mediante:
```bash
python src/limpieza.py
```
Este script:
1. Carga los datasets crudos desde `data/raw/`.
2. Resuelve la colisión de identificadores con claves compuestas (`content_id`).
3. Homologa taxonomías de género y limpia formatos de calificación/engagement.
4. Aplica una batería de **15 controles automáticos de calidad**.
5. Exporta las tablas finales normalizadas a `data/processed/`.

### Paso 2: Ejecutar los Notebooks Analíticos
Inicia el servidor de Jupyter:
```bash
jupyter notebook
```
Abre `notebooks/01_integracion_y_limpieza.ipynb` y asegúrate de seleccionar en el menú superior:  
`Kernel` -> `Change Kernel` -> **`Python (.venv - StreamView EV2)`**.

### Paso 3: Lanzar el Dashboard Interactivo
*(Si se utiliza la solución basada en Streamlit):*
```bash
streamlit run dashboard/app.py
```

---

## 📋 Reglas de Reproducibilidad y Buenas Prácticas
* **Rutas relativas:** Todo script o notebook utiliza rutas relativas respecto a la raíz del proyecto (`data/raw/`, `data/processed/`, etc.). No se permiten rutas absolutas locales.
* **Inmutabilidad:** Los archivos en `data/raw/` no se modifican bajo ninguna circunstancia. Toda transformación genera datasets procesados en `data/processed/`.
* **Desactivar el entorno:** Al finalizar la sesión de trabajo, puedes salir del entorno virtual escribiendo:
  ```bash
  deactivate
  ```
