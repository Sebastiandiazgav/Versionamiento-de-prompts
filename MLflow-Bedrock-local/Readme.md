# 🧪 Tutorial: Versionamiento de Prompts con MLflow GenAI

Este tutorial te guiará paso a paso para configurar un entorno local y utilizar las capacidades de `mlflow.genai` para registrar, versionar y gestionar prompts de manera profesional.

---

## 🧱 Paso 1: Configuración del Entorno Local

### 1.1 Crear Directorios

Abre tu terminal o línea de comandos y ejecuta los siguientes comandos para crear y navegar a la carpeta del proyecto:

```bash
mkdir prototipos
cd prototipos
mkdir prototipo_1_mlflow
cd prototipo_1_mlflow
```

### 1.2 Crear Entorno Virtual

Crearemos un entorno virtual para mantener nuestras dependencias aisladas.

```bash
# Crear el entorno
python -m venv venv

# Activar el entorno (Windows)
venv\Scripts\activate

# Activar el entorno (Linux/macOS)
source venv/bin/activate
```

### 1.3 Instalar MLflow

Con el entorno virtual activado, instala la librería de MLflow:

```bash
pip install mlflow
```

### 1.4 Iniciar la Interfaz de MLflow

Necesitarás dos ventanas de terminal abiertas dentro de la carpeta `prototipo_1_mlflow` y con el entorno virtual activado.

En la primera terminal, inicia la interfaz de usuario de MLflow (este servidor se mantendrá corriendo):

```bash
mlflow ui
```

En la segunda terminal, realizaremos el resto de los comandos.

---

## ✍️ Paso 2: Registrar las Versiones del Prompt

### 2.1 Crear el Script de Registro

Crea un nuevo archivo llamado `registrar_prompts.py` dentro de la carpeta `prototipo_1_mlflow`.

### 2.2 Registrar la Versión 1

Copia y pega el siguiente código en tu archivo `registrar_prompts.py`:

```python
# registrar_prompts.py

import mlflow

mlflow.set_tracking_uri("http://127.0.0.1:5000")

prompt_name = "ticket_abierto"

template_v1 = """
Basado en el siguiente ticket de soporte, resume el problema del cliente en una sola frase.
Ticket: {{ticket_text}}
Resumen:
"""

mlflow.genai.register_prompt(
    name=prompt_name,
    template=template_v1,
    commit_message="Versión 1: Resumen inicial en una frase."
)

print(f"Prompt '{prompt_name}' Versión 1 registrado exitosamente.")
```

Ejecuta el script:

```bash
python registrar_prompts.py
```

Verificación: Abre tu navegador y ve a `http://127.0.0.1:5000`. Deberías ver el prompt `ticket_abierto` registrado.

### 2.3 Registrar la Versión 2

Reemplaza el contenido de `registrar_prompts.py` por el siguiente:

```python
# registrar_prompts.py

import mlflow

mlflow.set_tracking_uri("http://127.0.0.1:5000")
prompt_name = "ticket_abierto"

template_v2 = """
Explica el problema de este ticket de soporte en un lenguaje sencillo que cualquiera pueda entender. Evita la jerga técnica.
Ticket: {{ticket_text}}
Explicación:
"""

mlflow.genai.register_prompt(
    name=prompt_name,
    template=template_v2,
    commit_message="Versión 2: Simplificación del lenguaje para audiencias no técnicas."
)

print(f"Prompt '{prompt_name}' Versión 2 registrado exitosamente.")
```

Ejecuta el script:

```bash
python registrar_prompts.py
```

Verificación: En la UI de MLflow, haz clic en `ticket_abierto`. Verás que ahora existen dos versiones. Puedes seleccionarlas y darle al botón **Compare** para ver las diferencias.

### 2.4 Registrar la Versión 3

Reemplaza nuevamente el contenido de `registrar_prompts.py` con el siguiente código:

```python
# registrar_prompts.py

import mlflow

mlflow.set_tracking_uri("http://127.0.0.1:5000")
prompt_name = "ticket_abierto"

template_v3 = """
Analiza el siguiente ticket de soporte y extrae la siguiente información:
- Cliente Afectado: (Nombre del cliente si se menciona)
- Problema Principal: (Describe el problema en 10 palabras o menos)
- Sentimiento del Cliente: (Positivo, Negativo o Neutral)

Ticket: {{ticket_text}}
"""

mlflow.genai.register_prompt(
    name=prompt_name,
    template=template_v3,
    commit_message="Versión 3: Extracción estructurada de datos."
)

print(f"Prompt '{prompt_name}' Versión 3 registrado exitosamente.")
```

Ejecuta el script:

```bash
python registrar_prompts.py
```

Ahora tendrás 3 versiones del mismo prompt registradas en MLflow.

---

## 🚀 Paso 3: Usar los Prompts en una Aplicación

### 3.1 Asignar Alias en la Interfaz de MLflow

La gestión de versiones en producción se realiza mediante **alias**, que son etiquetas que apuntan a una versión específica.

1. Ve a la página de MLflow: `http://127.0.0.1:5000`
2. En el menú izquierdo, entra en **Prompts**
3. Haz clic en `ticket_abierto`
4. En la columna **Aliases**, edita con el ícono ✏️:
   - Versión 3: asígnale el alias `production`
   - Versión 2: asígnale el alias `staging`
   - Versión 1: déjala sin alias

### 3.2 Crear y Ejecutar el Script de Uso

Crea un nuevo archivo llamado `usar_prompt.py` con el siguiente contenido:

```python
# usar_prompt.py

import mlflow

mlflow.set_tracking_uri("http://127.0.0.1:5000")

ticket_de_ejemplo = {
    "ticket_text": "Hola, soy Juan Pérez. Mi conexión a internet no funciona desde esta mañana."
                   " He reiniciado el router como me indicaron pero sigue sin funcionar. Estoy bastante molesto."
                   " Mi número de cliente es 84321."
}

print("--- Cargando prompt de PRODUCTION ---")
production_prompt = mlflow.genai.load_prompt("prompts:/ticket_abierto@production")
formatted_prompt = production_prompt.format(**ticket_de_ejemplo)
print(formatted_prompt)

print("\n--- Cargando prompt de STAGING para pruebas A/B ---")
staging_prompt = mlflow.genai.load_prompt("prompts:/ticket_abierto@staging")
formatted_staging_prompt = staging_prompt.format(**ticket_de_ejemplo)
print(formatted_staging_prompt)
```

Ejecuta el script:

```bash
python usar_prompt.py
```

Verás cómo el script imprime dos prompts diferentes (V3 y V2) sin cambiar el código fuente.

💡 **Tip**: Si el equipo de MLOps cambia el alias `production` de la versión 3 a la 2 en la UI de MLflow, la salida del script cambiará automáticamente.

---

## ✅ Resultado Final

- ✅ Se registraron **3 versiones** del prompt `ticket_abierto`.
- ✅ Se configuraron **alias** para facilitar despliegues sin modificar código.
- ✅ Se demostró cómo **cargar dinámicamente prompts** usando los alias `production` y `staging`.

---
