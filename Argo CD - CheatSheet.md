

---

## **Conceptos Básicos de Argo CD**
- **GitOps**: Filosofía que utiliza Git como fuente de verdad para la infraestructura y las aplicaciones.
- **Application**: Representación de una aplicación en Argo CD, vinculada a un repositorio Git.
- **Sync**: Proceso que alinea el estado deseado (definido en Git) con el estado actual del clúster.
- **Sync Status**: Estado de sincronización de una aplicación (Synced, OutOfSync, etc.).
- **Manifests**: Archivos YAML que definen los recursos de Kubernetes.
- **Repo**: Repositorio Git que contiene los manifiestos de la aplicación.
- **Project**: Agrupación lógica de aplicaciones en Argo CD.

---

## **Instalación de Argo CD**
1. **Instalar en Kubernetes**:
   ```bash
   kubectl create namespace argocd
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   ```

2. **Acceso a la Interfaz Web**:
   - Expón el servicio Argo CD:
     ```bash
     kubectl port-forward svc/argocd-server -n argocd 8080:443
     ```
   - Accede a `https://localhost:8080`.

3. **Obtener la Contraseña Inicial**:
   ```bash
   kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
   ```

4. **Instalar CLI de Argo CD**:
   - **Linux**:
     ```bash
     curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
     sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
     rm argocd-linux-amd64
     ```
   - **Windows**: Descarga el binario desde [GitHub](https://github.com/argoproj/argo-cd/releases).

5. **Iniciar Sesión en la CLI**:
   ```bash
   argocd login <argocd-server-address>
   ```

---

## **Comandos Básicos de Argo CD**
1. **Crear una Aplicación**:
   ```bash
   argocd app create <app-name> \
     --repo <repo-url> \
     --path <path-to-manifests> \
     --dest-server <k8s-cluster-url> \
     --dest-namespace <namespace>
   ```

2. **Sincronizar una Aplicación**:
   ```bash
   argocd app sync <app-name>
   ```

3. **Ver Estado de una Aplicación**:
   ```bash
   argocd app get <app-name>
   ```

4. **Listar Aplicaciones**:
   ```bash
   argocd app list
   ```

5. **Eliminar una Aplicación**:
   ```bash
   argocd app delete <app-name>
   ```

6. **Actualizar una Aplicación**:
   ```bash
   argocd app set <app-name> --sync-policy automated
   ```

7. **Ver Logs de una Aplicación**:
   ```bash
   argocd app logs <app-name>
   ```

8. **Forzar Sincronización**:
   ```bash
   argocd app sync <app-name> --force
   ```

9. **Rollback de una Aplicación**:
   ```bash
   argocd app rollback <app-name> --to <revision>
   ```

---

## **Configuración de Aplicaciones**
1. **Archivo de Configuración de Aplicación** (`application.yaml`):
   ```yaml
   apiVersion: argoproj.io/v1alpha1
   kind: Application
   metadata:
     name: my-app
     namespace: argocd
   spec:
     project: default
     source:
       repoURL: https://github.com/my-repo.git
       path: k8s-manifests
       targetRevision: HEAD
     destination:
       server: https://kubernetes.default.svc
       namespace: my-namespace
     syncPolicy:
       automated:
         prune: true
         selfHeal: true
   ```

2. **Sincronización Automática**:
   - Habilita la sincronización automática en la configuración de la aplicación:
     ```yaml
     syncPolicy:
       automated:
         prune: true
         selfHeal: true
     ```

3. **Sincronización Manual**:
   - Desactiva la sincronización automática y sincroniza manualmente:
     ```bash
     argocd app sync <app-name>
     ```

---

## **Gestión de Proyectos**
1. **Crear un Proyecto**:
   ```bash
   argocd proj create <project-name>
   ```

2. **Añadir Repositorios a un Proyecto**:
   ```bash
   argocd proj add-source <project-name> <repo-url>
   ```

3. **Definir Destinos de Clúster**:
   ```bash
   argocd proj add-destination <project-name> <cluster-url> <namespace>
   ```

4. **Listar Proyectos**:
   ```bash
   argocd proj list
   ```

---

## **Solución de Problemas**
1. **Estado OutOfSync**:
   - Verifica los manifiestos en Git y el estado del clúster.
   - Usa `argocd app diff <app-name>` para ver las diferencias.

2. **Errores de Sincronización**:
   - Revisa los logs de la aplicación:
     ```bash
     argocd app logs <app-name>
     ```

3. **Problemas de Conexión con el Repositorio**:
   - Verifica las credenciales y permisos del repositorio.

4. **Recursos Huérfanos**:
   - Usa `argocd app sync --prune` para eliminar recursos no deseados.

---

## **Mejores Prácticas**
1. **Usar Sincronización Automática**: Habilita `automated` para mantener el clúster sincronizado con Git.
2. **Definir Políticas de Sincronización**: Usa `syncPolicy` para controlar el comportamiento de sincronización.
3. **Gestionar Accesos con Proyectos**: Usa proyectos para agrupar aplicaciones y controlar permisos.
4. **Monitorear Aplicaciones**: Usa la interfaz web o la CLI para monitorear el estado de las aplicaciones.
5. **Versionar Manifiestos**: Almacena los manifiestos de Kubernetes en un repositorio Git.

---

## **Ejemplo Completo**
1. **Repositorio Git**:
   - Estructura del repositorio:
     ```
     my-repo/
     └── k8s-manifests/
         ├── deployment.yaml
         └── service.yaml
     ```

2. **Archivo de Aplicación** (`application.yaml`):
   ```yaml
   apiVersion: argoproj.io/v1alpha1
   kind: Application
   metadata:
     name: my-app
     namespace: argocd
   spec:
     project: default
     source:
       repoURL: https://github.com/my-repo.git
       path: k8s-manifests
       targetRevision: HEAD
     destination:
       server: https://kubernetes.default.svc
       namespace: my-namespace
     syncPolicy:
       automated:
         prune: true
         selfHeal: true
   ```

3. **Crear la Aplicación**:
   ```bash
   argocd app create my-app \
     --repo https://github.com/my-repo.git \
     --path k8s-manifests \
     --dest-server https://kubernetes.default.svc \
     --dest-namespace my-namespace
   ```

---

Este cheatsheet cubre los aspectos más importantes de Argo CD. Si necesitas más detalles sobre algún tema, no dudes en preguntar. ¡Buena suerte con tus implementaciones de GitOps! 🚀
