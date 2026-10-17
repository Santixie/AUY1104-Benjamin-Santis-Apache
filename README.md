# AUY1104 · Apache en contenedor con CI/CD

Servidor web **Apache** empaquetado en Docker, con pipeline de despliegue en **GitHub Actions** y manifiestos de **Kubernetes** (K3s en AWS EC2). Proyecto académico de AUY1104 (Duoc UC).

## Contenido

| Ruta | Descripción |
|---|---|
| `Dockerfile` | Imagen basada en `httpd:alpine` que copia el sitio estático a `/usr/local/apache2/htdocs/` |
| `public-html/` | Sitio web que sirve el contenedor |
| `k8s/deployment.yaml` | Deployment `apache-web` (puerto 80, con readiness y liveness probes) |
| `k8s/service.yaml` | Service que expone el Deployment |
| `.github/workflows/deploy.yaml` | Pipeline `Publicar Apache y desplegar en K3S` |

## Pipeline

Se dispara al crear un tag con formato `vX.Y.Z` (por ejemplo `v1.0.0`) o a mano con `workflow_dispatch`.

1. **Build and push:** construye la imagen y la publica en Docker Hub con el tag de la versión y con `latest`.
2. **Deploy:** se conecta por SSH al servidor K3s, copia los manifiestos de `k8s/`, los aplica con `kubectl apply` y verifica los pods.

## Ejecutar localmente

```bash
docker build -t apache-demo .
docker run --rm -p 8080:80 apache-demo
```

Abrir http://localhost:8080

## Despliegue manual en Kubernetes

```bash
kubectl apply -f k8s/
```

## Configuración requerida en el repositorio

| Tipo | Nombre |
|---|---|
| Secret | `DOCKER_USERNAME` |
| Secret | `DOCKER_PASSWORD` |
| Secret | `EA2_SSH_PRIVATE_KEY` |
| Variable | `K3S_SERVER_PUBLIC_IP` |
