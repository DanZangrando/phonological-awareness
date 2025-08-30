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

  - **Objetivo**: Entrenar un modelo de vía auditiva capaz de **discriminar** entre un conjunto fijo de fonemas a partir de sus embeddings de `wav2vec2`.
  - **Proceso**:
    1.  **Modularidad**: Se definen las clases `Dataset` y el modelo `PhonemeCNN` de forma independiente antes del bucle de entrenamiento para mayor claridad y eficiencia.
    2.  **Preparación de Datos**: Carga los **embeddings de fonemas** (archivos `.npy`). Los datos se dividen en conjuntos de **entrenamiento y validación** para evaluar la generalización.
    3.  **Manejo de Secuencias**: Utiliza una función `collate_fn` para aplicar **padding** a las secuencias de longitud variable, permitiendo un procesamiento eficiente en lotes.
    4.  **Entrenamiento Comparativo**: Dentro de un bucle principal, se entrena y valida un modelo **CNN 1D con Adaptive Max Pooling** para cada idioma (`es` y `en`).
    5.  **Visualización y Análisis**: Genera un conjunto de **gráficos comparativos** estandarizados (curvas de aprendizaje, matrices de confusión, heatmaps de logits, y un gráfico t-SNE) para un análisis directo del rendimiento.
    6.  **Guardado de Artefactos**: Almacena los modelos entrenados y los reportes de clasificación.

- **`03_visual_pathway_training.ipynb`**:

  - **Objetivo**: Entrenar y evaluar el modelo de la vía visual, que aprende a generar representaciones auditivas ("imaginar el sonido") a partir de imágenes de grafemas.
  - **Proceso**:
    1.  **Pre-procesamiento**: Realiza una tarea única de redimensionar las imágenes de EMNIST a 64x64 para optimizar la velocidad del entrenamiento.
    2.  **Arquitectura Híbrida**: Define un modelo `VisualToAuditoryModel` que consiste en un extractor de características **CORNet-Z** (con pesos congelados) y un **Decodificador LSTM** (entrenable).
    3.  **Entrenamiento con Pérdida Perceptual**: Utiliza el clasificador auditivo del cuaderno 02 como un "experto" congelado. La función de pérdida se calcula sobre los **logits** de este experto, forzando a la vía visual a generar embeddings que no solo se parezcan, sino que sean **funcionalmente equivalentes** a los reales.
    4.  **Análisis Cualitativo (t-SNE)**: Genera un gráfico t-SNE de alta resolución que mapea los embeddings reales y los predichos. El gráfico usa **color para el idioma**, **marcador para la ruta** (real vs. predicho) y **etiqueta de forma inteligente solo los errores de clasificación**, permitiendo un análisis visual profundo de las fallas del modelo.
    5.  **Evaluación Cuantitativa**: Mide el rendimiento de forma cruzada, alimentando los embeddings generados por la vía visual al clasificador auditivo y calculando métricas de clasificación (Accuracy, F1-Score, etc.).

- **`04_word_reconstruction.ipynb`**:
  - **Objetivo**: Modelar la **conciencia fonológica a nivel de palabra**, entrenando un modelo que aprenda a componer la representación auditiva de una palabra completa a partir de la secuencia de sus fonemas constituyentes.
  - **Proceso**:
    1.  **Preparación de Datos**: Se establece un nuevo problema de mapeo. La **entrada (X)** es una secuencia de embeddings de fonemas (ej. `[emb('b'), emb('a'), emb('l'), emb('a')]`). El **objetivo (Y)** es el embedding único de la palabra completa (ej. `emb('bala')`).
    2.  **Creación de "Imágenes Neurales"**: Las secuencias de embeddings de fonemas (vectores de 1024D) se apilan para formar una matriz 2D. Se utiliza un `collate_fn` para aplicar padding y asegurar que todas las matrices de un lote tengan el mismo tamaño, creando así "imágenes neurales" de las palabras descompuestas en fonemas.
    3.  **Arquitectura y Entrenamiento**: Se entrena una **Red Neuronal Convolucional 2D (CNN 2D)**. Esta red aprende a detectar patrones espaciales y de transición en las matrices de fonemas para predecir el vector final del embedding de la palabra.
    4.  **Análisis Final**: La métrica de rendimiento de este modelo (qué tan cerca están las predicciones de los embeddings de palabras reales) se utiliza como una medida cuantitativa del proceso de **pasaje de "imágenes neurales de fonemas" a la "imagen neural del sonido de la palabra"**, comparando este fenómeno entre español e inglés.
