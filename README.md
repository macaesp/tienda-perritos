# 🐾 Tienda de Alimentos para Perritos - Innovatech Chile

Aplicación web de gestión de productos para una tienda de alimentos para perros, desplegada en contenedores Docker sobre instancias AWS EC2 con pipeline CI/CD automatizado mediante GitHub Actions.

---

## 📁 Estructura del Repositorio

```
tienda-perritos/
├── .github/
│   └── workflows/
│       ├── cicd-tienda-frontend.yml
│       ├── cicd-tienda-backend.yml
│       └── cicd-tienda-db.yml
├── frontend/
│   ├── Dockerfile
│   ├── index.html
│   ├── app.js
│   └── default.conf
├── backend/
│   ├── Dockerfile
│   ├── server.js
│   └── package.json
├── db/
│   ├── Dockerfile
│   └── init.sql
├── docker-compose.yml
└── README.md
```

---

## 🛠️ Tecnologías Utilizadas

| Componente | Tecnología |
|-----------|-----------|
| Frontend | HTML + JavaScript + Nginx |
| Backend | Node.js + Express |
| Base de datos | MySQL 8 |
| Contenedores | Docker + Docker Compose |
| Registro de imágenes | Amazon ECR + Docker Hub |
| CI/CD | GitHub Actions |
| Infraestructura | AWS EC2 |

---

## 🐳 Contenedorización

### Frontend
- Imagen base: `nginx:alpine`
- Sirve los archivos estáticos HTML y JavaScript
- Puerto expuesto: `80`
- Configuración personalizada de Nginx mediante `default.conf`

### Backend
- Imagen base: `node:18-alpine`
- API REST con Express y conexión a MySQL
- Puerto expuesto: `3001`
- Variables de entorno para conexión a la base de datos

### Base de datos
- Imagen base: `mysql:8`
- Inicialización automática con `init.sql`
- Puerto expuesto: `3306`
- Persistencia mediante volumen Docker named `tienda_db_data`

---

## 🚀 Cómo ejecutar el proyecto

### Requisitos previos
- Docker Desktop instalado
- Docker Compose instalado
- Git instalado

### Pasos

1. Clonar el repositorio:
```bash
git clone https://github.com/macaesp/tienda-perritos.git
cd tienda-perritos
```

2. Levantar todos los servicios:
```bash
docker-compose up -d
```

3. Verificar que los contenedores estén corriendo:
```bash
docker ps
```

4. Acceder a la aplicación desde el navegador:
```
http://localhost
```

5. Para detener los servicios:
```bash
docker-compose down
```

---

## 💾 Persistencia de Datos

Se utiliza un **named volume** llamado `tienda_db_data` para la base de datos MySQL. Esta elección asegura que los datos no se pierdan al reiniciar o eliminar el contenedor, a diferencia de un bind mount que depende de la ruta del sistema host.

```yaml
volumes:
  db_data:
    name: tienda_db_data
```

---

## ⚙️ Pipeline CI/CD

El proyecto cuenta con tres workflows de GitHub Actions, uno por servicio:

| Workflow | Archivo | Trigger |
|---------|---------|---------|
| Frontend | `cicd-tienda-frontend.yml` | Push en `main` con cambios en `frontend/**` |
| Backend | `cicd-tienda-backend.yml` | Push en `main` con cambios en `backend/**` |
| DB | `cicd-tienda-db.yml` | Push en `main` con cambios en `db/**` |

### Flujo del pipeline:
1. **Checkout** del código fuente
2. **Configurar credenciales AWS** mediante GitHub Secrets
3. **Login en Amazon ECR**
4. **Build** de la imagen Docker
5. **Push** de la imagen a ECR
6. **Deploy** automático en la instancia EC2 correspondiente mediante AWS SSM

### GitHub Secrets configurados:
- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `AWS_SESSION_TOKEN`
- `AWS_REGION`
- `ECR_REGISTRY`
- `ECR_REPO_URL_FRONTEND`
- `ECR_REPO_URL_BACKEND`
- `ECR_REPO_URL_DB`
- `EC2_FRONTEND_INSTANCE_ID`
- `EC2_BACKEND_INSTANCE_ID`
- `EC2_DB_INSTANCE_ID`

---

## 🌐 Infraestructura AWS

| Instancia | Rol | Puerto |
|----------|-----|--------|
| EC2 Frontend | Sirve la interfaz web | 80 (público) |
| EC2 Backend | API REST | 3001 (privado) |
| EC2 DB | Base de datos MySQL | 3306 (privado) |

Solo el **Frontend** es accesible desde Internet. El Backend y la DB se encuentran en subredes privadas, accesibles únicamente desde dentro de la VPC según las reglas de los Security Groups.

---

## 👩‍💻 Autora

**Macarena Espinoza**  
Evaluación Parcial N°2 - Introducción a Herramientas DevOps  
DuocUC - IHDP-001D  
Mayo 2026
