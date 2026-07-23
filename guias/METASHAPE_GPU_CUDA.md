# Guía Metashape — GPU NVIDIA y CUDA

Cómo acelerar el procesado con tarjetas **NVIDIA** (CUDA). Docs: [GPU processing](https://agisoft.freshdesk.com/support/solutions/articles/31000150614-general-information-related-to-gpu-processing) · [crashes GPU](https://agisoft.freshdesk.com/support/solutions/articles/31000160098-gpu-related-crashes-in-metashape) · [manual](https://www.agisoft.com/pdf/metashape_2_2_en.pdf).

Flujo fotogramétrico del curso: [`METASHAPE.md`](METASHAPE.md).

---

## Qué acelera la GPU (y qué no)

Metashape usa GPU sobre todo en:

| Etapa | ¿GPU? |
| --- | --- |
| Matching de imágenes (parte de Align) | Sí |
| Depth maps / nube densa (depth maps) | Sí |
| Mesh / DEM / tiled model desde depth maps | Sí |
| Texture blending, mesh refinement | Sí |
| Bundle adjustment / **Optimize Cameras** | Principalmente **CPU** |
| Construcción de ortomosaico (partes) | Mixto |

Más **CUDA cores** y más **VRAM** = mejor (depth maps y mesh se benefician mucho de VRAM).

**NVIDIA recomendada por Agisoft:** GeForce GTX 7xx o superior / Quadro M4000 o superior, con **CUDA** (compute capability ≥ 3.0 en manuales recientes). En Windows NVIDIA usa normalmente el camino **CUDA**; AMD/Intel van por OpenCL.

---

## 1. Preparar el PC (Windows)

1. Instala el **driver Game Ready o Studio** desde [NVIDIA](https://www.nvidia.com/Download/index.aspx) (no te fíes solo de Windows Update).
2. Reinicia.
3. Comprueba que la GPU aparece en el Administrador de dispositivos.
4. Opcional: `nvidia-smi` en PowerShell/CMD → debe listar la tarjeta y el driver.

No hace falta instalar el **CUDA Toolkit** completo para Metashape: usa los drivers NVIDIA. El Toolkit solo si desarrollas o depuras aparte.

---

## 2. Activar la GPU en Metashape

1. `Tools → Preferences…` (Preferencias).
2. Pestaña **GPU**.
3. Marca tu GPU **discreta NVIDIA** (p. ej. GeForce RTX / GTX).
4. **Desmarca** la GPU integrada (Intel UHD / AMD iGPU) si tienes dedicada.
5. **Desmarca** *Use CPU when performing GPU accelerated processing* si la NVIDIA es potente (recomendación oficial Agisoft: más estable y a menudo más rápido).
6. Apply → OK.

Si solo tienes iGPU: puedes activarla + CPU, pero el rendimiento será bajo.

---

## 3. CUDA vs OpenCL (NVIDIA)

En Windows, Metashape suele usar **CUDA** con NVIDIA automáticamente.

Si hay **crashes** del driver CUDA:

1. `Tools → Preferences → Advanced → Tweaks…`
2. Crea / edita el tweak: **`main/gpu_enable_cuda`**
3. Valor: **`False`** → fuerza **OpenCL** en lugar de CUDA.
4. Apply → reinicia Metashape si hace falta.

Rendimiento suele ser similar o un poco peor en OpenCL; sirve para estabilidad ([artículo crashes](https://agisoft.freshdesk.com/support/solutions/articles/31000160098-gpu-related-crashes-in-metashape)).

Para volver a CUDA: `main/gpu_enable_cuda` = **`True`** (o elimina el tweak).

---

## 4. Comprobar que CUDA está trabajando

1. Abre el **Administrador de tareas** de Windows → pestaña **Rendimiento** → tu GPU NVIDIA.
2. En uno de los gráficos, cambia a **CUDA** (no solo “3D”).
3. Lanza en Metashape algo pesado en GPU: p. ej. **Build Depth Maps** / Dense cloud.
4. Debes ver carga en **CUDA** mientras procesa.

Si solo sube “3D” y CUDA queda a 0 %: la GPU no está activada en Preferences, o esa etapa no usa GPU.

---

## 5. Varias GPUs

- Puedes marcar **varias** NVIDIA en la pestaña GPU.
- Agisoft reparte trabajo entre ellas en etapas soportadas.
- Sigue desactivando iGPU y, con GPUs potentes, la opción “Use CPU when performing GPU…”.

---

## 6. Consejos de rendimiento (dataset tipo Campus26)

| Consejo | Motivo |
| --- | --- |
| Driver NVIDIA actualizado | Menos crashes; mejor CUDA |
| Cerrar juegos / navegadores pesados | Liberan VRAM |
| Calidad depth maps acorde a la VRAM | “Ultra/High” pide mucha memoria |
| Multiespectral: procesar por sensor / no mezclar de más | Menos RAM/VRAM |
| Disco SSD para proyecto y caché | Align y depth maps leen muchas fotos |
| No forzar CPU+GPU juntas en una RTX potente | Evita cuellos de botella |

Portátiles: usa el perfil **High performance** y asegúrate de que Metashape corre en la NVIDIA (Panel de control NVIDIA → “High-performance NVIDIA processor”).

---

## 7. Si falla o va lento

1. Actualiza driver desde nvidia.com.
2. En Preferences GPU: solo la dedicada; sin “Use CPU when…”.
3. Si crashea: tweak `main/gpu_enable_cuda = False` (OpenCL).
4. Desactiva temporalmente la GPU en Preferences: si deja de crashear, el problema es driver/GPU.
5. En laptops, evita que Windows use solo la iGPU para Metashape.

---

## Checklist

- [ ] Driver NVIDIA instalado (`nvidia-smi` OK)
- [ ] Preferences → GPU: NVIDIA marcada; iGPU off
- [ ] “Use CPU when performing GPU…” **off** (si la tarjeta es buena)
- [ ] Task Manager → gráfico **CUDA** activo en depth maps
- [ ] Si crashes: probar `main/gpu_enable_cuda = False`

## Referencias

- [General information — GPU processing](https://agisoft.freshdesk.com/support/solutions/articles/31000150614-general-information-related-to-gpu-processing)
- [GPU-related crashes](https://agisoft.freshdesk.com/support/solutions/articles/31000160098-gpu-related-crashes-in-metashape)
- [Descargas driver NVIDIA](https://www.nvidia.com/Download/index.aspx)
- [`METASHAPE.md`](METASHAPE.md)
