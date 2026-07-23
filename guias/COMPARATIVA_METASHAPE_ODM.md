# Comparativa: OpenDroneMap / WebODM vs Agisoft Metashape

Contexto Campus26: [`DATUM.md`](DATUM.md) · flujos: [`METASHAPE.md`](METASHAPE.md) · [`OPENDRONEMAP.md`](OPENDRONEMAP.md).

| | **Metashape** (curso) | **OpenDroneMap / WebODM** (prueba propia) |
| --- | --- | --- |
| **Tipo** | Comercial (demo / Pro) | Open source + WebODM de pago/cloud |
| **Uso en este repo** | Flujo oficial del [VI Curso](https://cursosdeverano.unileon.es/vi-curso-de-fotogrametria-con-drones/) | Alternativa personal en paralelo |
| **Licencia / coste** | Demo limitada; Pro de pago (markers, geoide, sin marca de agua) | Motor ODM gratis; WebODM Lightning / nodos de pago opcionales |
| **Interfaz** | Escritorio denso, control fino de cada paso | Web: tareas, presets, menos clics |
| **Flujo** | Manual por etapas (Align → Optimize → Dense → DEM/Ortho) | Pipeline automático de punta a punta |
| **GCP** | Markers en panel Reference; semi-automático tras 2–3 fotos | GCP Interface; hay que etiquetar píxeles; formato de fichero exigente |
| **CRS / geoide España** | CRS compuesto **`30N REDNAP_ES`** + [EGM08-REDNAP](https://www.agisoft.com/downloads/geoids/) | Sin geoide IGN nativo; **`gps-z-offset -55.3`** o convertir Z a mano |
| **RTK del dron** | EXIF + accuracies (0.03/0.04 m); se separa CRS cámara vs marker | EXIF + offset Z; `--force-gps` si quieres RTK + GCP |
| **Multiespectral (M3M)** | Flujos multi-cámara / bandas; más maduro en Pro | Preset multispectral; más limitado / experimental según versión |
| **Térmico (M3T)** | Se procesa (p. ej. `_V` o `_T`); radiometría térmica aparte | Similar: suele usarse la banda visible o térmica según objetivo |
| **Coded targets** | Sí en Pro (`Detect Markers`) | No equivalente Agisoft; cruces a mano |
| **Calidad / control** | Informes de error por marker/cámara; Optimize fino | Informe de calidad; menos control de parámetros de cámara |
| **Productos** | Nube densa, mesh, DEM, orto, texturas; export flexible | Ortho, DSM/DTM, nube, 3D; export estándar |
| **Curva de aprendizaje** | Alta (CRS, accuracies, Optimize) | Media (opciones y GCP); proyección a veces confusa |
| **Rendimiento** | Muy bueno con GPU; control de calidad vs tiempo | Depende del nodo; presets Fast / High Resolution |
| **Encaje SIG (QGIS)** | Export a `30N REDNAP_ES` → ortométricas directas | Con offset bien puesto → cotas ~836 m; CRS tipo UTM 30N / WGS84 |

---

## Datum vertical (punto crítico en Campus26)

| | Metashape | ODM / WebODM |
| --- | --- | --- |
| **Enfoque recomendado** | Cámaras **WGS 84** + markers **`30N REDNAP_ES`** + `CAMPUS27.txt` | `gps-z-offset = -55.3` + GCP ortométricos |
| **Plan B** | Todo elipsoidal (`CAMPUS27_elipsoidal.txt`) | Convertir Z en `geo.txt` / GCP |
| **Riesgo típico** | *Vertical datum out of range* si haces **Convert** de cámaras a REDNAP | `Unknown projection` si la cabecera GCP no es proj.4 / `WGS84 UTM 30N` |

---

## Cuándo usar cada uno (este curso)

**Metashape** — si quieres seguir el curso, CRS español REDNAP, markers/accuracies, multiespectral serio y entregables “de topografía”.

**OpenDroneMap** — si quieres automatizar, comparar resultados open source, o no tienes Pro; asume más trabajo manual en proyección y en el desfase elipsoidal/ortométrico.

**Los dos** — mismo vuelo + mismos GCP: comparar orto/DSM en QGIS ([`QGIS.md`](QGIS.md)) es el mejor aprendizaje.

---

## Resumen rápido

| Pregunta | Respuesta corta |
| --- | --- |
| ¿Cuál usa el curso? | **Metashape** |
| ¿Cuál es gratis de verdad? | **ODM** (el motor); WebODM cloud puede ser de pago |
| ¿Mejor con geoide REDNAP? | **Metashape Pro** + `30N REDNAP_ES` |
| ¿Más rápido de lanzar? | **WebODM** (pipeline) |
| ¿Más control del ajuste? | **Metashape** (Optimize + accuracies) |
| ¿Mismo problema de ~55 m? | Sí en ambos si no unificas Z |

## Referencias

- [Agisoft Metashape](https://www.agisoft.com/)
- [OpenDroneMap](https://www.opendronemap.org/) · [WebODM](https://www.opendronemap.org/webodm/)
- [QGIS](https://qgis.org/)
