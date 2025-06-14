# 📊 Workflows ETL Databricks — SaaS 2025

## 🚀 Descripción del Proyecto

Este repositorio contiene un pipeline **ETL completo y modular**, desarrollado en **Databricks** usando **PySpark**, para procesar el dataset **Top 100 SaaS Companies 2025**.

El flujo implementa la **Medallion Architecture (Bronze → Silver → Gold)** para garantizar:
- ✔️ Datos trazables y auditables.
- ✔️ Limpieza y transformación robusta.
- ✔️ Agregaciones y métricas listas para análisis de negocio.

> **Compatible con Databricks Free Edition y Azure Databricks.**

## 📂 Estructura del Data Lake

\`\`\`plaintext
FileStore/saas/
 ├── bronze/               → Ingesta cruda + tracking de metadata
 ├── silver/
 │   ├── valid/            → Datos limpios y validados
 │   ├── invalid/          → Registros inválidos para auditoría
 ├── gold/
     ├── top20_by_valuation/
     ├── top20_by_arr/
     ├── by_industry/
     ├── summary_stats/
     ├── outliers/
     ├── high_g2_rating/
\`\`\`

Cada carpeta se guarda como archivos **Delta o Parquet**, simulando un **Data Lake tipo HDFS**, optimizado para integrarse con herramientas de BI o pipelines externos.

## ⚙️ Componentes del Pipeline

### ✅ 1️⃣ Bronze Layer — Ingesta Cruda
- Carga el archivo CSV original.
- Agrega metadata: nombre de archivo + timestamp de ingesta.
- Guarda como archivos **Delta** en `/FileStore/saas/bronze/`.

### ✅ 2️⃣ Silver Layer — Limpieza y Validación
- Lee la capa Bronze.
- Limpia formatos monetarios y escala valores (M, B, T).
- Convierte tipos de datos y valida integridad (e.g. `ARR` no mayor que `Valuation`).
- Separa registros **válidos** e **inválidos** para auditoría.
- Guarda resultados en `/FileStore/saas/silver/valid/` y `/invalid/`.

### ✅ 3️⃣ Gold Layer — Agregaciones Avanzadas
- Lee la Silver Valid.
- Calcula:
  - Top 20 compañías por `Valuation` y `ARR`.
  - Estadísticas por industria: conteo de empresas, promedio de `Valuation` y `ARR`, empleados totales.
  - Estadísticas de dispersión: desviación estándar, mediana, percentiles.
  - Identifica outliers (`Valuation` y `ARR` superiores al percentil 95).
  - Segmento de empresas con `G2 Rating` alto.
- Guarda cada vista en subcarpetas dentro de `/FileStore/saas/gold/`.

## 🔄 Orquestación Recomendada

- **Notebooks sugeridos:**
  - `01_bronze_ingestion`
  - `02_silver_cleaning`
  - `03_gold_aggregation`

- **Workflow en Databricks Jobs:**
  1. **Task 1:** Bronze Ingestion  
  2. **Task 2:** Silver Cleaning (depende de Task 1)  
  3. **Task 3:** Gold Aggregation (depende de Task 2)

- **Extra:** Agregar notificaciones de éxito/fallo por email o Slack.
- **Recomendado:** Programar ejecución diaria o según necesidades del negocio.

## ✅ Cómo Replicar este Proyecto

### 1️⃣ Pre-requisitos
- Crear cuenta en [Databricks Community Edition](https://community.cloud.databricks.com/) **(gratis)** o tener acceso a **Azure Databricks**.
- Subir el archivo CSV `top_100_saas_companies_2025.csv` a `/FileStore/tables/`.

### 2️⃣ Importar Notebooks
- Subir e importar los notebooks del repositorio.
- Verificar y adaptar rutas: `bronze_path`, `silver_path`, `gold_path`.

### 3️⃣ Crear Cluster
- Recomendado: Databricks Runtime 11.x o superior.
- Habilitar autoscaling para optimizar costos y rendimiento.

### 4️⃣ Ejecutar Paso a Paso
- Ejecutar `01_bronze_ingestion`.
- Ejecutar `02_silver_cleaning`.
- Ejecutar `03_gold_aggregation`.

### 5️⃣ Verificar Resultados
- Navegar en Databricks FileStore: `dbutils.fs.ls("dbfs:/FileStore/saas/")`
- Conectar notebooks, Power BI, Tableau o tu herramienta de BI favorita para usar las capas **Gold**.

## 💡 Notas Adicionales

- **Formato de salida:** Bronze & Silver en Delta; Gold en Parquet.
- **Escalabilidad:** Pipeline apto para clusters de producción en Azure Databricks.
- **Integración:** Fácil de combinar con **Azure Data Factory**, **Synapse**, **Airflow**, u otros orquestadores.
- **Límites Free Edition:** Máximo 15 GB; para cargas reales se recomienda un cluster escalable en Azure.

## 👨‍💻 Autor

**Daniel Santos Pareja**  
🚀 Data Engineer | DataHackers & Data Wizard Club  
📬 *Disponible para workshops, mentorías o consultoría personalizada.*

## 📎 Archivos incluidos

- `01_bronze_ingestion.py`
- `02_silver_cleaning.py`
- `03_gold_aggregation.py`
- `top_100_saas_companies_2025.csv`



