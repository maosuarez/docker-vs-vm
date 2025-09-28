# Despliegue de FastAPI con Nginx en Ubuntu

Este documento describe paso a paso cómo configurar y desplegar una aplicación **FastAPI** detrás de **Nginx** en una máquina virtual con Ubuntu.

---

## 1. Instalar dependencias

Primero, actualiza los paquetes e instala Nginx y Python con venv:

```bash
sudo apt update
sudo apt install -y python3-venv python3-pip nginx
```

- `apt update`: actualiza la lista de paquetes disponibles.  
- `apt install`: instala los paquetes necesarios (venv, pip y nginx).  

---

## 2. Crear el entorno de la aplicación

Crea una carpeta para tu proyecto y un entorno virtual:

```bash
mkdir ~/fastapi-vm-poc
cd ~/fastapi-vm-poc
python3 -m venv venv
source venv/bin/activate
```

- `mkdir`: crea el directorio del proyecto.  
- `python3 -m venv venv`: crea un entorno virtual aislado.  
- `source venv/bin/activate`: activa el entorno virtual.  

---

## 3. Instalar FastAPI y Uvicorn

```bash
pip install fastapi uvicorn
```

- `pip install`: instala FastAPI y Uvicorn dentro del entorno virtual.  

---

## 4. Crear la aplicación FastAPI

Crea el archivo `app/main.py` con este contenido:

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello from FastAPI on Nginx!"}
```

---

## 5. Probar la aplicación

Ejecuta el servidor en el puerto 8081:

```bash
uvicorn app.main:app --host 0.0.0.0 --port 8081
```

- `uvicorn app.main:app`: indica el archivo y la instancia de la aplicación.  
- `--host 0.0.0.0`: expone el servicio a todas las interfaces de red.  
- `--port 8081`: define el puerto de escucha.  

---

## 6. Configurar Nginx

Crea un archivo de configuración para Nginx:

```bash
sudo nano /etc/nginx/sites-available/fastapi
```

Con el siguiente contenido:

```nginx
server {
    listen 80;

    server_name _;

    location / {
        proxy_pass http://127.0.0.1:8081;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

---

## 7. Habilitar el sitio en Nginx

```bash
sudo ln -s /etc/nginx/sites-available/fastapi /etc/nginx/sites-enabled/
sudo unlink /etc/nginx/sites-enabled/default
sudo nginx -t
sudo systemctl restart nginx
```

- `ln -s`: crea un enlace simbólico para habilitar la configuración.  
- `nginx -t`: verifica la configuración de Nginx.  
- `systemctl restart nginx`: reinicia el servicio Nginx.  

---

## 8. Acceder a la aplicación

Abre en el navegador:

```
http://<IP_DE_TU_VM>/api/hello
```

Y deberías ver:

```json
{"message": "Hello from FastAPI on Nginx!"}
```

---
