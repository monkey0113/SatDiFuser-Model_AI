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

