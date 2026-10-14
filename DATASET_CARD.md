
---
tags:
  - earth-observation
  - cadastral-dataset
  - building-footprints
  - bogota
  - colombia
  - orthophoto
  - remote-sensing
license: cc-by-4.0
dataset_name: Bogota-Urban-Polygons-Dataset
---

# Dataset Card: Conjunto de Datos de Polígonos Urbanos Bogotá (Suba y Bosa)

## Resumen del Dataset
Conjunto de datos espacial compuesto por **ortofotografías aéreas digitales ortorrectificadas a resolución de 0.20 m/píxel** y recortes satelitales, pareados con **1.250 polígonos vectoriales de referencia (*Ground Truth*)** correspondientes a huellas de edificaciones y predios en las localidades de **Suba** y **Bosa** de **Bogotá D.C., Colombia**.

El conjunto fue compilado para calibrar y evaluar modelos ligeros de digitalización y segmentación vectorial (*MobileSAM*), permitiendo el análisis comparativo entre zonas formales y de autoconstrucción.

---

## Detalles del Dataset
- **Curado por**: [Nombres y Apellidos de la Estudiante] - Asignatura: *Inteligencia Artificial Aplicada al Territorio*.
- **Fecha de compilación**: Octubre 2026.
- **Licencia**: Creative Commons Attribution 4.0 (CC-BY-4.0) para las anotaciones y máscaras derivadas; Datos fuente regulados por la política de datos abiertos de IDECA y ODbL de OpenStreetMap.
- **Sistema de Referencia Espacial (CRS)**: MAGNA-SIRGAS / Origen Nacional (EPSG:9377) y reproyectado a WGS84 (EPSG:4326) en GeoJSON.

---

## Fuentes de Datos (Dataset Sources)
1. **Imágenes Ráster:**
   * Ortofotografía oficial de Bogotá (IDECA - Infraestructura de Datos Espaciales de Bogotá).
   * Cobertura de 4 bandas (R, G, B, NIR), remuestreada a RGB visible de 8 bits en formato GeoTIFF.
   * Tamaño de mosaicos recortados: $512 \times 512$ y $1024 \times 1024$ píxeles.
2. **Polígonos de Ground Truth (Vectorial):**
   * Capa oficial de Manzanas y Construcciones de Catastro Distrital (IDECA) cruzada y validada con OpenStreetMap Buildings.
   * Filtro de calidad manual: Corrección de vértices y depuración de coberturas arbóreas oclusivas.

---

## Distribución y División del Dataset (Splits)

| Split | Número de Mosaicos Ráster | Número de Polígonos de Edificación | Porcentaje |
| :--- | :---: | :---: | :---: |
| **Train / Calibración** | 140 tiles | 875 polígonos | 70% |
| **Validation** | 30 tiles | 185 polígonos | 15% |
| **Test (Evaluación Final)** | 30 tiles | 190 polígonos | 15% |
| **Total** | **200 tiles** | **1.250 polígonos** | **100%** |

---

## Estructura de Directorios del Dataset


data/
├── aoi/
│   ├── bogota_suba_aoi.geojson        # Límite administrativo sector Suba
│   └── bogota_bosa_aoi.geojson        # Límite administrativo sector Bosa
├── raw_orthophotos/                   # GeoTIFFs originales (20 cm GSD)
│   ├── suba_mosaic_rgb.tif
│   └── bosa_mosaic_rgb.tif
├── processed_tiles/                   # Tiles de 512x512 para inferencia
│   ├── images/                        # Imágenes RGB (tile_001.png, ...)
│   └── masks/                         # Máscaras binarias ráster ground truth
├── ground_truth_vectors/              # Polígonos vectoriales verificados
│   ├── suba_buildings_gt.geojson
│   └── bosa_buildings_gt.geojson
└── splits/
    ├── train.txt
    ├── val.txt
    └── test.txt


---

## Consideraciones de Calidad y Sesgos de Muestreo

* **Condiciones lumínicas:** Las ortofotos fueron capturadas con ángulo cenital entre las 10:00 y las 14:00 horas para minimizar sombras alargadas.
* **Sesgo de Altura Edilicia:** El dataset tiene mayor representación de edificaciones entre 1 y 4 pisos (predominantes en los sectores analizados); para rascacielos o torres de más de 15 pisos con desplazamiento por abatimiento (*building lean*), se requiere corrección por modelo digital de superficie (DSM).
