# Proyecto de Conciencia Fonológica: Conectando Grafemas y Fonemas

**Tecnologías:** Python, PyTorch, Transformers (wav2vec2), gTTS, torchvision, Jupyter/Colab

## 1. Objetivo del Proyecto

Este proyecto investiga el proceso de conciencia fonológica mediante la creación y evaluación de dos vías computacionales (una auditiva y una visual) para procesar el lenguaje. El objetivo final es determinar si una representación visual de una letra (grafema) puede ser mapeada a la representación neuronal de su sonido correspondiente (fonema).

Un objetivo central es analizar el fenómeno de **transparencia grafema-fonema**, comparando el rendimiento de los modelos entre el **español** (un idioma transparente donde la correspondencia es directa) y el **inglés** (un idioma opaco con múltiples pronunciaciones para un mismo grafema).

## 2. Metodología

El proyecto se divide en cuatro fases principales, implementadas en cuadernos de Jupyter.

### Fase 1: Generación de Datos y Embeddings

- **Objetivo**: Crear el conjunto completo de datos de entrada para el proyecto.
- **Proceso**:
  1.  Se generan archivos de audio (`.wav`) para dos tipos de unidades lingüísticas: **fonemas** individuales y **palabras** completas cargadas desde diccionarios.
  2.  Se crea un dataset visual paralelo extrayendo imágenes de grafemas del dataset **EMNIST**.
  3.  **Crucialmente**, todos los archivos de audio (tanto de fonemas como de palabras) son pre-procesados con el modelo **Wav2Vec2** para extraer secuencias de embeddings de alta dimensionalidad (`.npy`), que servirán como la "verdad fundamental" auditiva para el resto del proyecto.

### Fase 2: Vía Auditiva (Fonema -> Clasificación)

- **Objetivo**: Entrenar un modelo que aprenda a identificar fonemas a partir de sus embeddings.
- **Proceso**: Se entrena una **CNN 1D** para clasificar fonemas a partir de la secuencia completa de embeddings extraída en la Fase 1.

### Fase 3: Vía Visual (Grafema -> Embedding)

- **Objetivo**: Entrenar un modelo que aprenda a "imaginar" el sonido de una letra.
- **Proceso**: Se entrena un modelo híbrido **CORNet-Z + Decodificador LSTM** para predecir la secuencia completa de embeddings auditivos a partir de una única imagen estática de una letra.

### Fase 4: Integración y Análisis Comparativo

- **Objetivo**: Evaluar la calidad de las representaciones aprendidas en un contexto de lenguaje real.
- **Proceso**: Se entrena un **LSTM** para reconstruir palabras a partir de las secuencias de embeddings generadas por las vías auditiva (real) y visual (predicha), permitiendo una comparación directa de su fidelidad y un análisis del impacto de la transparencia del idioma.

## 3. Arquitecturas de los Modelos

### Modelo 1: Clasificador de Fonemas (CNN 1D - Vía Auditiva)

Este modelo aprende a identificar un fonema a partir de los patrones temporales en su secuencia de embeddings.

`[Secuencia (Batch, L_pad, 1024)] -> {Transposición} -> [Tensor (Batch, 1024, L_pad)] -> {Bloque Conv1D} -> {Bloque Conv1D} -> {Global Avg Pooling} -> [Vector (Batch, 256)] -> {Capa Lineal} -> [Logits (Batch, N_Fonemas)]`

- **Bloques Conv1D**: Actúan como detectores de características que se deslizan a lo largo del tiempo, identificando patrones fonéticos locales.
- **Global Average Pooling**: Permite que el modelo maneje secuencias de longitud variable (con padding) y condense toda la información temporal en un único vector representativo antes de la clasificación.

### Modelo 2: Generador Visual de Embeddings (CORNet-Z + LSTM - Vía Visual)

Esta es una arquitectura híbrida que traduce una representación espacial (imagen) a una temporal (secuencia de audio).

`[Imagen (Batch, 3, 224, 224)] -> {CORNet-Z Congelado} -> [Vector Visual (Batch, 512)] -> {MLP Proyector} -> [Vector de Contexto (Batch, 512)] -> {LSTM Decoder} -> [Secuencia Predicha (Batch, L_pad, 1024)]`

- **CORNet-Z**: Actúa como un "ojo" pre-entrenado. Extrae una representación visual abstracta y de tamaño fijo de la imagen de la letra. Sus pesos no se entrenan.
- **MLP Proyector**: Una pequeña red neuronal que traduce el vector visual a un "vector de contexto" inicial para el generador de secuencias.
- **LSTM Decoder**: Actúa como una "voz". Toma el vector de contexto y lo "desenrolla" a lo largo del tiempo para generar una secuencia de embeddings que intenta imitar la estructura temporal del sonido real.

### Modelo 3: Reconstructor de Palabras (LSTM - Vía de Integración)

Este modelo aprende a deletrear una palabra a partir de la secuencia de sus fonemas constituyentes.

`[Secuencia de N Embeddings Promedio] -> {LSTM} -> [Secuencia de N Vectores Ocultos] -> {Capa Lineal} -> [Predicción de N Letras]`

- **LSTM**: Procesa la secuencia de embeddings de fonemas (ya sean los reales de la vía auditiva o los predichos de la vía visual) y aprende las relaciones contextuales y transiciones entre ellos para predecir la secuencia de letras correspondiente.
