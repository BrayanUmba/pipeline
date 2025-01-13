# Pipeline CI/CD con ArgoCD

## Estructura del Proyecto
```
pipeline/
├── .github/
│   └── workflows/
│       └── ci-cd.yaml        
├── k8s/
│   └── app.yaml             
└── Dockerfile               
└── README.md
```

## Requisitos Previos
- Docker
- Kubernetes (Minikube)
- kubectl
- Cuenta en GitHub
- Acceso a la imagen Docker: brayanumba/my-app:latest

## Configuración Rápida

### 1. Despliegue Local
```bash
# Iniciar Minikube
minikube start --driver=docker

# Instalar ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 2. Acceder a ArgoCD
```bash
# Exponer ArgoCD
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Acceder vía: http://localhost:8080
```

## Pipeline CI/CD

### Etapas del Pipeline

1. **Build**:
   - Descarga la imagen Docker preexistente
   - Ejecuta la imagen en puerto 3000

2. **Deploy**:
   - Configura Minikube
   - Instala ArgoCD
   - Despliega la aplicación

### Configuración de ArgoCD

La aplicación se despliega con las siguientes características:
- Namespace: default
- Repositorio fuente: https://github.com/BrayanUmba/pipeline.git
- Sincronización automática activada
- Auto-sanación activada

## Verificación del Despliegue

```bash
# Verificar estado de ArgoCD
kubectl get all -n argocd

# Verificar aplicación
kubectl get all -n default
```

## Solución de Problemas

### Verificar Componentes
```bash
# Estado de ArgoCD
kubectl get pods -n argocd

# Logs de la aplicación
kubectl logs deployment/frontend-app -n default

# Estado de servicios
kubectl get svc -n default
```

## Mantenimiento

### Actualizaciones
1. Subir cambios al repositorio
2. ArgoCD sincronizará automáticamente

### Monitoreo
```bash
# Ver estado de sincronización
kubectl get applications -n argocd

# Ver eventos
kubectl get events -n default
```

## Seguridad

Consideraciones importantes:
1. Cambiar credenciales por defecto de ArgoCD
2. Configurar HTTPS para acceso a ArgoCD
3. Implementar políticas de red
4. Configurar RBAC apropiado

## Rollback

En caso de fallos:
```bash
# Ver historial de revisiones
kubectl rollout history deployment/frontend-app -n default

# Realizar rollback
kubectl rollout undo deployment/frontend-app -n default
```