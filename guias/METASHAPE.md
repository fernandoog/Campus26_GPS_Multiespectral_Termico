# Guía Metashape — alturas, CRS y Optimize

Contexto del datum: [`DATUM.md`](DATUM.md).  
Coded targets: [`METASHAPE_CODED_TARGETS.md`](METASHAPE_CODED_TARGETS.md).  
Docs: [Height above geoid](https://agisoft.freshdesk.com/support/solutions/articles/31000148332-how-to-use-height-above-geoid-for-the-coordinate-system) · [Geoides Agisoft](https://www.agisoft.com/downloads/geoids/).

> Geoide / CRS compuestos: **Metashape Professional**. Si *Vertical datum out of range* → [Plan B](#plan-b-sin-geoide).

---

## 1. Importar y alinear

1. `Workflow → Add Photos`
2. `Workflow → Align Photos…`
3. Panel **Reference**: cámaras con Z ≈ 934 m. **No las edites.**

---

## 2. CRS del curso: `30N REDNAP_ES`

1. Reference → engranaje (**Settings**) → **Coordinate System** → `…`
2. Busca **`30N REDNAP_ES`** (UTM 30N + REDNAP).

Si no existe, créalo:

1. **ETRS89 / UTM zone 30N** (EPSG:25830) → **Edit**
2. Nombre: `30N REDNAP_ES`
3. Vertical CS → Custom → geoide **EGM08-REDNAP** ([descarga](https://www.agisoft.com/downloads/geoids/) o `es_ign_EGM08_REDNAP.tif`)

---

## 3. Settings: separar cámaras y markers

### Cámaras

| Campo | Valor |
| --- | --- |
| Coordinate system | **WGS 84** (EPSG:4326) |
| Camera accuracy (m) | **0.03** (M3M) / **0.04** (M3T) |
| Camera accuracy (deg) | **2** (o default ~10) |

**No** uses `30N REDNAP_ES` en cámaras. **No** pulses **Convert**.

### Markers (GCP)

| Campo | Valor |
| --- | --- |
| Coordinate system | **`30N REDNAP_ES`** |
| Marker accuracy (m) | **0.02** |
| Tie point accuracy (pix) | **1** |

Importa `GPS_Campus/CAMPUS27.txt` (~836 m). Marca el **vértice de la cruz** en 2–3 fotos por punto.

---

## 4. Camera accuracy (cálculo)

Del EXIF DJI (`RtkStdLon/Lat/Hgt`):

| Sensor | σ Lon | σ Lat | σ Hgt | RSS 3D → Settings |
| --- | ---: | ---: | ---: | ---: |
| M3M | 0.011 | 0.012 | 0.026 | **0.03 m** |
| M3T | 0.015 | 0.016 | 0.033 | **0.04 m** |

---

## 5. Optimize Cameras

1. Desmarca **cámaras**; marca **GCP**.
2. `Tools → Optimize Cameras…` → OK.
3. Error de markers: cm/dm, no ~55 m.
4. Exporta en `30N REDNAP_ES` → cotas ~836 m.

---

## Vertical datum out of range

Causa típica: **Convert** de cámaras a REDNAP, o geoide mal instalado.

1. Cámaras = **WGS 84** (sin Convert).
2. Markers = `30N REDNAP_ES` + `CAMPUS27.txt`.
3. Si sigue → [Plan B](#plan-b-sin-geoide).

---

## Plan B (sin geoide)

1. CRS = ETRS89 / UTM 30N **sin** geoid (cámaras y markers).
2. Markers: `GPS_Campus/CAMPUS27_elipsoidal.txt` (~891 m).
3. Cámaras desmarcadas → Optimize.
4. Modelo ~891 m. En QGIS resta N → [`QGIS.md`](QGIS.md).

---

## Checklist

- [ ] Align
- [ ] Cámaras WGS 84 · Markers `30N REDNAP_ES`
- [ ] Accuracy cámara 0.03/0.04 · marker 0.02
- [ ] GCP `CAMPUS27.txt` · sin Convert
- [ ] Optimize · error markers bajo
