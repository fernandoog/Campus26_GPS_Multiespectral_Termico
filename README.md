# Campus26 — GPS, multiespectral y térmico

Datos de campo del **[VI Curso de Fotogrametría con drones](https://cursosdeverano.unileon.es/vi-curso-de-fotogrametria-con-drones/)** (Universidad de León), adquiridos el **22 de julio de 2026** en el Campus de Vegazana (León) durante las prácticas de vuelo y GPS-RTK.

Curso organizado por la Escuela Superior y Técnica de Ingenieros de Minas (ULE), dirigido por Enoc Sanz Ablanedo y Javier Fernández Lozano. Programa oficial: [DIP_CV018_ESPEC_2026](https://cursosdeverano.unileon.es/wp-content/uploads/2026/05/DIP_CV018_ESPEC_2026_v02.pdf).

## Contenido

| Carpeta / archivo | Descripción |
| --- | --- |
| `GPS_Campus/` | GCP GCX3, shapefile y ficheros para WebODM (`gcp_webodm_gcpi.txt`, etc.) |
| `Multiespectral/` | En GitHub: solo ficheros **PPK** del vuelo M3M. Imágenes RGB/MS en local |
| `Térmico/` | Vuelo M3T en local; no versionado en GitHub por tamaño |
| `reference_export.txt` | Posiciones de referencia multiespectrales (WGS84) |
| `es_ign_EGM08_REDNAP.tif` | Geoide IGN EGM08-REDNAP |
| `guias/` | Guías **por programa**: Metashape, OpenDroneMap, QGIS ([índice](guias/README.md)) |

> **Nota:** el dataset de imágenes supera **8 GB**. En GitHub solo guías, GCP, geoide, PPK y metadatos (ver `.gitignore`).

## GPS / puntos de control

Medidos con **[Sokkia GCX3](https://www.topconpositioning.asia/jp/en/products/brand/sokkia/gcx3/)** sobre marcas del suelo. Cotas **ortométricas** EGM08-REDNAP.

- `GPS_Campus/CAMPUS27.txt` — H ortométrica (~836 m)
- `GPS_Campus/CAMPUS27_elipsoidal.txt` — h = H+55.3 (plan B Metashape)
- `GPS_Campus/gcp_webodm_gcpi.txt` — import GCP Interface WebODM
- CRS planimétrico: **ETRS89 / UTM 30N** ([EPSG:25830](https://epsg.io/25830))

Detalle del datum: [`guias/DATUM.md`](guias/DATUM.md).

## Multiespectral (`Multiespectral/DJI_202607221215_007_Campus26m3m/`)

Misión DJI del 22/07/2026 (~12:15). Por cada toma:

| Sufijo | Sensor |
| --- | --- |
| `_D.JPG` | RGB |
| `_MS_G.TIF` | Verde |
| `_MS_R.TIF` | Rojo |
| `_MS_RE.TIF` | Red Edge |
| `_MS_NIR.TIF` | NIR |

PPK: `.obs`, `.nav`, `.MRK`, `.bin`. Metadatos: `reference_export.txt`.

## Térmico (`Térmico/DJI_202607221155_007_Campus26m3t/`)

Pares `*_T.JPG` (térmico) y `*_V.JPG` (visible).

## Software y guías

| Herramienta | Guía |
| --- | --- |
| [Agisoft Metashape](https://www.agisoft.com/) | [`guias/METASHAPE.md`](guias/METASHAPE.md) · [`guias/METASHAPE_GPU_CUDA.md`](guias/METASHAPE_GPU_CUDA.md) · [`guias/METASHAPE_CODED_TARGETS.md`](guias/METASHAPE_CODED_TARGETS.md) |
| [OpenDroneMap](https://www.opendronemap.org/) / WebODM | [`guias/OPENDRONEMAP.md`](guias/OPENDRONEMAP.md) |
| [QGIS](https://qgis.org/) | [`guias/QGIS.md`](guias/QGIS.md) |
| Comparativa Metashape ↔ ODM | [`guias/COMPARATIVA_METASHAPE_ODM.md`](guias/COMPARATIVA_METASHAPE_ODM.md) |

## Licencia / uso

Datos del curso de verano de la Universidad de León. Consulte a la dirección del curso antes de redistribuir fuera del ámbito docente.
