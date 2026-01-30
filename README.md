📊 Proyecto ETL Multifuente – Pipeline de Integración y Calidad de Datos

🎯 Objetivo del Proyecto

Construir un proceso ETL completo integrando múltiples fuentes de datos con problemas reales de calidad, aplicando validaciones, reglas de negocio y generando un dataset analítico listo para visualización en dashboards.

Este proyecto simula un escenario real donde la información proviene de distintos sistemas y requiere estandarización, limpieza y control de calidad antes de ser utilizada para análisis.

🏗 Arquitectura del Pipeline

Fuentes → Staging → Limpieza → Reglas de Negocio → Dataset Final → Dashboard

📂 Fuentes de Datos

Se integraron datos provenientes de diferentes formatos:

Fuente	Formato	Descripción
Ventas Online	CSV	Ventas realizadas por e-commerce
Ventas Tienda	Excel	Ventas registradas en tiendas físicas
Clientes	JSON	Base de clientes
Stock Productos	CSV	Información de inventario
Cotización Dólar	CSV	Tipo de cambio por fecha
⚙️ Procesos Implementados
🔹 1. Normalización de Columnas

Estandarización de nombres de columnas en todas las fuentes para facilitar integraciones.

df.columns = df.columns.str.lower().str.strip()

🔹 2. Unificación de Estructuras

Homologación de nombres de campos entre ventas online y ventas en tienda física para consolidar la información.

🔹 3. Creación de Campos Técnicos

Se creó la columna:

etl_warning → registra problemas detectados durante el proceso de calidad de datos.

Esto permite mantener trazabilidad sin eliminar registros.

🔹 4. Limpieza y Validaciones de Calidad

Se implementaron controles típicos de procesos ETL reales:

Validación	Acción
Fechas inválidas	Conversión con errors='coerce' y registro en etl_warning
Cliente faltante	Asignación de ID -1 y warning
Producto nulo o vacío	Marcado en etl_warning
Nombre de cliente faltante	Warning tras integración
Email inválido	Validación de formato básico
Cotización faltante	Warning por imposibilidad de conversión a moneda local
Importe nulo	Warning
Importe convertido nulo	Warning
🔹 5. Integraciones de Datos

Unión de ventas con clientes por cliente_id

Unión con cotización del dólar por fecha

Generación de importe en moneda local:

ventas['importe_pesos'] = ventas['importe'] * ventas['cotizacion']

🔹 6. Validaciones de Reglas de Negocio

Se aplicaron reglas de plausibilidad para detectar valores imposibles:

Regla	Objetivo
Importes con magnitud extrema	Detectar errores de carga o formato
Conversión fallida de moneda	Identificar datos incompletos

Ejemplo:

ventas.loc[ventas["importe_pesos"] > 1e9, "etl_warning"] += "|importe fuera de rango|"

🔹 7. Generación de Dataset Analítico

Se creó un dataset agregado listo para visualización:

dataset_final = ventas.groupby(["fecha", "tipo_venta"]).agg({
    "importe_pesos": "sum",
    "venta_id": "count"
}).reset_index()


Métricas generadas:

Total vendido por día

Cantidad de ventas por canal

🧠 Conceptos de Data Engineering Aplicados

Este proyecto permitió aplicar conceptos clave utilizados en entornos profesionales:

✔ Normalización de datos
✔ Estandarización de esquemas
✔ Data Quality Checks
✔ Logging de errores sin perder información
✔ Reglas de negocio vs reglas técnicas
✔ Manejo de valores nulos
✔ Integración de múltiples fuentes
✔ Creación de datasets analíticos
✔ Separación entre datos crudos, procesados y analíticos

📌 Conclusiones del Proyecto

Los datos del mundo real contienen errores estructurales y de negocio.

Un proceso ETL no solo transforma datos, también documenta problemas.

No todos los valores extremos deben eliminarse; primero deben evaluarse.

La trazabilidad mediante etl_warning es clave para análisis confiables.

La calidad de datos impacta directamente en métricas y decisiones.

La integración de fuentes heterogéneas requiere estandarización previa.

🚀 Herramientas Utilizadas

Python

Pandas

Google Colab

GitHub

📈 Próximos Pasos

Construcción de dashboard en Power BI / Looker Studio

Automatización del pipeline

Incorporación de más reglas de calidad

Versionado del proceso ETL
