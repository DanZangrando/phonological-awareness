# Carpeta de Cuadernos de Jupyter

Esta carpeta contiene los cuadernos de Jupyter que documentan y ejecutan el flujo de trabajo experimental de manera secuencial. Cada cuaderno representa una fase principal del proyecto.

## Cuadernos

- **`01_data_generation.ipynb`**:

  - **Objetivo**: Generar el conjunto completo de datos de entrada y sus representaciones numéricas (embeddings) para las vías auditiva y visual.
  - **Proceso**:
    1.  **Carga de Unidades Lingüísticas**: Carga las listas predefinidas de **fonemas** y las listas de **palabras** desde archivos de diccionario en `/data/01_raw/dictionaries`.
    2.  **Generación de Audio**: Utiliza `gTTS` para sintetizar archivos `.wav` para cada fonema y cada palabra. Los resultados se guardan por separado en `/data/02_processed/phoneme_audio` y `/data/02_processed/word_audio`.
    3.  **Generación Visual**: Descarga el dataset `EMNIST`, filtra las imágenes para los grafemas de una sola letra, y guarda un número limitado de ejemplos en `/data/02_processed/grapheme_images`.
    4.  **Extracción de Embeddings**: Procesa todos los archivos `.wav` generados (tanto fonemas como palabras) con el modelo `Wav2Vec2` (`facebook/wav2vec2-large-xlsr-53`). Extrae la secuencia completa de embeddings de la última capa oculta y la guarda como archivos `.npy` en `/data/02_processed/wav2vec2_embeddings` y `/data/02_processed/word_embeddings`.
    5.  **Resumen y Verificación**: En lugar de imprimir el progreso archivo por archivo, al final de cada paso de generación se muestra una **tabla de resumen de Pandas** que informa cuántos archivos fueron creados, omitidos o si se produjeron errores, proporcionando una verificación clara y concisa del proceso.

- **`02_auditory_pathway_training.ipynb`**:

  - **Objetivo**: Entrenar y evaluar de forma comparativa los modelos de la vía auditiva para español e inglés.
  - **Proceso**:
    1.  Implementa un **bucle principal** que procesa ambos idiomas (`es` y `en`) en una sola ejecución.
    2.  Para cada idioma, carga los audios de fonemas y extrae la **secuencia completa de embeddings** con `wav2vec2`. Cada fonema se representa como una matriz de `(Longitud_Variable, 1024)`.
    3.  Prepara un `DataLoader` que utiliza una función `collate_fn` para manejar las secuencias de **longitud variable**. Esta función aplica **padding** (añade ceros) a las secuencias más cortas de cada lote para que todas tengan la misma longitud, permitiendo el procesamiento en batch.
    4.  Entrena una **CNN 1D con Global Average Pooling**. Esta arquitectura está diseñada para encontrar patrones temporales en las secuencias de embeddings. La capa de Global Pooling le permite manejar las entradas con padding de manera efectiva.
    5.  Guarda todos los resultados (historial de entrenamiento, predicciones, modelos) en un diccionario estructurado.
    6.  Genera **visualizaciones comparativas** para las curvas de aprendizaje, matrices de confusión y heatmaps de logits, permitiendo un análisis directo del rendimiento entre idiomas.

- **`03_visual_pathway_training.ipynb`**:

  - **Objetivo**: Entrenar y evaluar el modelo de la vía visual, que aprende a generar representaciones auditivas a partir de imágenes.
  - **Proceso**:
    1.  Crea un `Dataset` que empareja las imágenes de letras de EMNIST con sus correspondientes secuencias de embeddings de `wav2vec2`.
    2.  Define una arquitectura híbrida: un extractor de características **CORNet-Z** pre-entrenado (cuyos pesos están congelados) acoplado a un **Decodificador LSTM** (que sí se entrena).
    3.  Entrena el modelo para que, a partir de una imagen estática, genere una **secuencia de embeddings** que imite la secuencia auditiva real. La función de pérdida utilizada es `CosineEmbeddingLoss`, que optimiza la dirección de los vectores en el espacio de embeddings.
    4.  Evalúa el rendimiento comparando la **distancia euclidiana** y la similitud coseno entre las secuencias predichas y las reales.
    5.  Genera un **gráfico t-SNE unificado** para comparar visualmente los espacios de embeddings de las vías auditiva y visual.

- **`04_word_reconstruction.ipynb`**:
  - **Objetivo**: Integrar ambas vías y realizar el análisis comparativo final de reconstrucción de palabras.
  - **Proceso**: Entrena un modelo LSTM para reconstruir palabras a partir de secuencias de embeddings (tanto reales como predichos). Compara las métricas finales entre español e inglés para analizar el fenómeno de transparencia grafema-fonema.
