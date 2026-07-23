# Campus26 — GPS, multiespectral y térmico

Datos de campo del **[VI Curso de Fotogrametría con drones](https://cursosdeverano.unileon.es/vi-curso-de-fotogrametria-con-drones/)** (Universidad de León), adquiridos el **22 de julio de 2026** en el Campus de Vegazana (León) durante las prácticas de vuelo y GPS-RTK.

Curso organizado por la Escuela Superior y Técnica de Ingenieros de Minas (ULE), dirigido por Enoc Sanz Ablanedo y Javier Fernández Lozano. Programa oficial: [DIP_CV018_ESPEC_2026](https://cursosdeverano.unileon.es/wp-content/uploads/2026/05/DIP_CV018_ESPEC_2026_v02.pdf).

## Contenido

| Carpeta / archivo | Descripción |
| --- | --- |
| `GPS_Campus/` | GCP medidos con [Sokkia GCX3](https://www.topconpositioning.asia/jp/en/products/brand/sokkia/gcx3/) (ortométricas EGM08-REDNAP) y shapefile asociado |
| `Multiespectral/` | En GitHub: solo ficheros **PPK** del vuelo M3M (`.obs`, `.nav`, `.MRK`, `.bin`). Las imágenes RGB/MS (~6 GB) se guardan en local |
| `Térmico/` | Vuelo M3T en local (~2,5 GB); no versionado en GitHub por tamaño |
| `reference_export.txt` | Posiciones de referencia de las imágenes multiespectrales (WGS84) |
| `es_ign_EGM08_REDNAP.tif` | Modelo de geoide IGN (EGM08-REDNAP) para conversión de altitudes |
| `GUIA_ALTURAS.md` | Guía para unificar alturas elipsoidales (dron RTK) y ortométricas (GCP) en Metashape y OpenDroneMap |
| `GUIA_CODED_TARGETS.md` | Guía de coded targets y scale bars en Metashape (detección automática de dianas) |

> **Nota:** el dataset completo de imágenes supera **8 GB**. GitHub rechaza ese push; este repositorio publica guías, GCP, geoide, PPK y metadatos. Las carpetas de fotos (`Multiespectral/*.JPG|*.TIF`, `Térmico/`) quedan en local (ver `.gitignore`).

## GPS / puntos de control

Medidos con receptor GNSS **[Sokkia GCX3](https://www.topconpositioning.asia/jp/en/products/brand/sokkia/gcx3/)** (RTK de red / rover) sobre las marcas del suelo (cuadrado partido en el vértice). Cotas expresadas como **altura ortométrica** respecto al geoide **EGM08-REDNAP** (`es_ign_EGM08_REDNAP.tif`).

- `GPS_Campus/CAMPUS27.txt` — puntos `P01`…`P09` en formato `ID Este Norte Cota`.
- Sistema de referencia: **ETRS89 / UTM huso 30N** ([EPSG:25830](https://epsg.io/25830)), según `GPS_Campus/_POINTSZ.prj`.
- Shapefile `_POINTSZ.*` con la geometría de los puntos.

## Multiespectral (`Multiespectral/DJI_202607221215_007_Campus26m3m/`)

Misión DJI del 22/07/2026 (~12:15). Por cada toma:

| Sufijo | Sensor |
| --- | --- |
| `_D.JPG` | RGB (cámara visible) |
| `_MS_G.TIF` | Verde |
| `_MS_R.TIF` | Rojo |
| `_MS_RE.TIF` | Red Edge |
| `_MS_NIR.TIF` | Infrarrojo cercano |

También incluye ficheros **PPK** del vuelo (`.obs`, `.nav`, `.MRK`, `.bin`) para postproceso GNSS de precisión.

`reference_export.txt` lista las coordenadas WGS84 (EPSG:4326) y orientaciones asociadas a cada imagen (exportación tipo Metashape/Agisoft).

## Térmico (`Térmico/DJI_202607221155_007_Campus26m3t/`)

Misión del mismo día (~11:55). Imágenes JPG emparejadas:

- `*_T.JPG` — térmico  
- `*_V.JPG` — visible  

## Uso previsto

Material de práctica para el flujo fotogramétrico del curso: importación de imágenes, sistemas de referencia, ajuste con GCP/PPK, densificación, ortomosaicos / MDS y exportación a SIG.

## Software

| Herramienta | Rol |
| --- | --- |
| [Agisoft Metashape](https://www.agisoft.com/) (versión demo) | Procesamiento fotogramétrico del curso: alineación, nubes de puntos, ortomosaicos y MDS. Markers / coded targets requieren **Metashape Pro** (ver `GUIA_CODED_TARGETS.md`) |
| [QGIS](https://qgis.org/) | Visualización, análisis y exportación SIG de los productos generados |
| [OpenDroneMap](https://www.opendronemap.org/) | Prueba personal en paralelo (flujo open source alternativo a Metashape) |

## Licencia / uso

Datos generados en el marco del curso de verano de la Universidad de León. Consulte a la dirección del curso antes de redistribuirlos fuera del ámbito docente.
