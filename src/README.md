# Carpeta de Código Fuente

Esta carpeta contiene scripts de Python con código modular y reutilizable para mantener los cuadernos limpios y enfocados en el flujo experimental.

## Módulos

- **`data_creation.py`**:

  - **Propósito**: Contener funciones relacionadas con la creación y pre-procesamiento de datos. Por ejemplo, una función que toma una lista de fonemas y genera los archivos `.wav` usando `gTTS`.

- **`models.py`**:

  - **Propósito**: Definir las arquitecturas de los modelos de PyTorch utilizados en el proyecto. Esto incluye las clases para la CNN 1D (Fase 2), el modelo CNN+MLP (Fase 3) y el LSTM (Fase 4).

- **`evaluation.py`**:
  - **Propósito**: Contener funciones para la evaluación de los modelos. Por ejemplo, funciones para calcular la precisión, generar matrices de confusión, calcular la similitud coseno, y graficar las curvas de pérdida y precisión del entrenamiento.
