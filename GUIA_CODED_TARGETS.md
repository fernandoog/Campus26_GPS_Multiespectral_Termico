# Guía: coded targets y scale bars en Metashape

Basada en la documentación oficial de Agisoft: [Coded targets and Scale bars](https://agisoft.freshdesk.com/support/solutions/articles/31000148855-coded-targets-and-scale-bars#How-to-Print-Coded-Targets).

## Por qué importa (respecto a este curso)

En Campus26 las marcas del suelo son **cuadrados partidos en el vértice** (cruz clásica). Hay que **clicar a mano** el centro en cada foto (o semi-automático tras 2–3 fotos).

Las **coded targets** (dianas codificadas) de Metashape se **detectan solas**: cada diana tiene un patrón único y Metashape crea el marcador con su etiqueta en todas las imágenes donde la ve.

| Tipo de marca | Detección | Uso típico |
| --- | --- | --- |
| Cuadrado partido / cruz (este dataset) | Manual / semi-automática | GCP con [Sokkia GCX3](https://www.topconpositioning.asia/jp/en/products/brand/sokkia/gcx3/) |
| **Coded target** Agisoft | **Automática** (`Detect Markers`) | GCP, puntos de apoyo al alineado, scale bars |
| Scale bar entre dos markers | Tras detectar | Escala del modelo sin GNSS (o como refuerzo) |

> **Requisito:** markers, coded targets y scale bars están disponibles en **Metashape Professional**, no en la versión Standard/demo completa. Datos de ejemplo: [Agisoft sample data](https://www.agisoft.com/downloads/sample-data/).

---

## 1. Imprimir coded targets

1. En Metashape: `Tools → Markers → Print Markers...`
2. Elige el tipo de diana (ver tabla abajo) y confirma.
3. Guarda el **PDF**. Contiene **todas** las dianas posibles de ese tipo: no hace falta imprimir todas las páginas.
4. Imprime solo las páginas que vayas a usar (Agisoft recomienda coger páginas del **medio** del PDF).

### Tipos de diana circular

Metashape soporta cuatro patrones:

| Tipo | Ventaja |
| --- | --- |
| **12 bit** | Decodificación más precisa (recomendado si caben pocas dianas) |
| **14 / 16 / 20 bit** | Más dianas distintas en el mismo proyecto |

---

## 2. Colocarlas en campo y fotografiar

Reglas de Agisoft (resumidas):

- **Planas**: sin arrugas ni deformaciones.
- Cortar respetando el círculo y un **margen exterior ≈ 1/3 del radio** del círculo exterior.
- **No retocar** el patrón (ni pintar, ni laminado que brille en exceso si distorsiona el contraste).
- Deben verse nítidas en **al menos un par de fotos** (mejor en muchas, repartidas).
- Tamaño en la imagen (criterio oficial):
  - Radio del **círculo negro central** en la foto: **mínimo ~4–5 px**, **máximo ~30 px**.
  - Si sale más grande → diana demasiado grande o vuelo demasiado bajo; si sale más pequeña → no se detectará bien.

### Flujo típico con dron + GNSS (como en el curso)

1. Imprimes p. ej. 9 coded targets (`target 12`, `target 13`, …).
2. Las fijas en el suelo en las posiciones de control.
3. Mides cada centro con la **GCX3** (mismas reglas de altura ortométrica / geoide que en `GUIA_ALTURAS.md`).
4. Vuelas (M3M / M3T, etc.).
5. En Metashape: detectas markers → asignas coordenadas Este/Norte/H a cada etiqueta.

Así evitas el clic manual en el vértice de la cruz.

---

## 3. Detectar automáticamente

1. Importa las fotos y (recomendado) alinea: `Workflow → Align Photos...`.
2. `Tools → Markers → Detect Markers...`
3. En el diálogo:
   - **Marker Type** = el mismo que imprimiste (12/14/16/20 bit).
   - Ajusta **Tolerance** si hay falsos positivos o fallos (prueba valores por defecto primero).
4. `OK` y espera. Los markers aparecen con el **nombre del código** de cada diana.

Puedes detectar **después** del procesado; no es obligatorio hacerlo antes del alineado, aunque ayuda si usas markers para reforzar el matching (`Align Selected Cameras`).

---

## 4. Scale bars (barras de escala)

Si conoces la **distancia real** entre dos coded targets (cinta métrica, estación total, etc.), puedes fijar la **escala** del modelo sin (o además de) GNSS.

1. Alinea las fotos si aún no lo has hecho. Los markers se ven en la vista **Model**.
2. Con una herramienta de selección, haz clic en el primer marker y, con **Ctrl**, en el segundo.
3. Clic derecho → **Create Scale Bar** (también desde el panel Workspace sobre la pareja seleccionada).
4. Repite para cada distancia conocida.
5. Panel **Reference** → pestaña **Source** (botón *View Source*): escribe la distancia real de cada scale bar.
6. Activa (check) las scale bars que quieras usar → botón **Update** en la barra del panel Reference.

La escala se aplica al modelo. Para orientar el modelo en el espacio (si no tienes GCP/CRS), usa la herramienta **Rotate Object** de la barra de herramientas.

### Scale bar vs GCP

| | Scale bar | GCP (coordenadas) |
| --- | --- | --- |
| Qué aporta | Escala (y a veces forma local) | Posición absoluta + escala + orientación |
| Hace falta GNSS | No | Sí (p. ej. GCX3) |
| Ideal para | Interiores, objetos, vuelos sin RTK | Cartografía, SIG, QGIS |

En prácticas tipo Campus26 lo habitual es **GCP con coordenadas**; las scale bars son un complemento útil o un plan B.

---

## 5. Asignar coordenadas a coded targets (GCP)

Tras `Detect Markers`:

1. Panel **Reference** → selecciona cada marker.
2. Introduce Este, Norte, cota (p. ej. desde un listado tipo `CAMPUS27.txt`).
3. Configura el CRS (ETRS89/UTM30N + ortométricas REDNAP; ver `GUIA_ALTURAS.md`).
4. Activa los markers → **Update** / `Optimize Cameras`.

No hace falta buscar el “vértice de la cruz”: el centro geométrico de la diana codificada es el punto de referencia.

---

## 6. Checklist de campo (próximo vuelo)

- [ ] Metashape **Pro** disponible para markers / detect.
- [ ] PDF de coded targets impreso (tipo elegido documentado: p. ej. 12 bit).
- [ ] Dianas planas, fijas, tamaño adecuado a la altura de vuelo (4–5 … 30 px del círculo central).
- [ ] Cada diana visible en varias fotos del bloque.
- [ ] Coordenadas medidas en el **centro** de cada diana (GCX3 + geoide si aplica).
- [ ] Opcional: distancias entre pares medidas → scale bars.
- [ ] En gabinete: `Detect Markers` → CRS / alturas → Optimize.

---

## Relación con este repositorio

| Dataset Campus26 (julio 2026) | Con coded targets |
| --- | --- |
| Marcas cruz / cuadrado partido | Sustituir o complementar por dianas Agisoft |
| Marcaje manual del vértice | `Detect Markers` automático |
| GCX3 + `CAMPUS27.txt` | Igual, pero etiquetas = IDs de las dianas |
| Problema elipsoidal vs ortométrica | Sigue aplicando (`GUIA_ALTURAS.md`) |

## Referencias

- [Agisoft — Coded targets and Scale bars](https://agisoft.freshdesk.com/support/solutions/articles/31000148855-coded-targets-and-scale-bars#How-to-Print-Coded-Targets)
- [Agisoft sample data](https://www.agisoft.com/downloads/sample-data/)
- `GUIA_ALTURAS.md` — datum vertical dron vs GCX3
- Curso: [VI Curso de Fotogrametría con drones](https://cursosdeverano.unileon.es/vi-curso-de-fotogrametria-con-drones/)
