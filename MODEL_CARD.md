---
language:
- en
- es
license: apache-2.0
tags:
- segmentation
- interactive-segmentation
- polygon-digitization
- remote-sensing
- geospatial
- computer-vision
- lightweight
datasets:
- SA-1B
metrics:
- mIoU
- latency
pipeline_tag: mask-generation
---

# Model Card: MobileSAM for Polygon Digitization in Geospatial Imagery

Ficha técnica estructurada en conformidad con la metodología de **Model Cards for Model Reporting** (*Mitchell et al., 2019*) y las especificaciones de metadatos de **Hugging Face Hub**.

---

## 1. Model Details (Detalles del Modelo)

### 1.1 Información Básica
* **Nombre del modelo:** MobileSAM (*Faster Segment Anything: Towards Lightweight SAM for Mobile Applications*).
* **Versión del checkpoint:** 1.0 (`mobile_sam.pt`).
* **Peso y tamaño del archivo:** **39.0 MB** (~9.66 millones de parámetros en total).
* **Tipo de arquitectura:** Segmentación interactiva basada en Prompts (puntos, cajas delimitadoras o máscaras) mediante destilación de conocimiento acoplada a un codificador *TinyViT*.
* **Autores originales:** Chaoning Zhang, Dongshen Han, Yu Qiao, Jung Uk Kim, Sung-Ho Bae, SeungKyu Lee, Choong Seon Hong (Kyung Hee University, 2023).
* **Publicación científica:** arXiv:2306.14289 (Aceptado en conferencias internacionales de visión por computador).
* **Licencia de uso:** Apache License 2.0 (Permite uso académico, desarrollo e investigación abierta).
* **Repositorio oficial del código fuente:** [https://github.com/ChaoningZhang/MobileSAM](https://github.com/ChaoningZhang/MobileSAM)
* **Autora de la ficha técnica:** [Nombres y Apellidos de la Estudiante] ([tu_correo@universidad.edu.co]).

---

## 2. Intended Use (Uso Previsto)

### 2.1 Usos Directos y Previstos (Direct Intended Uses)
* **Digitalización interactiva de geometrías:** Extracción rápida de contornos de polígonos en imágenes de satélite o drones para proyectos de catastro, planificación urbana y medio ambiente.
* **Segmentación asistida por clics (Prompting):** Permite al analista hacer un clic en el centro de un tejado, piscina, lago o lote de cultivo para que el modelo extraiga la máscara binaria exacta en milisegundos.
* **Vectorización automática:** Conversión directa de las máscaras binarias generadas a capas vectoriales espaciales (polígonos GeoJSON, Shapefile o Geopackage) mediante algoritmos de contorno (`shapely`, `OpenCV` o `rasterio`).
* **Entornos con restricciones computacionales:** Despliegue en computadores portátiles convencionales o servidores web livianos sin requerir tarjetas gráficas de gama alta (GPUs).

### 2.2 Usos Fuera de Alcance y No Recomendados (Out-of-Scope Uses)
* **Clasificación semántica multiclase automática:** MobileSAM no predice etiquetas de texto (no indica el tipo de cobertura o material del techo); su función es delimitar la geometría.
* **Imágenes hiperespectrales o radar (SAR) en crudo:** El modelo fue entrenado en el espectro visible (RGB de tres canales); las imágenes satelitales multiespectrales deben proyectarse primero a composiciones RGB.
* **Vigilancia o rastreo biométrico de personas:** El modelo no está validado ni destinado para aplicaciones de invasión a la privacidad o seguimiento individual.

---

## 3. Factors, Metrics, and Evaluation Data (Factores, Métricas y Datos)

### 3.1 Factores de Rendimiento
El desempeño del modelo durante la digitalización depende de:
* **Resolución espacial (GSD):** Óptimo en resoluciones submétricas y de alta resolución (imágenes aéreas, ortofotos de dron, WorldView, PlanetScope), donde los bordes físicos sean distinguibles visualmente.
* **Contraste espectral:** Objetos con bajo contraste respecto a su fondo (por ejemplo, tejados grises sobre asfalto gris o zonas de sombra profunda) pueden requerir 2 o 3 clics interactivos (puntos positivos y negativos) para delimitar el polígono correctamente.
* **Oclusiones y copas de árboles:** En áreas con vegetación sobrepuesta en edificaciones, el polígono extraído representará solo la porción visible de la cubierta.

### 3.2 Métricas de Rendimiento (Metrics)
* **Mean Intersection over Union (mIoU):** Mide la exactitud geométrica del solapamiento entre el polígono predicho y la realidad del terreno (~75% mIoU frente al ground truth).
* **Latencia de inferencia:**
  * En GPU (NVIDIA RTX / T4): **~10 a 12 milisegundos por prompt**.
  * En CPU de computador portátil (Intel i5/i7 o AMD Ryzen): **~150 a 300 milisegundos**, permitiendo digitalización fluida en tiempo real.
* **Consumo de memoria RAM:** Menor a 1.2 GB durante la ejecución.

### 3.3 Datos de Entrenamiento (Training Data)
* MobileSAM fue entrenado por **destilación de conocimiento desacoplada (decoupled distillation)** utilizando como maestro (*teacher*) el modelo SAM original entrenado sobre el gigantesco conjunto de datos **SA-1B** (*Segment Anything 1-Billion* de Meta AI), que comprende más de 11 millones de imágenes de alta resolución y más de 1.100 millones de máscaras de segmentación de alta calidad.

---

## 4. Technical Specifications & Methodology (Especificaciones Técnicas)

### 4.1 Arquitectura del Sistema
1. **Image Encoder (Codificador de Imagen):** Utiliza un *TinyViT* de 5 millones de parámetros preentrenado que extrae las características densas de la imagen satelital.
2. **Prompt Encoder:** Módulo ultraligero que codifica las coordenadas (X, Y) de los clics dados por el usuario en el lienzo geográfico.
3. **Mask Decoder:** Decodificador liviano de dos capas de atención cruzada que proyecta las características del TinyViT junto con el clic para generar la máscara binaria final.
4. **Vectorización a Polígono:** La máscara binaria en formato ráster se simplifica vectorialmente usando algoritmos como el de Ramer-Douglas-Peucker (`cv2.approxPolyDP` o `shapely`) para producir un polígono georreferenciado limpio con pocos vértices.

---

## 5. Environmental Impact (Impacto Ambiental)

* **Eficiencia de Recursos:** Al pesar únicamente 39 MB, el modelo elimina la necesidad de clústeres de servidores dedicados con GPUs de alto consumo (como NVIDIA A100/H100), permitiendo su funcionamiento en hardware de oficina convencional.
* **Ahorro Energético:** La fase de destilación requirió menos del 1% del costo computacional y huella de carbono del entrenamiento del modelo base original de Meta, promoviendo una inteligencia artificial más sostenible (*Green AI*).

---

## 6. Ethical Considerations & Limitations (Consideraciones Éticas)

* **Supervisión Humana Obligatoria (Human-in-the-Loop):** Al ser un modelo interactivo, la responsabilidad final de la delimitación predial o catastral recae en el criterio del operador humano, evitando decisiones automatizadas sin verificación técnica.
* **Sesgo de Apariencia Urbana:** El modelo responde mejor a geometrías regulares y bien definidas (patrones urbanos occidentales) que a asentamientos informales con geometrías orgánicas o materiales heterogéneos.

---

## 7. Citations & References (Referencias Bibliográficas)

```bibtex
@article{zhang2023faster,
  title={Faster Segment Anything: Towards Lightweight SAM for Mobile Applications},
  author={Zhang, Chaoning and Han, Dongshen and Qiao, Yu and Kim, Jung Uk and Bae, Sung-Ho and Lee, SeungKyu and Hong, Choong Seon},
  journal={arXiv preprint arXiv:2306.14289},
  year={2023}
}

@inproceedings{mitchell2019model,
  title={Model Cards for Model Reporting},
  author={Mitchell, Margaret and Wu, Simone and Zaldivar, Andrew and Barnes, Parker and Vasserman, Lucy and Hutchinson, Ben and Spitzer, Elena and Raji, Inioluwa Deborah and Gebru, Timnit},
  booktitle={Proceedings of the Conference on Fairness, Accountability, and Transparency (FAT*)},
  pages={220--229},
  year={2019}
}

@article{kirillov2023segany,
  title={Segment Anything},
  author={Kirillov, Alexander and Mintun, Eric and Ravi, Nikhila and Mao, Hanzi and Rolland, Chloe and Gustafson, Laura and Xiao, Tete and Whitehead, Spencer and Berg, Alexander C and Lo, Wan-Yen and Dollar, Piotr and Girshick, Ross},
  journal={arXiv:2304.02643},
  year={2023}
}
