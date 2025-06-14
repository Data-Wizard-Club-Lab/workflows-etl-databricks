# workflows-etl-databricks
🚀 Descripción del Proyecto
Este repositorio contiene un pipeline ETL completo y modular, desarrollado en Databricks usando PySpark, que procesa el dataset Top 100 SaaS Companies 2025.

Se implementa la Medallion Architecture (Bronze ➜ Silver ➜ Gold) para garantizar:

Datos trazables y auditables.

Limpieza y transformación robusta.

Agregaciones y métricas listas para análisis de negocio.

👉 Compatible con Databricks Free Edition y Azure Databricks.

📌 Arquitectura del Pipeline
bash
Copiar
Editar
📁 FileStore/saas/
 ├── bronze/          # Ingesta cruda + tracking de metadata
 ├── silver/
 │   ├── valid/       # Datos limpios y validados
 │   ├── invalid/     # Registros inválidos para auditoría
 ├── gold/
     ├── top20_by_valuation/
     ├── top20_by_arr/
     ├── by_industry/
     ├── summary_stats/
     ├── outliers/
     ├── high_g2_rating/
Cada carpeta se guarda como archivos Delta o Parquet dentro de DBFS (FileStore), simulando un data lake tipo HDFS.

⚙️ Componentes
✅ 1️⃣ Bronze Layer — Ingesta cruda

Carga el CSV original.

Agrega metadata: nombre de archivo + timestamp de ingesta.

Guarda como archivos Delta en /FileStore/saas/bronze/.

✅ 2️⃣ Silver Layer — Limpieza y Validación

Lee Bronze.

Limpia formatos monetarios y escala valores (M, B, T).

Convierte tipos de datos.

Valida integridad: ARR no mayor que Valuation.

Separa registros válidos e inválidos.

Guarda en /FileStore/saas/silver/valid/ y /invalid/.

✅ 3️⃣ Gold Layer — Agregaciones Avanzadas

Lee Silver Valid.

Calcula:

Top 20 compañías por Valuation y ARR.

Estadísticas por industria: conteo, promedio, empleados totales.

Estadísticas de dispersión: stddev, mediana, percentiles.

Identifica outliers (Valuation y ARR superiores al P95).

Filtra empresas con G2 Rating alto.

Guarda cada vista en subcarpetas dentro de /FileStore/saas/gold/.

🔄 Orquestación Recomendada
Crear 3 notebooks:

01_bronze_ingestion

02_silver_cleaning

03_gold_aggregation

Crear un Workflow en Databricks Jobs:

Task 1: Bronze ➜ Task 2: Silver ➜ Task 3: Gold.

Opcional: añadir notificaciones de éxito/fallo por email o Slack.

Programar ejecución periódica (ej. diaria).

✅ Cómo Replicar
1️⃣ Pre-requisitos

Tener cuenta en Databricks Community Edition (gratis) o acceso a Azure Databricks.

Subir el archivo CSV top_100_saas_companies_2025.csv a /FileStore/tables/.

2️⃣ Importar Notebooks

Importar los notebooks del repositorio.

Revisar las rutas (bronze_path, silver_path, gold_path).

3️⃣ Crear Cluster

Runtime: Databricks Runtime 11.x o superior.

Autoscaling habilitado si deseas optimizar recursos.

4️⃣ Ejecutar Paso a Paso

Ejecutar 01_bronze_ingestion.

Luego 02_silver_cleaning.

Finalmente 03_gold_aggregation.

5️⃣ Consultar resultados

Usar dbutils.fs.ls("dbfs:/FileStore/saas/") para explorar carpetas.

Conectar notebooks o herramientas BI para consumir las capas Gold.

💡 Notas
Formato: El pipeline guarda en formato Delta (Bronze, Silver) y Parquet (Gold).

Escalabilidad: Compatible con clústers de producción en Azure Databricks.

Portabilidad: Puede integrarse con ADF, Synapse o Airflow.

Costos: En Free Edition se limita a 15 GB; para grandes volúmenes usar cluster escalable en Azure.

📣 Autor
Daniel Santos Pareja
🚀 Data Engineer | DataHackers & Data Wizard Club
💬 Contáctame para workshops, consultoría o colaboraciones.

