# 📱 Sentinel: Ecosistema de Clasificación de Intenciones (T-Moviles Argentina)

Este documento describe el flujo de trabajo (**Workflow**) integral para el procesamiento de lenguaje natural aplicado a la atención al cliente de **Movistar**. El ecosistema está diseñado bajo principios de **MLOps**, asegurando que el modelo sea reproducible, optimizado y escalable.

---

### 🗺️ Mapa del Flujo de Trabajo

El proceso se divide en cuatro etapas fundamentales que transforman datos crudos en un activo de inteligencia listo para ser consumido por el negocio.



---

### 📦 Etapa 1: Generación y Simulación de Datos
**Archivo:** `00-Generacion-Dataset.ipynb`

* **Finalidad:** Crear un entorno de entrenamiento controlado que simule la realidad operativa de la compañía.
* **Acciones Clave:**
    * Definición de las **4 categorías críticas**: *Falla Técnica, Churn (Baja), Feedback Positivo y Consultas Administrativas*.
    * Generación de un corpus sintético con lenguaje local (jerga argentina y referencias geográficas).
    * Balanceo de clases para asegurar que el modelo aprenda a identificar todas las intenciones por igual.

### 🧠 Etapa 2: Entrenamiento y Validación (Fine-Tuning)
**Archivo:** `01-Entrenamiento-BETO.ipynb`

* **Finalidad:** Especializar el modelo de lenguaje **BETO** (Spanish BERT) en el dominio específico de las telecomunicaciones.
* **Acciones Clave:**
    * **Tokenización:** Conversión del lenguaje humano a tensores numéricos procesables.
    * **Transfer Learning:** Ajuste de pesos del modelo pre-entrenado utilizando el dataset generado.
    * **Evaluación Científica:** Generación de la **Matriz de Confusión** y reportes de *Precision/Recall* para auditar la calidad de la IA.

### ⚡ Etapa 3: Optimización de Arquitectura (ONNX)
**Archivo:** `02-Convertir-a-ONNX.ipynb`

* **Finalidad:** Preparar el modelo para el alto desempeño en producción, reduciendo la latencia y el consumo de recursos.
* **Acciones Clave:**
    * **Exportación de Grafo:** Traducción del modelo de PyTorch a formato **ONNX**.
    * **Eficiencia:** Optimización de la velocidad de respuesta, permitiendo inferencias en milisegundos para integraciones con bots o dashboards en tiempo real.

### ☁️ Etapa 4: Gestión de Persistencia y Model Registry
**Archivo:** `03-Subida-MinIO.ipynb`

* **Finalidad:** Centralizar el modelo y permitir el versionado histórico en el almacenamiento de objetos.
* **Acciones Clave:**
    * **Versionado Dinámico:** Lógica automática que detecta versiones previas en el bucket y asigna una nueva etiqueta (ej: `v1`, `v2`, `v3`).
    * **Almacenamiento en S3 (MinIO):** Persistencia de los artefactos (`model.onnx`, `tokenizer.json`) en una ruta segura y accesible para el servidor de modelos.

---

### 🛠️ Cuadro de Estado del Proyecto

| Fase | Estado | Salida Técnica |
| :--- | :--- | :--- |
| **Ingesta** | ✅ Completado | `t-moviles_dataset.csv` |
| **Entrenamiento** | ✅ Completado | `pytorch_model.bin` |
| **Optimización** | ✅ Completado | `model.onnx` |
| **Persistencia** | ✅ Completado | `vX/` en MinIO |
| **Despliegue** | ⚙️ Manual | Endpoint REST (KServe) |

---

> **💡 Nota Operativa:** > Este ecosistema permite que cualquier actualización en el dataset se traduzca automáticamente en un nuevo modelo optimizado tras ejecutar los notebooks secuencialmente. El despliegue final se realiza de forma manual actualizando el **InferenceService** para que apunte a la ruta de la última versión generada.