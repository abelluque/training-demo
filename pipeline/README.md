# 📱 Sentinel: Sistema de Clasificación de Intenciones (T-Moviles Arg)

**Sentinel** es una solución de MLOps diseñada para **Movistar Argentina** que automatiza la clasificación de motivos de contacto de clientes. Utiliza procesamiento de lenguaje natural (NLP) basado en **BETO (BERT en Español)**, optimizado con **ONNX** y desplegado sobre la infraestructura de **OpenShift AI**.

---

## 🗺️ Arquitectura del Pipeline

El proyecto se divide en 4 etapas automatizadas a través de notebooks, garantizando la reproducibilidad y el versionado del modelo.



---

## 📂 Descripción de las Etapas

### 📦 0. Ingesta y Persistencia (Data Ingestion)
**Archivo:** `00-ingesta-datos.ipynb`
* **Objetivo:** Preparar el dataset sintético con jerga local argentina y subirlo al Data Lake interno.
* **Tecnologías:** `Pandas`, `Boto3`, `MinIO`.
* **Resultado:** Archivo `t-moviles_dataset.csv` disponible en el bucket `training-data`.

### 🧠 1. Entrenamiento y Fine-Tuning
**Archivo:** `01-entrenamiento-beto.ipynb`
* **Objetivo:** Especializar el modelo base `dccuchile/bert-base-spanish-wwm-cased` en las 4 categorías de Sentinel (Técnico, Churn, Positivo, Administrativo).
* **Tecnologías:** `Hugging Face Transformers`, `PyTorch`, `Accelerate`.
* **Resultado:** Modelo entrenado empaquetado en `modelo_pytorch.tar.gz`.

### ⚡ 2. Optimización Técnica (ONNX)
**Archivo:** `02-optimizacion-onnx.ipynb`
* **Objetivo:** Convertir el modelo de PyTorch a formato ONNX para reducir la latencia de respuesta y mejorar el rendimiento en el clúster.
* **Tecnologías:** `Optimum`, `ONNX Runtime`.
* **Resultado:** Artefacto optimizado en `modelo_onnx.tar.gz`.

### ☁️ 3. Registro y Versionado (Model Registry)
**Archivo:** `03-subida-minio.ipynb`
* **Objetivo:** Implementar una lógica de despliegue no destructivo mediante versionado automático (`v1`, `v2`, etc.).
* **Tecnologías:** `Boto3`, `Regex`.
* **Resultado:** Modelo persistido en MinIO y generación del archivo de control `deployed_version.txt`.

---

## 📊 Categorías de Clasificación

| ID | Intención | Descripción |
| :--- | :--- | :--- |
| **0** | **Falla Técnica** | Reclamos de internet, señal móvil o fallas de fibra. |
| **1** | **Churn (Baja)** | Clientes con intención de portabilidad o rescisión de contrato. |
| **2** | **Feedback Positivo** | Agradecimientos y buenas experiencias en canales de atención. |
| **3** | **Administrativo** | Consultas de facturación, precios y gestiones de cuenta. |

---

## 🛠️ Tecnologías Utilizadas

* **Plataforma:** OpenShift AI (RHOAI)
* **Orquestación:** Elyra Pipelines
* **Modelo Base:** BETO (Spanish BERT)
* **Inferencia:** KServe / ModelMesh (ONNX Runtime)
* **Storage:** MinIO (S3 Compatible)
* **Frontend:** Streamlit (Dashboard Sentinel)

---

## 🚀 Despliegue Manual (Instrucciones)

Una vez completado el pipeline de notebooks, el modelo se despliega en producción siguiendo estos pasos:

1. **Actualizar el InferenceService:**
   Apuntar el `storageUri` del recurso KServe a la última versión generada en MinIO (ej: `s3://modelos-prod/v5/`).
2. **Reiniciar el Pod de la API:**
   Asegurarse de que el microservicio FastAPI apunte al nuevo endpoint del modelo.
3. **Validar Dashboard:**
   Verificar la conexión en la URL de la Route de Streamlit.

---
> **Proyecto:** Sentinel - T-Moviles AI  
> **Entorno:** OpenShift Sandbox / Production  
> **Status:** ✅ Pipeline Funcional