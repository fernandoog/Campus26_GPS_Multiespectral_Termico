# Guía QGIS

Contexto del datum: [`DATUM.md`](DATUM.md). Soft: [QGIS](https://qgis.org/).

---

## 1. Comprobar undulación N

1. Carga `es_ign_EGM08_REDNAP.tif`.
2. Carga `GPS_Campus/_POINTSZ` (o `CAMPUS27.txt` como puntos).
3. *Identify* o *Sample raster values* sobre un GCP → **N ≈ 55.3 m**.
4. Comprueba `H + N ≈ 891 m` (suelo elipsoidal del dron).

CRS del proyecto / capa de puntos: **EPSG:25830** (ETRS89 / UTM 30N).

---

## 2. DEM desde Metashape (plan B, elipsoidal ~891 m)

Si el MDS/MDT está en altura elipsoidal:

1. `Raster → Raster calculator`
2. `"DEM@1" - 55.3` (o resta el geoide muestreado si quieres más rigor)
3. Resultado ≈ cotas ortométricas REDNAP (~836 m).

---

## 3. Productos Metashape con `30N REDNAP_ES`

Si exportaste ya en REDNAP, las cotas del DEM deben rondar **~836 m** en los GCP: solo verifica, sin restar N.

---

## 4. Productos OpenDroneMap

Con `gps-z-offset -55.3` y GCP ortométricos, el DSM debería alinearse ~**836 m** en P01…P09.

---

## Checklist

- [ ] Geoide + GCP cargados; N ≈ 55.3
- [ ] DEM coherente con ~836 m (ortométrico) o documentado como elipsoidal
