# 🚀 Tutorial: Gestión de Prompts con PromptLayer y OpenAI/Ollama

Este tutorial te guiará paso a paso para crear un entorno de trabajo con PromptLayer, OpenAI y Ollama. Aprenderás a gestionar versiones de prompts, etiquetar solicitudes y visualizar resultados en el dashboard de PromptLayer.

---

## 🧱 Paso 1: Activación del Entorno Virtual

### En Windows (usando Git Bash)
```bash
source venv/Scripts/activate
```

### En macOS o Linux
```bash
source venv/bin/activate
```

Una vez activado, verás `(venv)` al principio de la línea de tu terminal.

---

## 📦 Paso 2: Instalación de Dependencias

Con el entorno virtual activo, instala las siguientes librerías necesarias:

```bash
pip install promptlayer openai
```

---

## 🔐 Paso 3: Configuración de Claves de API

### 1. Obtener tu Clave de PromptLayer
- Ve a [promptlayer.com](https://promptlayer.com)
- Inicia sesión o regístrate
- Copia tu clave desde la sección **Settings ⚙️**

### 2. Establecer las Variables de Entorno
Ejecuta estos comandos en tu terminal:

```bash
export PROMPTLAYER_API_KEY="pl_tu_clave_de_api_aqui"
export OPENAI_API_KEY="sk_tu_clave_de_openai_aqui"
```

> ℹ️ **Nota**: La clave de OpenAI es requerida por la estructura de la librería, aunque las solicitudes se redirijan a Ollama.

---

## 🧪 Paso 4: Crear Script - Versión 1

### 1. Crear el Archivo
```bash
touch run_v1.py
```

### 2. Contenido del Script

```python
# Archivo: run_v1.py
from openai import OpenAI
from promptlayer import PromptLayer

# --- Configuración ---
promptlayer_client = PromptLayer()
client = promptlayer_client.openai.OpenAI(
    base_url='http://localhost:11434/v1',
    api_key='ollama',
)
# --------------------

print("Ejecutando Versión 1...")
response = client.chat.completions.create(
    model="llama3",
    messages=[
        {"role": "user", "content": "Compón un haiku sobre la programación local."}
    ],
    pl_tags=["experimento-ollama-v1"]
)

print("\n--- Respuesta de V1 ---")
print(response.choices[0].message.content)
print("\nSolicitud V1 registrada en PromptLayer.")
```

### 3. Ejecutar el Script
```bash
python run_v1.py
```

---

## 🧪 Paso 5: Crear Script - Versión 2

### 1. Crear el Archivo
```bash
touch run_v2.py
```

### 2. Contenido del Script

```python
# Archivo: run_v2.py
from openai import OpenAI
from promptlayer import PromptLayer

# --- Configuración ---
promptlayer_client = PromptLayer()
client = promptlayer_client.openai.OpenAI(
    base_url='http://localhost:11434/v1',
    api_key='ollama',
)
# --------------------

print("Ejecutando Versión 2...")
response = client.chat.completions.create(
    model="llama3",
    messages=[
        {"role": "user", "content": "Escribe un chiste corto de dos líneas sobre la programación."}
    ],
    pl_tags=["experimento-ollama-v2"]
)

print("\n--- Respuesta de V2 ---")
print(response.choices[0].message.content)
print("\nSolicitud V2 registrada en PromptLayer.")
```

### 3. Ejecutar el Script
```bash
python run_v2.py
```

---

## 📊 Paso 6: Verificación en el Dashboard de PromptLayer

1. Abre tu navegador y entra a [PromptLayer](https://promptlayer.com)
2. Ve a la sección **Requests** desde el menú lateral
3. Filtra por etiqueta:
    - `experimento-ollama-v1` → Resultado del haiku
    - `experimento-ollama-v2` → Resultado del chiste

---

## ✅ Resultado Final

- ✅ Se creó un entorno virtual y se instalaron dependencias
- ✅ Se configuraron las claves necesarias
- ✅ Se desarrollaron y ejecutaron dos scripts versionados
- ✅ Se visualizó y verificó cada versión en PromptLayer

