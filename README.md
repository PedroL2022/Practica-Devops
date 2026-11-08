# Plantilla DevOps — Electiva 1 (ITLA 2026-C-3)

Aplicación web mínima (lista de tareas) con todo lo necesario para practicar un pipeline **CI/CD** sin cuentas pagas ni Azure:

| Pieza | Archivo | Para qué sirve |
|---|---|---|
| App Node.js/Express | `src/app.js`, `src/server.js` | API de tareas + `/health` y `/metrics` |
| Frontend estático | `public/index.html` | Se publica en GitHub Pages |
| Pruebas automatizadas | `tests/app.test.js` | Jest + Supertest con cobertura |
| Contenedor | `Dockerfile` | Imagen de producción con *healthcheck* |
| Pipeline | `.github/workflows/ci.yml` | Lint → pruebas → build Docker → deploy a Pages |

## Requisitos (gratis)
- Cuenta de **GitHub** (usa un repositorio **público**: GitHub Actions es gratuito).
- **Git** y **Node.js 20+**. Opcional para probar el contenedor en tu PC: **Docker Desktop**.

## Ejecutar en tu computadora
```bash
npm install
npm test          # pruebas + cobertura
npm start         # http://localhost:3000
```
Endpoints: `GET /health`, `GET /metrics`, `GET/POST /api/tareas`, `PATCH /api/tareas/:id`.

Con Docker:
```bash
docker build -t tareas-devops .
docker run -p 3000:3000 tareas-devops
```

## Subirlo a GitHub y activar el pipeline
1. Crea un repositorio **público** vacío en GitHub (sin README).
2. En la carpeta de la plantilla:
   ```bash
   git init
   git add .
   git commit -m "Plantilla inicial"
   git branch -M main
   git remote add origin https://github.com/TU_USUARIO/TU_REPO.git
   git push -u origin main
   ```
3. En GitHub: **Settings → Pages → Source: GitHub Actions**.
4. Abre la pestaña **Actions**: verás el workflow *CI/CD* ejecutándose. Cuando termine, la URL de tu página aparece en el job *Despliegue a GitHub Pages*.

## Práctica 3 — qué debes agregar
- Al menos **2 pruebas nuevas** (por ejemplo: borrar tareas, validar títulos largos).
- Una funcionalidad nueva pequeña con su prueba (ej. `DELETE /api/tareas/:id`).
- Provocar a propósito una prueba fallida, mostrar que el pipeline se pone en **rojo**, corregirla y mostrarlo en **verde**.
- Completar este README con la explicación de cada etapa del pipeline.

## Proyecto Final — cómo extenderla
- Persistencia con **SQLite** (o MongoDB) en lugar del arreglo en memoria.
- Publicar la imagen en **GitHub Container Registry** (`ghcr.io`) desde el pipeline.
- Análisis estático real (ESLint) y umbral de cobertura mínima.
- Monitoreo: usar `/metrics` y los logs JSON; definir al menos una **alerta** (ej. errores > 0).
- Documentar: guía de instalación, manual de operaciones y lecciones aprendidas.

---

## Documentación del pipeline (Práctica 3)

El archivo `.github/workflows/ci.yml` define el workflow **CI/CD**. Se dispara con cada `push` y `pull request` hacia `main`.

| Etapa | Job / paso | Qué hace |
|---|---|---|
| 1. Checkout | `actions/checkout@v4` | Descarga el código del repositorio en el runner. |
| 2. Preparar Node | `actions/setup-node@v4` | Instala Node.js 20. |
| 3. Dependencias | `npm install` | Instala Express, Jest y Supertest. |
| 4. Revisión estática | `npm run lint` | Verifica la sintaxis de `src/*.js`. |
| 5. Pruebas + cobertura | `npm test` | Ejecuta pruebas unitarias y de integración (Jest + Supertest). Falla si la cobertura baja del umbral. |
| 6. Reporte | `upload-artifact` | Guarda la carpeta `coverage/` como artefacto descargable. |
| 7. Build Docker | `docker build` | Comprueba que la imagen se construye. |
| 8. Despliegue | job `deploy-pages` | Solo en `push` a `main` y si el job anterior pasó: publica `public/` en GitHub Pages. |

### Pruebas
- **Unitarias/integración**: `tests/app.test.js` prueba cada endpoint con Supertest.
- **Cobertura**: umbral global en `package.json` (80 % líneas/funciones/sentencias, 70 % ramas).

### Instrucciones de despliegue
1. Repositorio público en GitHub.
2. **Settings → Pages → Source: GitHub Actions**.
3. Haz `git push` a `main`; al terminar el job de despliegue, la URL aparece en la pestaña **Actions** y en **Settings → Pages**.

### Contribuir
Ver [CONTRIBUTING.md](CONTRIBUTING.md).

### Endpoints
`GET /health`, `GET /metrics`, `GET/POST /api/tareas`, `PATCH /api/tareas/:id`, `DELETE /api/tareas/:id` (nuevo).
