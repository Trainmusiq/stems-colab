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
- **Sube SOLO los temas pendientes.** Cada versión parte de cero: todo lo que esté en el Dataset se procesa completo, aunque ya lo hayas procesado antes. Ejemplo: si ya tienes listos los 4 primeros, sube desde "05 - Adiós" en adelante, en un Dataset nuevo o en una nueva versión.
- **Cuánto subir:** en el Output de Kaggle solo quedan los zips, de ~350 MB por tema en modo completo, con un tope de ~20 GB. Unos 15 temas por ejecución caben con holgura (~5,3 GB).
  - Los stems sueltos y los modelos (~5 GB) van a `/kaggle/temp`, que no se guarda. Ahí se trabaja un tema a la vez: después de crear y verificar su zip, se borran sus stems sueltos.
  - El notebook revisa los dos espacios antes de empezar y se detiene con un mensaje claro si no alcanza.
  - Lo que suele limitar es el tiempo: 12 horas por ejecución.
- **Afinación a mano (opcional):** agrega junto al audio un archivo `Nombre del tema.afinacion.txt` con una línea `corregir -8.0`, `detectado 442.1` o `no_afinar`.
  - **Un Dataset no se edita.** Para agregar o cambiar un `.afinacion.txt` después:
    1. Abre tu Dataset › **New Version** › sube el archivo (y el audio, si no estaba) › **Create**.
    2. En el notebook, panel derecho › *Input*: en tu Dataset, actualiza a la versión nueva. Si aparece un aviso de versión nueva, acéptalo; si no, quítalo y vuelve a agregarlo con **+ Add Input**.
    3. Recién entonces vuelve a correr **Save & Run All**.

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
- El resumen queda en `stems/salida/progreso.txt` (pestaña Output), que se va reescribiendo durante la ejecución.

Tiempos aproximados: unos 2–2,5 min por minuto de canción en modo completo y ~1 min en karaoke. La primera vez de cada sesión hay que sumar la descarga de modelos.

## 7. Descargar los resultados
1. Abre la versión terminada del notebook › pestaña **Output**.
2. El Output contiene **solo** esto:
   - `stems/zips/<tema>.zip`: **un zip por tema**. Trae la carpeta del tema: stems numerados para el DAW, `afinacion.txt`, `guitarras.txt` y el manifiesto.
   - `stems/salida/progreso.txt`: tabla resumen, avisos ⚠ de afinación y lista de zips.
   - `stems/salida/errores.txt`: solo si algún tema falló.
3. Descarga los zips con el botón de descarga de cada archivo.

Los stems sueltos y los modelos no aparecen en el Output: viven en `/kaggle/temp` y se pierden al terminar, a propósito.

## Si algo falla
| Mensaje o síntoma | Qué hacer |
|---|---|
| "No hay GPU activa" | Session options › Accelerator › **GPU T4 x2**. Si no aparece, verifica el teléfono (paso 1) o revisa si ya usaste tu cuota semanal de GPU (se ve en tu perfil). |
| "Kaggle no tiene Internet" | Session options › **Internet: On**. Requiere el teléfono verificado. |
| "Hay N Datasets… / No encuentro el Dataset" | Escribe el nombre exacto en `KAGGLE_DATASET`. Es el que aparece en el panel derecho, debajo de *Input*. |
| "Los zips no caben en /kaggle/working" | Más de ~55 temas en una ejecución: sube menos temas por Dataset, usa `MODO = "karaoke"` o `FORMATO = "MP3 320"`. |
| "No hay espacio en /kaggle/temp" | No caben los modelos (~5 GB) más un tema. Prueba con `MODO = "karaoke"` o `SEPARAR_ACUSTICA_ELECTRICA = False` (~1,4 GB menos de modelos). |
| "El zip de … no se verificó" | No se borró nada: ese tema queda con ERROR y los demás siguen. Vuelve a correr con solo ese tema en el Dataset. |
| Un tema con ERROR en la tabla | El detalle está en `stems/salida/errores.txt` (Output). Los demás temas siguen igual. |
| Se cortó a las 12 horas | Divide los temas en dos Datasets y corre una versión con cada uno. |
| ⚠ de afinación en la tabla | Lee el `afinacion.txt` dentro de su zip. Si hace falta, crea una **New Version** del Dataset con `Tema.afinacion.txt` (y solo los temas a rehacer), actualiza el input del notebook a esa versión y vuelve a correr. |

### Notas
- **Nada persiste entre sesiones:** Kaggle borra todo al terminar cada ejecución, salvo el Output de esa versión. Cada versión empieza de cero, así que REPROCESAR casi nunca hace falta aquí. Dentro de una misma sesión, un tema cuyo zip ya existe se salta; con REPROCESAR, su zip anterior se mueve a `stems/zips/_anteriores/` (no se borra).
- **Los Datasets no se modifican nunca:** el notebook solo los lee.
- **Temporales:** stems sueltos, intermedios y modelos van a `/kaggle/temp` y no quedan en el Output.
- **Cantidad de archivos del Output:** Kaggle parece tener un límite de cantidad de archivos en el Output, pero no conozco la cifra exacta. Con este diseño quedan ~15 zips + 2 `.txt` por ejecución, muy pocos archivos.
