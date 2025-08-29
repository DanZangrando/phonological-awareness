# Carpeta de Cuadernos de Jupyter

Esta carpeta contiene los cuadernos de Jupyter que documentan y ejecutan el flujo de trabajo experimental de manera secuencial. Cada cuaderno representa una fase principal del proyecto.

## Cuadernos

- **`01_data_generation.ipynb`**:

  - **Objetivo**: Crear el dataset auditivo.
  - **Proceso**: Carga los listados de fonemas, utiliza `gTTS` para generar los archivos `.wav` correspondientes y los organiza en la carpeta `/data/02_processed/phoneme_audio`.

- **`02_auditory_pathway_training.ipynb`**:

  - **Objetivo**: Entrenar y evaluar el modelo de la vía auditiva.
  - **Proceso**: Carga los audios de fonemas, extrae sus embeddings con `wav2vec2`, entrena una CNN 1D para clasificarlos y guarda el modelo entrenado y sus métricas.

- **`03_visual_pathway_training.ipynb`**:

  - **Objetivo**: Entrenar y evaluar el modelo de la vía visual.
  - **Proceso**: Carga las imágenes de EMNIST, entrena un modelo CNN+MLP para predecir los embeddings `wav2vec2` correspondientes y evalúa la predicción con similitud coseno.

- **`04_word_reconstruction.ipynb`**:
  - **Objetivo**: Integrar ambas vías y realizar el análisis comparativo final.
  - **Proceso**: Entrena un modelo LSTM para reconstruir palabras a partir de secuencias de embeddings (tanto reales como predichos). Compara las curvas de aprendizaje y métricas finales entre español e inglés para analizar el fenómeno de transparencia grafema-fonema.
