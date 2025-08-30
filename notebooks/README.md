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
    2.  **Preparación de Datos**: Carga los **embeddings de fonemas** (archivos `.npy`) generados en el cuaderno anterior. Los datos se dividen en conjuntos de **entrenamiento y validación** para evaluar la generalización del modelo.
    3.  **Manejo de Secuencias**: Utiliza una función `collate_fn` para aplicar **padding** a las secuencias de longitud variable, permitiendo un procesamiento eficiente en lotes.
    4.  **Entrenamiento Comparativo**: Dentro de un bucle principal, se entrena y valida un modelo **CNN 1D con Adaptive Max Pooling** para cada idioma (`es` y `en`).
    5.  **Visualización y Análisis**: Genera un conjunto de **gráficos comparativos** estandarizados (curvas de aprendizaje, matrices de confusión con paleta `plasma`, heatmaps de logits con paleta `rocket`, y un gráfico t-SNE unificado con colores y marcadores consistentes por idioma) para un análisis directo del rendimiento y las representaciones aprendidas.
    6.  **Guardado de Artefactos**: Almacena los modelos entrenados, los historiales de entrenamiento y los reportes de clasificación para su uso en los siguientes cuadernos.

- **`03_visual_pathway_training.ipynb`**:

  - **Objetivo**: Entrenar y evaluar el modelo de la vía visual, que aprende a generar representaciones auditivas a partir de imágenes.
  - **Proceso**:
    1.  Crea un `Dataset` que empareja las imágenes de letras de EMNIST con sus correspondientes secuencias de embeddings de `wav2vec2`.
    2.  Define una arquitectura híbrida: un extractor de características **CORNet-Z** pre-entrenado (cuyos pesos están congelados) acoplado a un **Decodificador LSTM** (que sí se entrena).
    3.  Entrena el modelo para que, a partir de una imagen estática, genere una **secuencia de embeddings** que imite la secuencia auditiva real. Se utiliza una **pérdida perceptual** basada en los logits de un clasificador auditivo pre-entrenado para optimizar la similitud funcional de los embeddings.
    4.  Evalúa el rendimiento comparando la **distancia euclidiana** entre las secuencias predichas y las reales.
    5.  Genera un **gráfico t-SNE unificado** para comparar visualmente los espacios de embeddings de las vías auditiva (reales) y visual (predichos), usando estilos consistentes con el cuaderno anterior.

- **`04_word_reconstruction.ipynb`**:
  - **Objetivo**: Integrar ambas vías y realizar el análisis comparativo final de reconstrucción de palabras.
  - **Proceso**: Entrena un modelo LSTM para reconstruir palabras a partir de secuencias de embeddings (tanto reales como predichos). Compara las métricas finales entre español e inglés para analizar el fenómeno de transparencia grafema-fonema.
