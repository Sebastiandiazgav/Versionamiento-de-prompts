# Prototipo de Versionamiento de Prompts con MLflow y Bedrock en SageMaker

Este repositorio contiene un **prototipo de un sistema de MLOps** para gestionar y versionar prompts de modelos de lenguaje (LLMs), utilizando **Amazon Bedrock**, **MLflow** y **Amazon SageMaker Studio**.

El objetivo es tratar los prompts como **artefactos críticos del ciclo de vida del software**, permitiendo un versionamiento robusto, trazabilidad y la capacidad de gestionar diferentes versiones en entornos de prueba y producción.

---

## 📋 Prerrequisitos

Antes de comenzar, asegúrate de contar con lo siguiente:

- ✅ **Cuenta de AWS** con acceso a la consola.
- 🌎 **Región de AWS**: Validado en `us-east-2` (Ohio). Todos los recursos deben estar en la misma región.
- 🔐 **Permisos IAM** adecuados para:
  - Amazon SageMaker
  - Amazon Bedrock
  - AWS IAM (para roles)
  - Amazon S3

---

## 🛠️ Paso 1: Configuración del Entorno en AWS

### 1.1. Crear un Bucket en S3

MLflow utiliza un bucket para almacenar artefactos como los prompts.

1. Ve a [Amazon S3](https://s3.console.aws.amazon.com/s3/home).
2. Clic en **"Crear bucket"**.
3. Usa un nombre único, por ejemplo: `mlflow-sagemaker-artifacts-tu-nombre-12345`
4. Región: `us-east-2`.
5. Mantén las demás opciones por defecto y crea el bucket.

### 1.2. Crear un Tracker de MLflow en SageMaker

1. Ve a [Amazon SageMaker Studio](https://console.aws.amazon.com/sagemaker/).
2. Abre Studio y selecciona el ícono de **MLflow**.
3. Clic en **"Create MLflow tracker"**.
4. Asigna un nombre (ej. `PruebaMlflow`).
5. En `Artifact storage location`, coloca la URI de tu bucket en formato: `s3://<nombre-de-tu-bucket>`
6. Clic en **"Create"**. La creación puede tardar 20-25 minutos.
7. Copia el **ARN del tracker**, lo necesitarás para el código en la celda 2 del notebook.

---

## 🔐 Paso 2: Configuración de Permisos en IAM

1. Ve a [IAM Roles](https://console.aws.amazon.com/iam/home#/roles).
2. Busca y selecciona el rol de ejecución de SageMaker (por lo general contiene `SageMakerExecutionRole`).
3. En la pestaña de **Permisos**, selecciona: `Add permissions → Create inline policy`.

### Política para MLflow

En la pestaña **JSON**, pega lo siguiente:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowMLflowAccess",
            "Effect": "Allow",
            "Action": "sagemaker-mlflow:*",
            "Resource": "*"
        }
    ]
}
```

Nombra la política como: `MLflow-Tracker-Full-Access`.

### Política para Bedrock

Repite el paso anterior con la siguiente política:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowBedrockInvokeModel",
            "Effect": "Allow",
            "Action": "bedrock:InvokeModel",
            "Resource": "*"
        }
    ]
}
```

Nombra la política como: `Bedrock-InvokeModel-Access`.

---

## 🚀 Paso 3: Ejecución del Prototipo en un Notebook de SageMaker

1. Abre Amazon SageMaker Studio.
2. Copia el  **Notebook** de este repositorio.
3. Asegúrate de que el kernel sea compatible con Python (por ejemplo, `Python 3 (Data Science)`).
4. En el notebook, ejecuta las celdas provistas en este repositorio (en el archivo `.ipynb`) en orden:
   - Carga de configuraciones (ARN del tracker, bucket de S3, nombre del modelo de Bedrock).
   - Definición de funciones para registrar los prompts como artefactos en MLflow.
   - Ejecución de un ejemplo simple de inferencia con Bedrock.
   - Registro del resultado y prompt en MLflow.
5. Verifica en el panel de MLflow dentro de SageMaker que los artefactos (prompts) hayan sido correctamente versionados.
