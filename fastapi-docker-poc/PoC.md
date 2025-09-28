## 🧪 Prueba de Concepto: Despliegue de API REST con FastAPI usando Docker

### 🎯 Objetivo

Desplegar una API REST básica con **FastAPI** en un contenedor Docker, demostrando rapidez, portabilidad y simplicidad frente a una instalación manual en una máquina virtual.

---

### 📁 Estructura del Proyecto

```
fastapi-docker-poc/
├── app/
│   └── main.py
├── requirements.txt
├── Dockerfile
├── docker-compose.yml
└── README.md
```

---

### 📝 Paso a Paso

#### 1. Crear el archivo `main.py` con FastAPI

```python
# app/main.py
from fastapi import FastAPI

app = FastAPI()

@app.get("/api/hello")
def read_root():
    return {"message": "Hola desde FastAPI en Docker"}
```

---

#### 2. Crear el archivo `requirements.txt`

```txt
fastapi
uvicorn
```

---

#### 3. Crear el `Dockerfile`

```Dockerfile
# Imagen base oficial de Python
FROM python:3.10-slim

# Establecer directorio de trabajo
WORKDIR /app

# Copiar archivos
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY ./app ./app

# Exponer el puerto
EXPOSE 8000

# Comando para ejecutar la API
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

---

#### 4. Crear el archivo `docker-compose.yml`

```yaml
version: '3.8'

services:
  fastapi:
    build: .
    ports:
      - "8000:8000"
    container_name: fastapi_app
```

---

#### 5. Ejecutar el proyecto

```bash
docker-compose up --build
```

Accede a http://localhost:8000/docs

Accede a la API en: http://localhost:8000/api/hello

---

### 🧠 ¿Qué hacen estos archivos?

- **`main.py`**: Define la lógica de la API con FastAPI.
- **`requirements.txt`**: Lista de dependencias necesarias.
- **`Dockerfile`**: Define cómo construir la imagen del contenedor.
- **`docker-compose.yml`**: Orquesta el despliegue del servicio con configuración de red y puertos.

---

### 📊 Comparación con Ubuntu Server (VM)

| Aspecto               | Docker + FastAPI               | VM con Ubuntu Server            |
|-----------------------|--------------------------------|---------------------------------|
| Tiempo de despliegue  | Segundos                       | Minutos                         |
| Configuración         | Automatizada                   | Manual                          |
| Portabilidad          | Alta (imagen ejecutable)       | Media (depende del hipervisor) |
| Seguridad             | Requiere hardening             | Aislamiento completo            |
| Recursos              | Bajo consumo                   | Alto consumo                    |
