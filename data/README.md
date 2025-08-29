# Carpeta de Datos

Esta carpeta contiene todos los datos utilizados y generados a lo largo del proyecto, organizados por etapa de procesamiento.

**Nota Importante:** Esta carpeta está incluida en el `.gitignore` para evitar subir grandes volúmenes de datos al repositorio de Git. Los datos deben ser generados o descargados localmente.

## Subdirectorios

### `/01_raw`

Datos originales y sin procesar.

- **/dictionaries**: Contiene los archivos `.txt` con los listados de fonemas/letras en español e inglés que sirven como base para la generación de audio.
- **/emnist**: Destinado a almacenar el dataset de imágenes de letras EMNIST.

### `/02_processed`

Datos intermedios generados por los scripts de procesamiento.

- **/phoneme_audio**: Almacena los archivos de audio `.wav` para cada fonema individual, generados por `gTTS`.
- **/wav2vec2_embeddings**: Contiene los embeddings extraídos de los audios de fonemas, guardados en un formato eficiente (ej. `.npy` o `.pt`) para no tener que re-calcularlos.

### `/03_final`

Datasets limpios, pre-procesados y listos para ser utilizados directamente en el entrenamiento de los modelos.
