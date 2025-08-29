# Proyecto de Conciencia Fonológica: Conectando Grafemas y Fonemas

**Tecnologías:** Python, PyTorch, Transformers (wav2vec2), gTTS, torchvision, Jupyter/Colab

## 1. Objetivo del Proyecto

Este proyecto investiga el proceso de conciencia fonológica mediante la creación y evaluación de dos vías computacionales (una auditiva y una visual) para procesar el lenguaje. El objetivo final es determinar si una representación visual de una letra (grafema) puede ser mapeada a la representación neuronal de su sonido correspondiente (fonema).

Un objetivo central es analizar el fenómeno de **transparencia grafema-fonema**, comparando el rendimiento de los modelos entre el español (un idioma transparente) y el inglés (un idioma opaco).

## 2. Metodología

El proyecto se divide en cuatro fases principales, implementadas en cuadernos de Jupyter.

### Fase 1: Generación de Datasets Paralelos

- **Proceso**: Se crean dos datasets alineados (auditivo y visual) para cada idioma.
  - **Auditivo**: Se utiliza `gTTS` para generar un archivo de audio `.wav` para cada fonema/letra de una lista predefinida.
  - **Visual**: Se descarga el dataset `EMNIST` y se extraen las imágenes correspondientes a los grafemas de una sola letra de la misma lista.
- **Salida**: Un dataset de `(audio, etiqueta)` y otro de `(imagen, etiqueta)`.

### Fase 2: Vía Auditiva (Fonema -> Clasificación)

- **Entrada**: El dataset de audio de fonemas.
- **Proceso**:
  1.  Se extrae la **secuencia completa de embeddings** de cada audio usando un modelo `wav2vec2` pre-entrenado.
  2.  Se entrena una **Red Neuronal Convolucional (CNN 1D)** para clasificar las secuencias y predecir la etiqueta del fonema.
- **Salida**: Un modelo clasificador de fonemas entrenado por cada idioma y los embeddings `wav2vec2` almacenados.

### Fase 3: Vía Visual (Grafema -> Embedding)

- **Entrada**: El dataset de imágenes de letras **EMNIST**.
- **Proceso**: Se entrena un modelo **CNN+MLP** para realizar una tarea de regresión: a partir de una imagen de una letra, predecir el embedding `wav2vec2` de su fonema asociado.
- **Evaluación**: La calidad de la predicción se mide con **similitud coseno** contra los embeddings reales de la Fase 2.

### Fase 4: Integración y Análisis Comparativo

- **Entrada**: Una secuencia de embeddings de fonemas (reales o predichos).
- **Proceso**: Se entrena una **Red Neuronal Recurrente (LSTM)** para que, a partir de una secuencia de embeddings, reconstruya la palabra escrita.
- **Análisis Final**: Se compararán las **curvas de aprendizaje** y las métricas de precisión de los modelos entre español e inglés para validar la hipótesis de la transparencia grafema-fonema.

## 3. Estructura del Repositorio

- **/data**: Contiene los datos crudos, procesados y finales.
- **/notebooks**: Cuadernos de Jupyter para cada fase del experimento.
- **/src**: Scripts de Python con código modular.
- **/results**: Almacena los resultados como figuras y modelos entrenados.
- `requirements.txt`: Lista de dependencias de Python.
