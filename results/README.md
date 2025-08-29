# Carpeta de Resultados

Esta carpeta almacena las salidas generadas por los cuadernos de Jupyter durante el entrenamiento y la evaluación de los modelos.

**Nota Importante:** Los modelos entrenados (`.pth`) a menudo se incluyen en `.gitignore` debido a su gran tamaño.

## Subdirectorios

### `/figures`

Contiene todas las salidas visuales del proyecto.

- Curvas de aprendizaje (pérdida y precisión vs. épocas) para cada modelo.
- Matrices de confusión para los modelos de clasificación.
- Gráficos comparativos de métricas entre español e inglés.
- Visualizaciones de los espacios de embeddings (si se realizan).

### `/trained_models`

Almacena los pesos de los modelos entrenados, generalmente en formato `.pth` o `.pt` de PyTorch.

- `auditory_cnn_es.pth`: Modelo clasificador de fonemas para español.
- `auditory_cnn_en.pth`: Modelo clasificador de fonemas para inglés.
- `visual_embedding_predictor.pth`: Modelo que predice embeddings a partir de imágenes.
- `word_reconstructor_lstm.pth`: Modelo final para la reconstrucción de palabras.
