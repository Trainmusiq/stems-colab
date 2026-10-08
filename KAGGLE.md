# Usar el notebook en Kaggle (gratis, en segundo plano)

Kaggle presta una GPU T4 gratis por varias horas a la semana y puede correr el notebook **en segundo plano**: lo lanzas, cierras el navegador y vuelves cuando termina. Usa el archivo `stems_kaggle.ipynb`, que tiene el mismo código que la versión de Colab: el notebook detecta solo que está en Kaggle.

> Los menús de Kaggle cambian de nombre de vez en cuando. Si algo no aparece exactamente así, busca la opción equivalente en el mismo lugar.

## 1. Crear la cuenta y verificar el teléfono (una sola vez)
1. Entra a <https://www.kaggle.com> y elige **Register**. Puedes entrar con tu cuenta de Google.
2. Arriba a la derecha, abre tu foto › **Settings**.
3. En **Phone verification**, ingresa tu número y el código que te llega por SMS.
   Sin este paso Kaggle **no** te deja usar la GPU ni Internet, y el notebook no funciona.

## 2. Subir tus temas como Dataset
1. Menú izquierdo › **Datasets** › **+ New Dataset** (o **Create › New Dataset**).
2. Arrastra tus archivos FLAC, WAV o MP3. Pueden ir en subcarpetas.
3. Ponle un nombre corto, por ejemplo `temas-soda`. Déjalo **Private**.
4. Pulsa **Create** y espera a que termine de procesar.

Consejos:
- **Cuánto subir:** sube unos 15 temas por Dataset. En modo completo cada tema ocupa unos 350 MB de salida, y Kaggle guarda como máximo unos 20 GB por ejecución. El notebook revisa el espacio y se detiene antes de empezar si no alcanza.
- **Afinación a mano (opcional):** agrega junto al audio un archivo `Nombre del tema.afinacion.txt` con una línea `corregir -8.0`, `detectado 442.1` o `no_afinar`.

## 3. Importar el notebook desde GitHub
1. Menú izquierdo › **Code** › **+ New Notebook** (o **Create › New Notebook**).
2. En el notebook nuevo: **File › Import Notebook**.
3. Abre la pestaña **GitHub**, escribe `Trainmusiq/stems-colab` y elige **`stems_kaggle.ipynb`**.
   - Si el repositorio es privado, Kaggle te pedirá conectar tu cuenta de GitHub.
   - Si no lo encuentra, descarga `stems_kaggle.ipynb` desde GitHub e impórtalo con la opción de subir archivo.
4. Arriba, cámbiale el nombre al notebook, por ejemplo "Stems Soda".

## 4. Agregar el Dataset y activar GPU e Internet
En el panel derecho del notebook:
1. **+ Add Input** › **Your Datasets** › elige tu Dataset (`temas-soda`).
2. En **Session options**:
   - **Accelerator:** **GPU T4 x2**. El notebook usa una sola de las dos.
   - **Internet:** **On**. Los modelos se descargan en cada ejecución, más de 3 GB la primera vez de cada sesión.

## 5. Ajustar la configuración
La primera celda de código es la configuración. En Kaggle no hay formularios: se editan los valores en el texto. Por ejemplo:
```python
MODO = "karaoke"        # o "completo"
REFERENCIA = "440 Hz"
FORMATO = "FLAC 24-bit"
KAGGLE_DATASET = ""     # vacío si agregaste un solo Dataset; si hay varios, su nombre: "temas-soda"
```
No cambies nada más.

## 6. Ejecutar en segundo plano: Save & Run All
1. Arriba a la derecha: **Save Version**.
2. Elige **Save & Run All (Commit)** y pulsa **Save**.
3. Ya puedes cerrar el navegador: Kaggle lo corre solo, hasta 12 horas por ejecución.

Para seguir el avance:
- Abre el notebook › **Version History**, o el aviso de la ejecución en curso, › **Logs**.
- Verás una línea por etapa, por ejemplo:
  `[14:03:12] ▶ Tema 2/15 «Prófugos» · etapa 5/12: guitarras · 38 % · …`
- Al terminar, el resumen queda también en `stems/salida/progreso.txt` (pestaña Output).

Tiempos aproximados: unos 2–2,5 min por minuto de canción en modo completo y ~1 min en karaoke. La primera vez de cada sesión hay que sumar la descarga de modelos.

## 7. Descargar los resultados
1. Abre la versión terminada del notebook › pestaña **Output**.
2. Entra a `stems/zips/`: hay **un `.zip` por tema**. Descarga los que quieras con el botón de descarga.
3. Cada zip trae la carpeta del tema: stems numerados para el DAW, `afinacion.txt`, `guitarras.txt` y la prueba de cancelación.
4. Revisa `stems/salida/progreso.txt` para ver la tabla resumen y los avisos ⚠ de afinación.

## Si algo falla
| Mensaje o síntoma | Qué hacer |
|---|---|
| "No hay GPU activa" | Session options › Accelerator › **GPU T4 x2**. Si no aparece, verifica el teléfono (paso 1) o revisa si ya usaste tu cuota semanal de GPU (se ve en tu perfil). |
| "Kaggle no tiene Internet" | Session options › **Internet: On**. Requiere el teléfono verificado. |
| "Hay N Datasets… / No encuentro el Dataset" | Escribe el nombre exacto en `KAGGLE_DATASET`. Es el que aparece en el panel derecho, debajo de *Input*. |
| "No cabe en /kaggle/working" | Sube menos temas por Dataset, usa `MODO = "karaoke"` o `FORMATO = "MP3 320"`. |
| Un tema con ERROR en la tabla | El detalle está en `stems/salida/errores.txt` (Output). Los demás temas siguen igual. |
| Se cortó a las 12 horas | Divide los temas en dos Datasets y corre una versión con cada uno. |
| ⚠ de afinación en la tabla | Lee su `afinacion.txt`. Si hace falta, agrega `Tema.afinacion.txt` al Dataset y vuelve a correr. |

### Notas
- **Nada persiste entre sesiones:** Kaggle borra todo al terminar cada ejecución, salvo el Output de esa versión. Cada versión empieza de cero, así que REPROCESAR casi nunca hace falta aquí.
- **Los Datasets no se modifican nunca:** el notebook solo los lee.
- **Temporales:** van a `/kaggle/temp` y no quedan en el Output.
