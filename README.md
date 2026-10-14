# MobileSAM-Polygon-Cadastre-Bogota

Extracción y digitalización semi-automática de polígonos de huellas de edificaciones y predios urbanos en las localidades de **Suba** (tejido urbano consolidado y regular) y **Bosa** (tejido urbano denso y de autoconstrucción) en **Bogotá, Colombia**, utilizando el modelo fundacional ultraligero **MobileSAM** (*Faster Segment Anything*, Zhang et al., 2023) acoplado a un pipeline de vectorización y simplificación geométrica hacia **GeoJSON / Shapefile**.

Este proyecto evalúa la capacidad de transferencia de un modelo de visión por computador ligero (peso de **39 MB**, inferencia en tiempo real en CPU) a la digitalización catastral y cartográfica urbana, proporcionando métricas de exactitud geométrica desagregadas por tipología urbana, resolución espacial de sensor y densidad de puntos *prompt*, siguiendo el marco metodológico de **Model Cards** (Mitchell et al., 2019).

---

## 📄 Fichas del Proyecto

* **Model Card**: [MODEL_CARD.md](./MODEL_CARD.md)
* **Dataset Card**: [DATASET_CARD.md](./DATASET_CARD.md)

---

## 🎯 Resultados Principales de Evaluación

Evaluación sobre **1.250 polígonos de validación** contrastados con la cartografía oficial vectorial de Catastro Bogotá (IDECA) a escala 1:1.000:

| Métrica de Exactitud | Sector Suba (Regular) | Sector Bosa (Orgánico) | Global (Promedio Ponderado) |
| :--- | :---: | :---: | :---: |
| **IoU (Jaccard Index)** | **84.32%** | **76.15%** | **80.88%** |
| **F1-Score (Dice Coeff)** | **91.49%** | **86.46%** | **89.43%** |
| **Precision (Pureza)** | **93.10%** | **88.20%** | **91.09%** |
| **Recall (Completitud)** | **89.92%** | **84.78%** | **87.82%** |
| **Error en Área ($m^2$ RMSE)** | **4.12 $m^2$** | **8.65 $m^2$** | **6.18 $m^2$** |
| **Latencia promedio CPU** | **185 ms** | **192 ms** | **188 ms** |

> **Nota**: Métricas obtenidas tras la inferencia de MobileSAM con modo *1-point prompt* central + corrección de polígono mediante Douglas-Peucker ($\epsilon = 0.5\text{ m}$).

---

## 🗂️ Estructura del Proyecto
MobileSAM-Polygon/ ├── MODEL_CARD.md # Ficha del modelo (Mitchell et al. 2019 + HF template) ├── DATASET_CARD.md # Ficha del dataset (HF template) ├── README.md # Este archivo principal ├── .gitignore ├── config.yaml # Configuración de AOI, umbrales y rutas ├── requirements.txt # Dependencias Python ├── data/ │ ├── aoi/ │ │ ├── bogota_suba_aoi.geojson │ │ └── bogota_bosa_aoi.geojson │ ├── ground_truth/ # Polígonos oficiales IDECA / OSM en GeoJSON │ └── raw/ # Ortofotos aéreas 0.2m / PlanetScope 3m ├── scripts/ │ ├── 01_download_aoi.py # Descarga límites y capas base de Bogotá │ ├── 02_tiling_orthophoto.py # División en tiles de 512x512 y 1024x1024 │ ├── 03_setup_mobilesam.py # Descarga de pesos de MobileSAM (39 MB) │ ├── 04_interactive_segment.py # Segmentación por clics o cajas │ ├── 05_mask_to_polygon.py # Vectorización raster -> GeoJSON (shapely) │ ├── 06_evaluate_metrics.py # Cálculo de IoU, F1 y error de área │ └── 07_generate_figures.py # Generación de mapas y matrices de error ├── notebooks/ │ ├── 01_exploratory_analysis.ipynb │ └── 02_polygon_visualization.ipynb └── outputs/ ├── polygons_geojson/ # Polígonos vectorizados generados ├── metrics_summary.csv # Resultados desagregados └── figures/ # Comparativas visuales (Pred vs GT)



---

## ⚙️ Pipeline de Ejecución

```bash
# 1. Clonar el repositorio
git clone https://github.com/tu-usuario/MobileSAM-Polygon-Cadastre-Bogota.git
cd MobileSAM-Polygon-Cadastre-Bogota

# 2. Instalar dependencias
pip install -r requirements.txt

# 3. Descargar el checkpoint ligero de MobileSAM (39 MB)
python scripts/03_setup_mobilesam.py

# 4. Procesar tiles de ortofoto y generar polígonos vectoriales
python scripts/04_interactive_segment.py --aoi suba
python scripts/05_mask_to_polygon.py --simplify-tolerance 0.5

# 5. Calcular métricas oficiales de exactitud posicional y temática
python scripts/06_evaluate_metrics.py

Python: 3.9 / 3.10 / 3.11
Hardware: Funciona en CPU estándar (Intel i5/i7 o AMD Ryzen, 8 GB RAM). No requiere GPU obligatoria.
Espacio en disco: < 500 MB (incluyendo modelo de 39 MB y datos de prueba).
pip install torch torchvision
pip install timm opencv-python shapely geopandas rasterio matplotlib
pip install git+https://github.com/ChaoningZhang/MobileSAM.git
