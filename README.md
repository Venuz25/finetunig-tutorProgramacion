# Sistema de Fine-Tuning: Tutor de Programación con Qwen2.5
Proyecto enfocado en la modificación del comportamiento del modelo de lenguaje Qwen2.5 para su implementación como un tutor de programación interactivo y socrático.
---

## Acerca del Proyecto

El objetivo principal de este repositorio es la adaptación del modelo de lenguaje `Qwen/Qwen2.5-0.5B-Instruct` mediante técnicas de *fine-tuning* para actuar como un tutor de programación. A diferencia de los asistentes tradicionales que proporcionan soluciones directas, este modelo está entrenado para emplear el método socrático, guiando a los usuarios a través de preguntas estratégicas y fomentando el pensamiento crítico para la resolución de problemas de programación.

El ajuste del modelo se realiza aplicando *Low-Rank Adaptation* (LoRA), lo que permite una adaptación eficiente de los parámetros del modelo sin necesidad de reentrenar la red completa, optimizando así el uso de recursos computacionales.

---

## Características Principales

- **Tutoría Socrática Interactiva:** Guía a los usuarios en la comprensión de conceptos de programación mediante preguntas reflexivas en lugar de entregar código resuelto.
- **Ajuste Eficiente con LoRA:** Implementación de LoRA para adaptar el modelo Qwen2.5, manteniendo un tamaño de archivo reducido y un bajo consumo de memoria.
- **Adaptación a Escenarios de Aprendizaje:** El modelo es capaz de identificar y responder a distintos estados del estudiante, como nivel principiante, frustración o depuración de código (*debugging*).
- **Generación Automatizada de Datasets:** Scripts dedicados para la extracción, generación y limpieza de conjuntos de datos de conversaciones, utilizando modelos como Gemini para simular diálogos entre estudiante y tutor.
- **Optimización para Hardware Limitado:** Implementación de cuantización de 4 bits (NF4) y optimizaciones mediante la biblioteca Unsloth, permitiendo el entrenamiento e inferencia en GPUs con VRAM restringida (ej. 4 GB).
- **Entorno de Pruebas Agéntico:** Script de interacción que simula una interfaz de terminal, ajustando dinámicamente el *prompt* del sistema según la intención detectada en la consulta del usuario.

---

## Tecnologías y Frameworks

- **Lenguaje de Programación:** Python
- **Modelos de Lenguaje:** Qwen2.5, Gemini (para generación de datos sintéticos)
- **Bibliotecas de IA/ML:** Hugging Face Transformers, PEFT, TRL, Unsloth, Datasets, Google Generative AI
- **Optimización de Modelos:** BitsAndBytes (cuantización), Unsloth
- **Gestión de Entorno:** `requirements.txt`, variables de entorno (`.env`)

---

## Instalación y Configuración

Para configurar el entorno de desarrollo y ejecutar el proyecto, siga los siguientes pasos:

1. **Clonar el Repositorio:**
   ```bash
   git clone https://github.com/Venuz25/finetunig-tutorProgramacion.git
   cd finetunig-tutorProgramacion
   ```

2. **Crear y Activar un Entorno Virtual:**
   ```bash
   python -m venv venv
   source venv/bin/activate  # En Windows utilice: venv\Scripts\activate
   ```

3. **Instalar Dependencias:**
   ```bash
   pip install -r requirements.txt
   ```

4. **Configurar Claves de API:**
   - Copie el archivo de ejemplo para las variables de entorno:
     ```bash
     cp .env.example .env
     ```
   - Edite el archivo `.env` y sustituya los valores de ejemplo por sus claves reales de la API de Google Gemini.

5. **Configuración del Modelo Base (Opcional):**
   Si desea utilizar un modelo base distinto al predeterminado, modifique las variables `MODEL_ID` o `BASE_MODEL` en los scripts de entrenamiento (`03_train_qlora.py`, `train_unsloth.py`) y de prueba (`04_testModel.py`).

---

## Guía de Uso

El flujo de trabajo del proyecto está estructurado en scripts secuenciales para la generación de datos, entrenamiento y evaluación.

### 1. Extracción del Dataset Base (`00_extraer_dataset.py`)
Descarga y filtra ejemplos de datasets públicos (como CodeFeedback y Alpaca) para conformar un conjunto inicial de *prompts*.
```bash
python 00_extraer_dataset.py
```

### 2. Generación del Dataset de Conversación (`01_generate_dataset.py`)
Utiliza las claves de API de Gemini configuradas para generar conversaciones sintéticas entre tutor y alumno, basándose en los *prompts* extraídos y los escenarios pedagógicos definidos.
```bash
python 01_generate_dataset.py
```

### 3. Verificación y Limpieza del Dataset (`02_verificar_dataset.py`)
Sanitiza el dataset generado, eliminando registros corruptos, vacíos o que excedan los límites de longitud, garantizando la calidad de los datos para el entrenamiento.
```bash
python 02_verificar_dataset.py
```

### 4. Fine-Tuning del Modelo (`03_train_qlora.py` y `train_unsloth.py`)
- **`03_train_qlora.py`:** Ejecuta el ajuste fino mediante QLoRA utilizando `transformers` y `peft`. Optimizado para entornos con recursos limitados.
- **`train_unsloth.py`:** Emplea la biblioteca `unsloth` para acelerar el proceso de entrenamiento y reducir el consumo de memoria, soportando contextos más extensos.

```bash
# Ejecutar con QLoRA estándar
python 03_train_qlora.py

# Ejecutar con optimización Unsloth
python train_unsloth.py
```
Los modelos resultantes se almacenarán en el directorio `models/SHUKAKU1.0/`.

### 5. Prueba Interactiva del Tutor (`04_testModel.py`)
Inicia una interfaz de línea de comandos para interactuar con el modelo ajustado. El script implementa un enrutador agéntico que modifica el *system prompt* en función de la intención detectada en la entrada del usuario.
```bash
python 04_testModel.py
```

---

## Estructura del Proyecto

```text
finetunig-tutorProgramacion/
├── .env.example
├── README.md
├── requirements.txt
├── 00_extraer_dataset.py
├── 01_generate_dataset.py
├── 02_verificar_dataset.py
├── 03_train_qlora.py
├── train_unsloth.py
├── 04_testModel.py
├── datasets/
│   ├── datasets-filtrados/
│   └── datasets-generados/
└── models/
    └── SHUKAKU1.0/
        ├── qwen-checkpoints/
        └── qwen-tutor/
```

---
  
<div align="center">
  <strong>© 2026 | <a href="https://github.com/Venuz25">Areli Guevara</strong>
</div>
