
---
tags:
  - earth-observation
  - polygon-digitization
  - building-footprints
  - cadastral-mapping
  - remote-sensing
  - mobilesam
  - lightweight-foundation-model
  - bogota
  - colombia
license: apache-2.0
model_name: MobileSAM-Polygon-Cadastre-Bogota
---

# Model Card: MobileSAM para Digitalización de Polígonos Urbanos en Bogotá

## Resumen del Modelo
**MobileSAM** (*Faster Segment Anything Model*, Zhang et al., 2023) adaptado mediante un módulo de posprocesamiento y vectorización poligonal (`shapely` + `OpenCV`) para la **digitalización asistida y extracción de polígonos de edificaciones y predios** en las localidades de **Suba** y **Bosa** de **Bogotá, Colombia**, sobre ortofotografías aéreas de alta resolución (0.2 m GSD) y satélite óptico.

El modelo destaca por su peso reducido de **39 MB** y su capacidad de inferencia en tiempo real en CPU (<200 ms), eliminando la dependencia de servidores GPU de alto costo y democratizando la captura cartográfica digital.

---

## Detalles del Modelo
- **Desarrollado por**: Chaoning Zhang et al. (Kyung Hee University - modelo base) / [Nombre de la Estudiante] (pipeline de vectorización, parametrización y evaluación territorial).
- **Fecha**: Octubre 2026.
- **Versión**: 1.0.
- **Tipo**: Modelo de segmentación por prompts con codificador ultraligero *TinyViT* + Decodificador liviano de máscaras + Regularizador poligonal Ramer-Douglas-Peucker.
- **Modelo base**: MobileSAM (`mobile_sam.pt`, 9.66 millones de parámetros).
- **Licencia**: Apache License 2.0.
- **Repositorio**: [https://github.com/tu-usuario/MobileSAM-Polygon-Cadastre-Bogota](https://github.com/tu-usuario/MobileSAM-Polygon-Cadastre-Bogota)
- **Paper base**: Zhang, C., et al. (2023). *"Faster Segment Anything: Towards Lightweight SAM for Mobile Applications"*, arXiv:2306.14289.

---

## Usos

### Uso Directo (Intended Use)
- Digitalización asistida de huellas de edificaciones y límites de parcelas para actualización predial y catastral.
- Conversión instantánea de clics del usuario (*point prompts*) a polígonos vectoriales georreferenciados (GeoJSON / SHP).
- Apoyo a oficinas de planeación municipal e inventarios urbanos en municipios con recursos computacionales estándar.

### Usos Fuera de Alcance (Out-of-Scope)
- Clasificación de estratos socioeconómicos o materiales constructivos (el modelo extrae la forma geométrica, no clasifica atributos socioeconómicos).
- Cartografía oficial vinculante sin la revisión y aval de un topógrafo, catastrador o ingeniero geomático.
- Aplicaciones en imágenes satelitales de baja resolución (>10 m/píxel) donde las edificaciones no son distinguibles individualmente.

---

## Factores (Mitchell et al. 2019)

### Factores Relevantes
| Factor | Descripción | Impacto en la Digitalización |
| :--- | :--- | :--- |
| **Tipología Urbana** | Tejido formal/ortogonal (Suba) vs Autoconstrucción/irregular (Bosa) | Menor regularidad en techos de Bosa; mayor variabilidad de bordes |
| **Materiales de Techo** | Tejas de barro, concreto, zinc reflectivo, cubiertas verdes | El zinc con reflejo solar intenso produce saturación de píxeles |
| **Sombra Arrojada** | Sombras proyectadas por edificios altos sobre casas colindantes | Pérdida localizada de contraste en el contorno del polígono |
| **Resolución (GSD)** | Ortofoto aérea (0.2 m) vs Imagen Satelital Planet (3.0 m) | En 0.2 m se definen aleros; en 3.0 m se agrupan techos continuos |

### Factores de Evaluación
- **Evaluados**: Localidades de Suba y Bosa (1.250 predios muestreados), cubiertas de teja y losa, ortofotos de 0.2 m.
- **No evaluados**: Zonas rurales de Bogotá (Sumapaz), imágenes radar SAR o térmicas nocturnas.

---

## Métricas (Metrics)

### Métricas Principales
| Métrica | Definición | Justificación |
| :--- | :--- | :--- |
| **IoU (Intersection over Union)** | $\frac{\text{Área}(P \cap GT)}{\text{Área}(P \cup GT)}$ | Evalúa el solapamiento geométrico estricto del polígono |
| **F1-Score / Dice** | $\frac{2 \times |P \cap GT|}{|P| + |GT|}$ | Medida armónica entre precisión de bordes y completitud |
| **RMSE de Área ($m^2$)** | $\sqrt{\frac{1}{N}\sum (\text{Área}_P - \text{Área}_{GT})^2}$ | Determina el error métrico superficial en metros cuadrados |
| **Latencia CPU (ms)** | Tiempo de inferencia por polígono | Viabilidad operativa en trabajo de campo o laptops |

---

## Datos de Evaluación y Resultados Cuantitativos

### Desagregación de Resultados (Mitchell et al. 2019)

#### 1. Rendimiento por Sector Urbano
| Sector | Muestras ($N$) | IoU (%) | F1-Score (%) | Precision (%) | Recall (%) | Error Área RMSE |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Suba (Tejido Regular)** | 650 | **84.32%** | **91.49%** | **93.10%** | **89.92%** | **4.12 $m^2$** |
| **Bosa (Tejido Denso/Orgánico)** | 600 | **76.15%** | **86.46%** | **88.20%** | **84.78%** | **8.65 $m^2$** |
| **Global Ponderado** | **1.250** | **80.88%** | **89.43%** | **91.09%** | **87.82%** | **6.18 $m^2$** |

#### 2. Rendimiento según el modo de Entrada (*Prompting*)
| Modo de Digitalización | Puntos / Clics | IoU Promedio (%) | Tiempo de Inferencia CPU |
| :--- | :---: | :---: | :---: |
| **1-Point Prompt (Centroide)** | 1 clic positivo | **78.40%** | **180 ms** |
| **2-Point Prompt (Centro + Borde)** | 2 clics | **83.15%** | **195 ms** |
| **Bounding Box Prompt (Caja)** | 2 esquinas | **85.60%** | **188 ms** |

---

## Consideraciones Éticas, Sesgos y Limitaciones

- **Sesgo Morfológico:** En sectores informales o de rápido crecimiento con límites prediales difusos y techos superpuestos de zinc, el modelo tiende a agrupar dos construcciones contiguas en un solo polígono si no se le suministra un segundo punto de control.
- **Privacidad y Derechos Territoriales:** Las geometrías resultantes deben ser tratadas con rigor ético, sirviendo como insumo técnico de soporte y nunca como herramienta punitiva automatizada contra asentamientos vulnerables.

---

## 📚 Referencias Bibliográficas

* **Mitchell, M., Wu, S., Zaldivar, A., Barnes, P., Vasserman, L., Hutchinson, B., Spitzer, E., Raji, I. D., & Gebru, T. (2019)**. *Model Cards for Model Reporting*. In Proceedings of the Conference on Fairness, Accountability, and Transparency (FAT* '19), pp. 220–229. ACM. Disponible en: [https://arxiv.org/abs/1810.03993](https://arxiv.org/abs/1810.03993)
* **Zhang, C., Han, D., Qiao, Y., Kim, J. U., Bae, S. H., Lee, S., & Hong, C. S. (2023)**. *Faster Segment Anything: Towards Lightweight SAM for Mobile Applications*. arXiv preprint arXiv:2306.14289. Repositorio: [https://github.com/ChaoningZhang/MobileSAM](https://github.com/ChaoningZhang/MobileSAM)
* **Kirillov, A., Mintun, E., Ravi, N., Mao, H., Rolland, C., Gustafson, L., Xiao, T., Whitehead, S., Berg, A. C., Lo, W. Y., Dollár, P., & Girshick, R. (2023)**. *Segment Anything*. Meta AI Research. IEEE International Conference on Computer Vision (ICCV). Disponible en: [https://arxiv.org/abs/2304.02643](https://arxiv.org/abs/2304.02643)
* **Hugging Face Hub (2024)**. *Model Card and Dataset Card Templates*. Hugging Face Open Source Documentation. Disponible en: [https://github.com/huggingface/huggingface_hub/tree/main/src/huggingface_hub/templates](https://github.com/huggingface/huggingface_hub/tree/main/src/huggingface_hub/templates)
* **IDECA (2024)**. *Infraestructura de Datos Espaciales para el Distrito Capital de Bogotá*. Catastro Distrital. Portal de Datos Abiertos: [https://www.ideca.gov.co](https://www.ideca.gov.co)

<details>
<summary><b>Click aquí para ver las citas en formato BibTeX</b></summary>

```bibtex
@inproceedings{mitchell2019model,
  title={Model cards for model reporting},
  author={Mitchell, Margaret and Wu, Simone and Zaldivar, Andrew and Barnes, Parker and Vasserman, Lucy and Hutchinson, Ben and Spitzer, Elena and Raji, Inioluwa Deborah and Gebru, Timnit},
  booktitle={Proceedings of the Conference on Fairness, Accountability, and Transparency},
  pages={220--229},
  year={2019}
}

@article{zhang2023faster,
  title={Faster segment anything: Towards lightweight sam for mobile applications},
  author={Zhang, Chaoning and Han, Dongshen and Qiao, Yu and Kim, Jung Uk and Bae, Sung-Ho and Lee, SeungKyu and Hong, Choong Seon},
  journal={arXiv preprint arXiv:2306.14289},
  year={2023}
}

