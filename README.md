# 🚀 Rust GitHub API

Una API REST moderna construida en **Rust** con **Actix-web** que consume la **GitHub GraphQL API** para recuperar información detallada sobre usuarios, repositorios y organizaciones de GitHub.

## ✨ Características

- 🔐 **Autenticación GitHub**: Soporta tanto consultas públicas como con token GitHub
- 📊 **Información de Usuarios**: Login, perfil, estadísticas y contribuciones
- 📁 **Repositorios**: Acceso a repos con detalles de lenguaje, estrellas, forks y más
- 🏢 **Organizaciones**: Listado de organizaciones con información completa
- ⚡ **GraphQL**: Consultas optimizadas a la GitHub GraphQL API
- 🌐 **CORS Habilitado**: Permite peticiones desde cualquier origen
- 🐳 **Docker Ready**: Incluye Dockerfile para despliegue en contenedores
- 🚂 **Railway Listo**: Configurado para desplegar en Railway con `railway.toml`
- 📱 **UI Incluida**: Frontend HTML interactivo para probar los endpoints

## 📋 Requisitos

- **Rust 1.56+** (edición 2021)
- **Cargo** (gestor de paquetes de Rust)
- (Opcional) **GitHub Token** para información extendida

## 🛠️ Instalación

### 1. Clonar el repositorio

```bash
git clone https://github.com/iseu-dev-git/rust-github-api.git
cd rust-github-api
```

### 2. Configurar variables de entorno

Crea un archivo `.env` en la raíz del proyecto:

```env
GITHUB_TOKEN=tu_token_aqui_opcional
PORT=8080
```

Para obtener un token GitHub:
1. Ve a [GitHub Settings > Developer settings > Personal access tokens](https://github.com/settings/tokens)
2. Haz clic en "Generate new token"
3. Selecciona los scopes necesarios (recomendado: `user`, `repo`, `read:org`)
4. Copia el token en tu archivo `.env`

### 3. Compilar y ejecutar

```bash
cargo run
```

El servidor estará disponible en `http://localhost:8080`

## 📚 Endpoints API

### Status

```http
GET /api/v1/github/status
```

Verifica el estado de la API y si el token GitHub está configurado.

**Respuesta:**
```json
{
  "status": "success",
  "data": {
    "api": "GitHub GraphQL API",
    "graphql_disponible": true,
    "token_configurado": true,
    "mensaje": "Token configurado - Información extendida disponible"
  }
}
```

### Obtener Usuario

```http
GET /api/v1/github/users/{username}
```

Obtiene información de un usuario GitHub específico.

**Ejemplo:**
```bash
curl http://localhost:8080/api/v1/github/users/torvalds
```

**Respuesta:**
```json
{
  "status": "success",
  "data": {
    "username": "torvalds",
    "perfil": {
      "login": "torvalds",
      "id": "MDQ6VXNlcjMyNDQw",
      "avatar_url": "https://avatars.githubusercontent.com/u/3244?v=4",
      "html_url": "https://github.com/torvalds",
      "name": "Linus Torvalds",
      "public_repos": 12,
      "followers": 250000,
      "following": 0,
      "created_at": "2005-01-16T01:58:52Z",
      "updated_at": "2026-04-24T15:30:00Z",
      "total_commits": 1000000,
      "total_prs": 50,
      "total_issues": 100
    },
    "info_extendida": true
  }
}
```

### Listar Repositorios

```http
GET /api/v1/github/users/{username}/repos
```

Obtiene todos los repositorios de un usuario (máximo 100).

**Ejemplo:**
```bash
curl http://localhost:8080/api/v1/github/users/torvalds/repos
```

**Respuesta:**
```json
{
  "status": "success",
  "data": {
    "username": "torvalds",
    "total": 12,
    "repositorios": [
      {
        "name": "linux",
        "description": "Linux kernel source tree",
        "html_url": "https://github.com/torvalds/linux",
        "language": "C",
        "language_color": "#555555",
        "stargazers_count": 180000,
        "forks_count": 25000,
        "created_at": "2005-01-16T02:54:33Z"
      }
    ]
  }
}
```

### Listar Organizaciones

```http
GET /api/v1/github/users/{username}/orgs
```

Obtiene las organizaciones a las que pertenece un usuario.

**Ejemplo:**
```bash
curl http://localhost:8080/api/v1/github/users/torvalds/orgs
```

### Saludar (Demo)

```http
GET /api/v1/saludar?nombre=XXX
```

Endpoint de demostración.

**Ejemplo:**
```bash
curl "http://localhost:8080/api/v1/saludar?nombre=Max"
```

## 🐳 Docker

### Construir la imagen

```bash
docker build -t rust-github-api .
```

### Ejecutar el contenedor

```bash
docker run -p 8080:8080 -e GITHUB_TOKEN=tu_token rust-github-api
```

## 🚂 Despliegue en Railway

Este proyecto está configurado para desplegar fácilmente en [Railway](https://railway.app):

1. Conecta tu repositorio a Railway
2. Railway detectará automáticamente que es un proyecto Rust
3. Configura la variable de entorno `GITHUB_TOKEN` en Railway
4. ¡Hecho! Tu API estará en línea

## 📦 Dependencias

```toml
actix-web = "4"        # Framework web
actix-cors = "0.7"     # CORS middleware
serde = "1.0"          # Serialización/deserialización
serde_json = "1.0"     # JSON support
chrono = "0.4"         # Manipulación de fechas
reqwest = "0.12"       # Cliente HTTP
tokio = "1"            # Runtime async
dotenv = "0.15"        # Cargar variables de entorno
lazy_static = "1.4"    # Variables estáticas lazy
```

## 🔄 Flujo de Datos

```
┌─────────────────┐
│  Cliente REST   │
└────────┬────────┘
         │ HTTP Request
         ▼
┌─────────────────────┐
│  Actix Web Server   │
│   (Port 8080)       │
└────────┬────────────┘
         │ Valida & Prepara Query
         ▼
┌─────────────────────┐
│ GraphQL Client      │ + GITHUB_TOKEN (si existe)
│  (reqwest)          │
└────────┬────────────┘
         │ GraphQL Query
         ▼
┌─────────────────────────┐
│ GitHub GraphQL API      │
│ (api.github.com/graphql)│
└────────┬────────────────┘
         │ Datos JSON
         ▼
┌─────────────────────┐
│ Response Builder    │
│ (Format JSON)       │
└────────┬────────────┘
         │ JSON Response
         ▼
┌─────────────────┐
│  Cliente REST   │
└─────────────────┘
```

## 🎯 Casos de Uso

- 📊 Dashboards de análisis GitHub
- 🤖 Bots que consumen datos de GitHub
- 📈 Herramientas de estadísticas de developers
- 🔍 Integración con sistemas de terceros
- 📱 Aplicaciones móviles que requieren datos de GitHub

## 🔒 Seguridad

- ✅ El token GitHub se lee solo de variables de entorno
- ✅ No se logea información sensible
- ✅ CORS configurado para múltiples orígenes
- ⚠️ En producción, usa HTTPS siempre

## 📝 Estructura del Proyecto

```
rust-github-api/
├── src/
│   └── main.rs          # Código principal de la API
├── Cargo.toml           # Dependencias y configuración
├── Cargo.lock           # Lock file de dependencias
├── Dockerfile           # Configuración Docker
├── railway.toml         # Configuración Railway
├── index.html           # Frontend UI
├── .gitignore           # Archivos ignorados
└── README.md            # Este archivo
```

## 🚀 Próximas Características

- [ ] Búsqueda avanzada de repositorios
- [ ] Análisis de contribuciones detallado
- [ ] Cache de respuestas
- [ ] Rate limiting
- [ ] Autenticación OAuth de usuarios
- [ ] Base de datos para historial

## 📄 Licencia

Este proyecto está bajo licencia **MIT**. Ver archivo `LICENSE` para más detalles.



---

**Hecho con Rust**
