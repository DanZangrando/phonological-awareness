# Proyecto de Conciencia Fonológica: Conectando Grafemas y Fonemas

**Tecnologías:** Python, PyTorch, Transformers (wav2vec2), gTTS, Jupyter/Colab

## 1. Objetivo del Proyecto

Este proyecto investiga el proceso de conciencia fonológica mediante la creación y evaluación de dos vías computacionales (una auditiva y una visual) para procesar el lenguaje. El objetivo final es determinar si una representación visual de una letra (grafema) puede ser mapeada a la representación neuronal de su sonido correspondiente (fonema), y si estas representaciones pueden ser utilizadas para reconstruir palabras.

## 2. Metodología

El proyecto se divide en cuatro fases principales, implementadas en cuadernos de Jupyter.

### Fase 1: Generación del Dataset Auditivo

- **Entrada**: Un listado de fonemas/letras clave en español e inglés.
- **Proceso**: Se utiliza la librería `gTTS` para generar un archivo de audio `.wav` limpio para cada fonema individual.
- **Salida**: Un dataset etiquetado de `(audio_fonema, etiqueta_fonema)`.

### Fase 2: Vía Auditiva (Fonema -> Clasificación)

- **Entrada**: El dataset de audio de fonemas.
- **Proceso**:
  1.  Se extraen embeddings de características de cada audio usando un modelo `wav2vec2` pre-entrenado.
  2.  Se entrena una **Red Neuronal Convolucional (CNN 1D)** para clasificar los embeddings y predecir la etiqueta del fonema correspondiente.
- **Salida**: Un modelo clasificador de fonemas entrenado y los embeddings `wav2vec2` almacenados.

### Fase 3: Vía Visual (Grafema -> Embedding)

- **Entrada**: El dataset de imágenes de letras **EMNIST**.
- **Proceso**: Se entrena un modelo **CNN+MLP** para realizar una tarea de regresión: a partir de una imagen de una letra, predecir el embedding `wav2vec2` de su fonema asociado.
- **Evaluación**: La calidad de la predicción se mide con **similitud coseno** y **distancia euclidiana** contra los embeddings reales de la Fase 2.

### Fase 4: Integración (Secuencia de Fonemas -> Palabra)

- **Entrada**: Una secuencia de embeddings de fonemas (ya sea los reales de la Fase 2 o los predichos de la Fase 3).
- **Proceso**: Se entrena una **Red Neuronal Recurrente (LSTM)** para que, a partir de una secuencia de embeddings, reconstruya la palabra escrita letra por letra.
- **Salida**: Una evaluación comparativa de la precisión de reconstrucción de palabras para ambas vías (auditiva y visual).

## 3. Estructura del Repositorio

- **/data**: Contiene los datos crudos, procesados y finales.
- **/notebooks**: Cuadernos de Jupyter para cada fase del experimento.
- **/src**: Scripts de Python con código modular (modelos, funciones de ayuda).
- **/results**: Almacena los resultados como figuras y modelos entrenados.
- `requirements.txt`: Lista de dependencias de Python.
