# Carpeta de Cuadernos de Jupyter

Esta carpeta contiene los cuadernos de Jupyter que documentan y ejecutan el flujo de trabajo experimental de manera secuencial. Cada cuaderno representa una fase principal del proyecto.

## Cuadernos

- **`01_data_generation.ipynb`**:

  - **Objetivo**: Crear el dataset auditivo.
  - **Proceso**: Carga los listados de fonemas, utiliza `gTTS` para generar los archivos `.wav` correspondientes y los organiza en la carpeta `/data/02_processed/phoneme_audio`.

- **`02_auditory_pathway_training.ipynb`**:

  - **Objetivo**: Entrenar y evaluar de forma comparativa los modelos de la vía auditiva para español e inglés.
  - **Proceso**:
    1.  Implementa un **bucle principal** que procesa ambos idiomas (`es` y `en`) en una sola ejecución.
    2.  Para cada idioma, carga los audios de fonemas y extrae la **secuencia completa de embeddings** con `wav2vec2`.
    3.  Prepara un `DataLoader` que maneja secuencias de longitud variable mediante **padding**.
    4.  Entrena una **CNN 1D con Global Average Pooling** para clasificar los fonemas.
    5.  Guarda todos los resultados (historial de entrenamiento, predicciones, modelos) en un diccionario estructurado.
    6.  Genera **visualizaciones comparativas** (lado a lado o fusionadas) para las curvas de aprendizaje, matrices de confusión y heatmaps de logits.
    7.  Guarda los informes de clasificación y los modelos finales con nombres descriptivos.

- **`03_visual_pathway_training.ipynb`**:

  - **Objetivo**: Entrenar y evaluar el modelo de la vía visual.
  - **Proceso**: Carga las imágenes de EMNIST, entrena un modelo CNN+MLP para predecir los embeddings `wav2vec2` correspondientes y evalúa la predicción con similitud coseno.

- **`04_word_reconstruction.ipynb`**:
  - **Objetivo**: Integrar ambas vías y realizar el análisis comparativo final.
  - **Proceso**: Entrena un modelo LSTM para reconstruir palabras a partir de secuencias de embeddings (tanto reales como predichos). Compara las curvas de aprendizaje y métricas finales entre español e inglés para analizar el fenómeno de transparencia grafema-fonema.
