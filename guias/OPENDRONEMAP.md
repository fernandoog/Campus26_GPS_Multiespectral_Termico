# Guía OpenDroneMap / WebODM

Contexto del datum: [`DATUM.md`](DATUM.md).  
Docs: [GCP](https://docs.opendronemap.org/gcp/) · [`gps-z-offset`](https://docs.opendronemap.org/arguments/gps-z-offset/) · [Task options](https://docs.webodm.net/references/task-options/).

Las fotos DJI llevan Z **elipsoidal**; los GCP GCX3 son **ortométricos** (~836 m). Hay que unificar con offset y un fichero GCP que tu WebODM entienda.

---

## 1. Offset de altura (fotos ya importadas)

1. Tarea → **Edit** opciones.
2. **`gps-z-offset` = `-55.3`**
3. Los GCP **no** se modifican (siguen ~836 m).
4. **Restart / Reprocess** (no basta guardar si ya procesó).

`Z_usada = Z_EXIF + offset`

---

## 2. Importar GCP (GCP Interface / POSM GCPi)

Tu WebODM **no** acepta bien `EPSG:25830` en la cabecera. Usa el fichero listo:

**`GPS_Campus/gcp_webodm_gcpi.txt`**

```text
+proj=utm +zone=30 +ellps=WGS84 +datum=WGS84 +units=m +no_defs
P01 290219.012 4721221.998 835.766
P02 ...
```

Formato: **nombre Este Norte Z** (sin píxeles). Luego marcas cada diana en 2–3 fotos.

### Si no carga la proyección

Prueba **`GPS_Campus/gcp_webodm_utm.txt`** (cabecera `WGS84 UTM 30N`).

### Si usas GCP Editor Pro

**`GPS_Campus/gcp_webodm_editor.csv`** y en la caja PROJ:

```text
+proj=utm +zone=30 +ellps=WGS84 +datum=WGS84 +units=m +no_defs
```

### Errores habituales

| Mensaje | Qué hacer |
| --- | --- |
| `Unknown projection undefined` | Falta cabecera / proyección vacía → usa `gcp_webodm_gcpi.txt` |
| `Unknown projection EPSG:25830` | Esta versión no lee EPSG → usa **proj.4** o `WGS84 UTM 30N` |
| No reconoce el fichero | No uses listas con `0 0 _MARCAR_.JPG` para importar en la interfaz; eso es formato “ya etiquetado” |

Docs del formato “solo XYZ” para la interfaz: [Ground Control Points](https://docs.opendronemap.org/gcp/).

---

## 3. Procesar

1. GCP importados y etiquetados en fotos.
2. `gps-z-offset = -55.3`
3. Process / Restart.
4. Comprobar DSM en QGIS → [`QGIS.md`](QGIS.md) (cota ~836 m en un GCP).

---

## Checklist

- [ ] `gcp_webodm_gcpi.txt` importado; puntos en el mapa (León)
- [ ] Cada P01…P09 marcado en ≥2 fotos
- [ ] `gps-z-offset = -55.3`
- [ ] Restart / Process
- [ ] DSM ~836 m en GCP
