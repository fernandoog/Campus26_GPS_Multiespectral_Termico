# Guía: alturas elipsoidales del dron vs ortométricas de los GCP

## Diagnóstico (este dataset)

| Origen | Equipo | Altura que aporta | Sistema |
| --- | --- | --- | --- |
| Imágenes | **DJI Mavic 3 Multispectral (M3M)** y **Mavic 3 Thermal (M3T)** | `AbsoluteAltitude` / EXIF GPS ≈ **934 m** | **Elipsoidal** WGS84 (`AltitudeType = RtkAlt`, RTK vía NTRIP) |
| Marcas en suelo | **[Sokkia GCX3](https://www.topconpositioning.asia/jp/en/products/brand/sokkia/gcx3/)** (GNSS RTK) | Cotas ≈ **836 m** en `GPS_Campus/CAMPUS27.txt` | **Ortométrica** sobre geoide **EGM08-REDNAP**, proyectadas en **ETRS89 / UTM 30N** ([EPSG:25830](https://epsg.io/25830)) |
| Geoide | `es_ign_EGM08_REDNAP.tif` | Undulación **N ≈ 55.3 m** en Campus de Vegazana (muestreo del TIFF) | Modelo IGN EGM08-REDNAP (rejilla en ETRS89 geográficas) |

Relación fundamental:

```text
H = h − N
```

- **h** = altura elipsoidal (lo que escribe el RTK del dron)
- **H** = altura ortométrica (lo que mide la antena respecto al geoide / “nivel del mar”)
- **N** = undulación del geoide (valor del TIFF en esa lat/lon)

Comprobación rápida con una foto M3M (`RelativeAltitude ≈ 42.4 m`):

```text
h_suelo ≈ 933.99 − 42.41 ≈ 891.6 m   (elipsoidal)
H_GCP  ≈ 835.8 m                     (ortométrica)
N      ≈ 891.6 − 835.8 ≈ 55.8 m      (≈ 55.3 m del geoide; ~0.5 m de residual por antena / takeoff / resolución)
```

Si mezclas ambos sin convertir, el modelo queda **desplazado ~55 m en Z** (o el ajuste pelea con errores enormes en altura).

Las marcas del suelo (cuadrado partido en el vértice) son los **GCP** medidos con la **Sokkia GCX3**; hay que marcarlas en las fotos y cargar las coordenadas de `CAMPUS27.txt` en el CRS correcto.

El GCX3 es un receptor GNSS dual-frecuencia compacto (SOKKIA / Topcon), pensado como rover RTK de red: entrega precisión centimétrica en planimetría y altura. En este trabajo las cotas se redujeron a **ortométricas REDNAP** (no se dejaron en elipsoidal WGS84), de ahí el desfase vertical respecto al RTK del dron.

---

## Qué hay que unificar

Elige **un solo** sistema vertical para cámaras + GCP + productos (MDS/MDT):

| Estrategia | Idea | Cuándo |
| --- | --- | --- |
| **A. Todo ortométrico (recomendada en España / SIG)** | Convertir las alturas del dron con el geoide; GCP se quedan como están | Exportar a QGIS, comparar con cartografía IGN, cotas “de mapa” |
| **B. Todo elipsoidal** | Sumar N a los GCP: `h = H + N` | Trabajar solo en GNSS / comparar con RTK del dron |
| **C. Offset constante** | Restar ~55.3 m a las Z del EXIF | Aproximación válida en un área pequeña (este campus sí lo es) |

A continuación: **Metashape** (estrategia A con el TIFF) y **OpenDroneMap** (A/C).

---

## Metashape (versión demo / Professional)

Documentación oficial: [How to use height above geoid](https://agisoft.freshdesk.com/support/solutions/articles/31000148332-how-to-use-height-above-geoid-for-the-coordinate-system).

> La creación de CRS compuestos con geoide personalizado está pensada para **Metashape Professional**. En la demo puedes seguir el flujo para aprender; si alguna opción de Vertical CS / Import Geoid no está disponible, usa la conversión previa de coordenadas (script/QGIS) o el offset constante.

### 1. Importar imágenes y alinear

1. `Workflow → Add Photos` (M3M: bandas coherentes; M3T: normalmente `_V` o `_T` según el producto).
2. Comprobar en el panel **Reference** que las cámaras tienen lat/lon/alt del EXIF (~934 m).
3. `Align Photos` con precisión alta si la demo lo permite.

### 2. Crear CRS compuesto: ETRS89/UTM30N + EGM08-REDNAP

1. Panel **Reference** → icono de engranaje (**Settings**).
2. En **Coordinate System** pulsa `…` → busca **ETRS89 / UTM zone 30N** (EPSG:25830) → **Edit**.
3. Cambia el nombre, p. ej. `ETRS89 / UTM 30N + EGM08-REDNAP`.
4. En **Vertical CS** elige **Custom…** y nombra p. ej. `EGM08-REDNAP height`.
5. En **Datum** → **Add…** y selecciona el archivo del repo:

   `es_ign_EGM08_REDNAP.tif`

6. Confirma el diálogo **Import Geoid** (el TIFF ya viene en ETRS89 geográficas; Metashape lee la rejilla de undulación N).
7. Acepta hasta volver a Settings. El CRS compuesto quedará en **User-defined coordinate systems**.

### 3. Asignar CRS distinto a cámaras y a marcadores

Objetivo: decirle a Metashape “las fotos están en elipsoidal; los GCP en ortométrica + UTM”.

1. En **Reference → Settings**:
   - **Camera reference** / sistema de las fotos: **WGS 84** (EPSG:4326) o el que muestre el EXIF — altura **elipsoidal**.
   - **Marker reference** (GCP): el CRS compuesto `ETRS89 / UTM 30N + EGM08-REDNAP`.
2. Importa los GCP desde `GPS_Campus/CAMPUS27.txt` (Este, Norte, H ortométrica) o el shapefile `_POINTSZ`.
3. En cada foto, coloca el marcador en el **vértice del cuadrado partido** (centro de la cruz).
4. Marca al menos 3–4 GCP bien repartidos; el resto pueden servir de check.

### 4. Optimizar y comprobar

1. `Optimize Cameras` (con marcadores activos).
2. En el panel Reference mira **Error (m)** de los markers: en Z debería quedar en el orden de centímetros/decímetros, no ~56 m.
3. Si el error Z de las **cámaras** sigue siendo ~56 m pero el de los **markers** es bajo, el modelo ya está anclado a los GCP ortométricos (el residual de cámaras es el desajuste de datum que Metashape absorbe al transformar).

### 5. Exportar productos

Exporta DEM / ortomosaico / nube en el CRS compuesto (o EPSG:25830 + cotas ortométricas). Así en **QGIS** las cotas coinciden con la antena y con cartografía en REDNAP.

### Atajo si no puedes cargar el geoide (demo)

1. En QGIS, usa `es_ign_EGM08_REDNAP.tif` (muestreo puntual) o calcula `h = H + N` para cada GCP.
2. Carga en Metashape GCP ya en **altura elipsoidal**.
3. Deja cámaras y markers en elipsoidal (WGS84 / UTM + elipsoide).
4. O al revés: resta N a las Z de las cámaras (o usa un offset fijo de **−55.3 m**) y deja los GCP ortométricos.

---

## OpenDroneMap / WebODM

ODM **no** aplica de forma nativa un GeoTIFF de geoide español a las fotos. Hay que igualar Z antes o con un offset. Ver [`--gps-z-offset`](https://docs.opendronemap.org/arguments/gps-z-offset/).

### Opción 1 (rápida): offset en las fotos, GCP sin tocar

Para este campus, **N ≈ 55.3 m** (geoide), así que las Z del EXIF (elipsoidales) deben bajar esa cantidad para parecer ortométricas:

```bash
docker run -ti --rm -v /ruta/al/proyecto:/datasets opendronemap/odm \
  --project-path /datasets proyecto \
  --gps-z-offset -55.3 \
  --gcp /datasets/proyecto/gcp_list.txt
```

En WebODM: opciones del procesado → `gps-z-offset` = `-55.3`.

- El offset **solo** afecta al GPS EXIF de las imágenes, **no** a los GCP.
- En un área pequeña el error residual de usar N constante es despreciable (centímetros).

### Opción 2 (más limpia): archivo `geo.txt` con Z ya convertidas

Formato ODM:

```text
EPSG:4326
DJI_...._D.JPG  lon  lat  H_ortometrica  yaw  pitch  roll
...
```

Con `H = h_EXIF − N` (N del geoide ≈ 55.3). Pásalo con `--geo /ruta/geo.txt`.

### Opción 3: GCP en elipsoidal

Convierte cada punto de `CAMPUS27.txt`:

```text
h = H + N
```

y escribe el `gcp_list.txt` con esas `h`. Las fotos se dejan como están (elipsoidales).

Cabecera típica del GCP list (UTM ortométrico, opción 1):

```text
EPSG:25830
290219.012 4721221.998 835.766  pix_x  pix_y  nombre_imagen.jpg  P01
...
```

(Edita `pix_x` / `pix_y` con el GCP Interface de WebODM o midiendo en las fotos.)

### Notas ODM con RTK + GCP

- Con GCP, ODM prioriza los puntos de suelo; si dejas el desfase de ~55 m sin corregir, el informe mostrará un **GPS Z error enorme** aunque el modelo “pegue” a los GCP.
- En instalaciones avanzadas se menciona `bundle_compensate_gps_bias` en OpenSfM para absorber un bias sistemático; lo más fiable sigue siendo **igualar el datum vertical** con `--gps-z-offset` o convirtiendo Z a mano.
- Tras el procesado, abre el DSM en **QGIS** y compara la cota en un GCP: debe acercarse a ~835.7–836.2 m, no a ~890 m.

---

## Comprobar N en QGIS (opcional)

1. Carga `es_ign_EGM08_REDNAP.tif` y el shapefile `GPS_Campus/_POINTSZ`.
2. Usa *Identify* o *Sample raster values* sobre un GCP: el valor del ráster es **N**.
3. Verifica `H + N ≈ h_suelo` del RTK del dron.

---

## Checklist final

- [ ] Identificado el dron: **M3M** (multiespectral) / **M3T** (térmico), RTK, altura elipsoidal.
- [ ] GCP con **Sokkia GCX3** en ETRS89/UTM30N + **H ortométrica REDNAP** (~836 m).
- [ ] Geoide `es_ign_EGM08_REDNAP.tif` disponible (N ≈ 55.3 m aquí).
- [ ] Metashape: CRS compuesto **o** conversión previa de Z; error Z de markers << 1 m.
- [ ] ODM: `--gps-z-offset -55.3` (o Z unificadas en `geo`/`gcp`); DSM coherente con los GCP en QGIS.
- [ ] Productos finales documentados: ¿ortométricas REDNAP o elipsoidales WGS84?

## Referencias

- Curso: [VI Curso de Fotogrametría con drones](https://cursosdeverano.unileon.es/vi-curso-de-fotogrametria-con-drones/)
- Antena GCP: [Sokkia GCX3](https://www.topconpositioning.asia/jp/en/products/brand/sokkia/gcx3/)
- [Agisoft — height above geoid](https://agisoft.freshdesk.com/support/solutions/articles/31000148332-how-to-use-height-above-geoid-for-the-coordinate-system)
- [ODM — gps-z-offset](https://docs.opendronemap.org/arguments/gps-z-offset/)
- [QGIS](https://qgis.org/)
