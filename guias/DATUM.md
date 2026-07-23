# Datum de alturas (común a todos los programas)

## Diagnóstico (Campus26)

| Origen | Equipo | Altura | Sistema |
| --- | --- | --- | --- |
| Imágenes | DJI **M3M** / **M3T** (RTK) | EXIF ≈ **934 m** | **Elipsoidal** WGS84 (`RtkAlt`) |
| GCP | **[Sokkia GCX3](https://www.topconpositioning.asia/jp/en/products/brand/sokkia/gcx3/)** | ≈ **836 m** en `GPS_Campus/CAMPUS27.txt` | **Ortométrica** EGM08-REDNAP + ETRS89/UTM 30N |
| Geoide | `es_ign_EGM08_REDNAP.tif` / [Agisoft EGM08-REDNAP](https://www.agisoft.com/downloads/geoids/) | **N ≈ 55.3 m** en Vegazana | ETRS89 → Alicante height (EPSG:5782) |

```text
H = h − N
h = H + N
```

```text
h_suelo ≈ 891.6 m   (elipsoidal)
H_GCP  ≈ 835.8 m   (ortométrica)
N      ≈ 55.3 m
```

Si no unificas el datum vertical → desfase **~55 m** en Z.

Planimetría GCP: **ETRS89 / UTM zone 30N** ([EPSG:25830](https://epsg.io/25830)). Curso Metashape: CRS **`30N REDNAP_ES`**.

## Ficheros útiles

| Archivo | Uso |
| --- | --- |
| `GPS_Campus/CAMPUS27.txt` | GCP ortométricos (~836 m) |
| `GPS_Campus/CAMPUS27_elipsoidal.txt` | GCP elipsoidales (~891 m) |
| `GPS_Campus/gcp_webodm_gcpi.txt` | Import GCP Interface WebODM |
| `es_ign_EGM08_REDNAP.tif` | Geoide IGN |

## Guías por programa

→ [`README.md`](README.md) en esta carpeta.
