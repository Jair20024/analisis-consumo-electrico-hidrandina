# Análisis de Consumo Eléctrico - Hidrandina (Q1 2025)

Proyecto de análisis de datos end-to-end: desde datos abiertos gubernamentales hasta un dashboard ejecutivo en Power BI, pasando por un proceso ETL y la carga a un Data Warehouse en SQL Server con modelado en estrella.

## 🎯 Objetivo
Analizar el consumo eléctrico y la facturación de clientes de Hidrandina (empresa distribuidora de electricidad) durante el primer trimestre de 2025 (enero-marzo), identificando patrones por ubicación geográfica, tarifa, cartera y unidad de negocio.

## 📊 Fuente de datos

Los datos provienen del portal de **[Datos Abiertos del Perú](https://www.datosabiertos.gob.pe/)**, dataset *"Consumo energético de clientes Hidrandina 2025"*.

Archivos originales utilizados (no incluidos en este repositorio por su peso, ~200 MB cada uno):
- `DatosAbiertos_consumohdna_202501.csv`
- `DatosAbiertos_consumohdna_202502.csv`
- `DatosAbiertos_consumohdna_202503.csv`

Puedes descargarlos directamente desde el portal oficial para reproducir el proceso.

## 🔧 Proceso y arquitectura

```
CSV (datos abiertos) → ETL (Google Colab) → Parquet limpio → Carga a SQL Server (modelo estrella) → Dashboard (Power BI)
```

### 1. ETL — `notebooks/ETL_Consumo_Electrico_Hidrandina.ipynb`
- Diccionario de datos
- Análisis exploratorio (EDA)
- Limpieza y transformación de los 3 archivos CSV mensuales
- Exportación del resultado a formato `.parquet`

### 2. Carga al Data Warehouse — `notebooks/CargarASQLServer_Consumo_Electrico_Hindrandina.ipynb`
- Lectura del `.parquet` limpio
- Modelado dimensional en **esquema estrella**:
  - `Dim_Cliente` (ubicación: departamento, provincia, distrito)
  - `Dim_Tarifa` (tarifa, cartera)
  - `Dim_Sucursal` (unidad de negocio)
  - `Dim_Fecha` (fecha, año, mes)
  - `Fact_Factura` (importe, consumo)
- Conexión y carga a SQL Server mediante SQLAlchemy, creación de tablas con llaves foráneas y verificación de la carga (~3.1 millones de registros)

### 3. Dashboard — `dashboard/Dashboard_Consumo_Electrico_Hidrandina.pbix`
Construido en Power BI, conectado directamente a la base de datos `HidrandinaDW` en SQL Server. Incluye:
- Medidas DAX: Total Consumo, Total Importe, Total Clientes
- Tarjetas con indicadores generales
- Mapa de burbujas del consumo por ubicación, con segmentadores por departamento/provincia/distrito
- Gráfico circular de importe por cartera (Común / Mayor)
- Gráfico circular de importe por tarifa (con agrupación "Otros" para tarifas menores)
- Gráfico de columnas: consumo por mes
- Gráfico de barras: consumo por unidad de negocio

## 🛠️ Tecnologías
- **Python** (pandas, sqlalchemy) — ETL y carga
- **SQL Server** — Data Warehouse
- **Power BI** (DAX) — Visualización y dashboard
