# README.md

## Flujo de Trabajo: Modelado de la Conciencia Fonológica con IA

Este proyecto investiga un fenómeno cognitivo clave: la capacidad de construir una **"imagen auditiva"** de una palabra completa a partir de escuchar los sonidos de sus letras deletreadas (por ejemplo, generar la representación de "perro" a partir de los sonidos "pe", "e", "erre", "o").

El núcleo del experimento es determinar si un modelo puede aprender a **traducir la secuencia de embeddings** correspondiente al deletreo de una palabra a la **secuencia de embeddings** que representa el sonido de la palabra completa.

El flujo de trabajo se divide en dos fases experimentales principales, soportadas por cuadernos de preparación:

1.  **Fase 1 (Vía Auditiva Pura)**: Se entrena un modelo para ver si puede aprender esta compleja tarea de traducción de secuencias utilizando únicamente información auditiva.
2.  **Fase 2 (Vía Multimodal)**: Se introduce una vía visual (la imagen de las letras) para medir cómo este refuerzo afecta y potencia el aprendizaje del fenómeno.

La estructura se compone de cuatro cuadernos que guían el proceso de principio a fin.

---

### ➡️ 1. Cuaderno 01: Generación de Datos y Embeddings

**Objetivo principal**: Construir de manera automatizada todos los activos necesarios para los experimentos: datasets paralelos (auditivo, visual) y sus representaciones numéricas (embeddings).

#### **¿Qué hace?**

1.  **Creación de Diccionarios**: Genera listas de palabras filtradas y aleatorias en español e inglés.
2.  **Generación de Activos por Palabra**: Para cada palabra, crea:
    * **Dataset Visual**: Imágenes de sus caracteres usando **EMNIST**.
    * **Dataset Auditivo**: Audio de la palabra completa, sus fonemas y, crucialmente, el **nombre de cada una de sus letras**.
3.  **Extracción de Embeddings**: Utiliza **Wav2Vec 2.0 (`wav2vec2-large-xlsr-53`)** para convertir todos los audios en **secuencias de embeddings** de alta calidad (dimensión 1024), que son la base para el aprendizaje de los modelos.

**Decisión clave**: El uso de **Wav2Vec 2.0** es fundamental, ya que captura características fonéticas ricas del habla a lo largo del tiempo, permitiendo que los modelos neuronales "entiendan" y manipulen las secuencias de sonido de forma efectiva.

---

### ➡️ 2. Cuaderno 02: Entrenamiento de Clasificadores Expertos

**Objetivo principal**: Entrenar dos clasificadores "jueces" casi perfectos (uno para fonemas, otro para palabras) que servirán como **ground-truth** para evaluar la calidad de las secuencias de embeddings generadas por los modelos principales.

#### **¿Qué hace?**

1.  **Define la Arquitectura**: Utiliza un modelo `SequentialClassifierLSTM` (LSTM + capa lineal).
2.  **Entrenamiento sobre el 100% de los Datos**: Se entrena con todos los datos para asegurar que el modelo pueda separar las clases perfectamente. El objetivo no es la generalización, sino la **fiabilidad del juez**.
3.  **Guardado del Modelo**: Almacena los clasificadores entrenados para su uso posterior.

**Decisión clave**: Estos expertos se usarán para calcular una **pérdida perceptual**. En lugar de comparar si dos secuencias de embeddings son numéricamente idénticas, se comprueba si el "juez" las clasifica como la misma palabra. Esto es una métrica de evaluación mucho más robusta y cognitivamente relevante.

---

### ➡️ 3. Cuaderno 03: Vía Auditiva - El Experimento Principal

**Objetivo principal**: Modelar el proceso cognitivo central: ¿es posible **traducir una secuencia de embeddings** (sonido de letras deletreadas) a **otra secuencia de embeddings** (sonido de la palabra completa)?

#### **¿Qué hace?**

1.  **Define la Arquitectura (Seq2Seq con Atención)**:
    * **Encoder (Transformer)**: Procesa la secuencia de entrada (los embeddings de los nombres de las letras, ej: "pe", "e", "erre", "o").
    * **Decoder (LSTM con Atención)**: Genera, paso a paso, una nueva secuencia de embeddings que debe corresponder a la del audio de la palabra completa ("perro").
2.  **Entrenamiento**: El modelo se entrena para minimizar la distancia (pérdida L1) entre la secuencia de embeddings que genera y la secuencia real objetivo.
3.  **Evaluación Avanzada**: El éxito se mide con métricas de generalización:
    * **Pérdida con Clasificador Experto**: ¿La secuencia generada "engaña" al juez del Cuaderno 02 y es clasificada correctamente?
    * **Visualización t-SNE**: ¿Las secuencias generadas para palabras no vistas se agrupan cerca de sus correspondientes secuencias reales?

**Decisión clave**: La arquitectura **Seq2Seq con Atención** es la elección idónea para este problema, ya que está diseñada específicamente para transformar secuencias de entrada en secuencias de salida, que es exactamente el fenómeno que se busca modelar.

---

### ➡️ 4. Cuaderno 04: Síntesis Multimodal - El Efecto de la Ruta Visual

**Objetivo principal**: Probar la hipótesis de que el **aprendizaje multimodal** (añadir información visual) es clave para refinar la tarea de "traducción auditiva" que el modelo anterior intentó desarrollar de forma puramente unisensorial.

#### **¿Qué hace?**

Este cuaderno replica el experimento del Cuaderno 03, pero con una modificación fundamental en la entrada del modelo.

1.  **Input Multimodal**: El Encoder del modelo Seq2Seq ahora recibe una secuencia que combina **dos fuentes de información** para cada letra:
    * El embedding del **nombre de la letra** (auditivo, igual que antes).
    * El embedding de la **imagen del grafema** (visual, de EMNIST).
2.  **Entrenamiento y Comparación**: El proceso de entrenamiento y las métricas de evaluación son idénticos a los del Cuaderno 03. El objetivo es **comparar directamente los resultados** y demostrar si la adición del canal visual mejora la capacidad del modelo para generar secuencias de embeddings más precisas y generalizar a palabras no vistas.

**Decisión clave**: Este diseño experimental se basa en la idea de que aprender a leer en humanos es un anclaje entre el símbolo visual, su nombre y su sonido. Se busca replicar esta **triangulación de información** para ver si proporciona la regularización necesaria que le faltaba al modelo puramente auditivo para tener éxito en su tarea de traducción de secuencias.