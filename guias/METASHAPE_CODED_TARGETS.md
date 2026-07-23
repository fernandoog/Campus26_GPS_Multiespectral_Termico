# Guía Metashape — coded targets y scale bars

Basada en: [Coded targets and Scale bars](https://agisoft.freshdesk.com/support/solutions/articles/31000148855-coded-targets-and-scale-bars#How-to-Print-Coded-Targets).  
Alturas / CRS: [`METASHAPE.md`](METASHAPE.md) · [`DATUM.md`](DATUM.md).

En Campus26 las marcas son **cruces** (marcaje manual). Las **coded targets** se detectan solas con `Detect Markers`.

| Tipo | Detección |
| --- | --- |
| Cruz / cuadrado partido | Manual / semi-automática |
| Coded target Agisoft | Automática |
| Scale bar | Escala entre dos markers |

> Solo en **Metashape Professional**. Ejemplos: [sample data](https://www.agisoft.com/downloads/sample-data/).

## 1. Imprimir

`Tools → Markers → Print Markers...` → PDF → imprime páginas del medio (12 bit = más preciso; 14/16/20 bit = más dianas).

## 2. Campo

Dianas planas; margen ~1/3 del radio; círculo central en foto entre **4–5 y 30 px**. Medir centros con GCX3 (mismas reglas de [`DATUM.md`](DATUM.md)).

## 3. Detectar

`Align Photos` → `Tools → Markers → Detect Markers...` (mismo tipo que imprimiste).

## 4. Scale bars

Selecciona 2 markers (Ctrl) → Create Scale Bar → distancia en Reference (Source) → Update.

## 5. Coordenadas GCP

Asigna Este/Norte/H (`CAMPUS27.txt`) y CRS según [`METASHAPE.md`](METASHAPE.md) → Optimize Cameras.
