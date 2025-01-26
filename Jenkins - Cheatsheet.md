

---

## **Conceptos Básicos de Jenkins**
- **Jenkins**: Herramienta de automatización de código abierto para integración continua y entrega continua (CI/CD).
- **Pipeline**: Conjunto de pasos automatizados que definen el proceso de construcción, prueba y despliegue.
- **Job/Tarea**: Un proceso automatizado en Jenkins (por ejemplo, compilar código, ejecutar pruebas).
- **Node/Nodo**: Máquina física o virtual donde se ejecutan los jobs (puede ser el servidor maestro o agentes).
- **Agent/Agente**: Máquina esclava que ejecuta jobs en nombre del servidor Jenkins.
- **Workspace**: Directorio donde Jenkins ejecuta los jobs y almacena archivos temporales.
- **Plugin**: Extensión que añade funcionalidades adicionales a Jenkins.

---

## **Instalación y Configuración**
1. **Instalación en Linux**:
   ```bash
   sudo apt update
   sudo apt install openjdk-11-jdk
   wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
   sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
   sudo apt update
   sudo apt install jenkins
   sudo systemctl start jenkins
   sudo systemctl enable jenkins
   ```

2. **Acceso a Jenkins**:
   - Abre el navegador y ve a `http://<ip-del-servidor>:8080`.
   - Desbloquea Jenkins usando la contraseña inicial:
     ```bash
     sudo cat /var/lib/jenkins/secrets/initialAdminPassword
     ```

3. **Instalación de Plugins**:
   - Ve a **Manage Jenkins > Manage Plugins**.
   - Busca e instala plugins populares como:
     - Git
     - Pipeline
     - Docker
     - Blue Ocean
     - Credentials

4. **Configuración de Agentes**:
   - Ve a **Manage Jenkins > Manage Nodes and Clouds**.
   - Añade un nuevo nodo y configura la conexión (SSH, JNLP, etc.).

---

## **Comandos Útiles de Jenkins**
1. **Reiniciar Jenkins**:
   - Desde la interfaz web: `http://<jenkins-url>/restart`.
   - Desde la terminal:
     ```bash
     sudo systemctl restart jenkins
     ```

2. **Ver logs**:
   ```bash
   sudo tail -f /var/log/jenkins/jenkins.log
   ```

3. **Actualizar Jenkins**:
   ```bash
   sudo apt update
   sudo apt install jenkins
   ```

4. **Backup de Jenkins**:
   - Copia el directorio `JENKINS_HOME` (por defecto `/var/lib/jenkins`).

---

## **Creación y Gestión de Jobs**
1. **Crear un Job**:
   - Ve a **New Item** > Elige el tipo de job (Freestyle, Pipeline, etc.).
   - Configura el repositorio SCM (Git, SVN, etc.).
   - Define los pasos de construcción (shell scripts, comandos Maven, etc.).

2. **Ejecutar un Job**:
   - Haz clic en **Build Now** en la página del job.

3. **Ver Salida del Job**:
   - Haz clic en el número de build y selecciona **Console Output**.

4. **Programar Jobs**:
   - Usa **Build Triggers > Build periodically** y define un cron job (por ejemplo, `H/15 * * * *` para cada 15 minutos).

---

## **Jenkins Pipeline**
1. **Definir un Pipeline**:
   - Crea un archivo `Jenkinsfile` en tu repositorio:
     ```groovy
     pipeline {
         agent any
         stages {
             stage('Build') {
                 steps {
                     sh 'mvn clean package'
                 }
             }
             stage('Test') {
                 steps {
                     sh 'mvn test'
                 }
             }
             stage('Deploy') {
                 steps {
                     sh 'echo "Deploying..."'
                 }
             }
         }
     }
     ```

2. **Crear un Pipeline Job**:
   - Ve a **New Item** > Elige **Pipeline**.
   - Selecciona **Pipeline script from SCM** y configura el repositorio.

3. **Sintaxis Básica de Pipeline**:
   - **agent**: Define dónde se ejecuta el pipeline.
   - **stages**: Bloques de pasos lógicos.
   - **steps**: Comandos o acciones dentro de un stage.
   - **post**: Acciones posteriores a la ejecución (por ejemplo, notificaciones).

---

## **Gestión de Credenciales**
1. **Añadir Credenciales**:
   - Ve a **Manage Jenkins > Manage Credentials**.
   - Añade credenciales de tipo:
     - Usuario/contraseña
     - SSH Key
     - Token de acceso (GitHub, Docker Hub, etc.).

2. **Usar Credenciales en Pipelines**:
   ```groovy
   pipeline {
       agent any
       stages {
           stage('Deploy') {
               steps {
                   withCredentials([usernamePassword(credentialsId: 'my-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                       sh 'echo "User: $USER, Pass: $PASS"'
                   }
               }
           }
       }
   }
   ```

---

## **Plugins Populares**
1. **Git**: Integración con repositorios Git.
2. **Docker**: Construcción y despliegue de contenedores Docker.
3. **Blue Ocean**: Interfaz visual para Pipelines.
4. **Credentials**: Gestión de credenciales.
5. **Email Extension**: Notificaciones por correo electrónico.
6. **JUnit**: Visualización de resultados de pruebas.
7. **Artifactory**: Integración con JFrog Artifactory.

---

## **Solución de Problemas**
1. **Problemas de Conexión con Agentes**:
   - Verifica que el agente esté en línea y tenga acceso al servidor Jenkins.
   - Revisa los logs del agente.

2. **Errores en Pipelines**:
   - Revisa la salida de la consola para identificar errores.
   - Usa `sh 'set -x'` para depurar scripts.

3. **Problemas de Memoria**:
   - Aumenta la memoria de Jenkins editando `/etc/default/jenkins`:
     ```bash
     JAVA_OPTS="-Xmx2048m"
     ```

---

## **Mejores Prácticas**
1. **Usar Pipelines Declarativos**: Son más fáciles de mantener y leer.
2. **Versionar Jenkinsfiles**: Almacena tus `Jenkinsfile` en el repositorio del proyecto.
3. **Limitar Permisos**: Usa el plugin **Role Strategy** para gestionar permisos.
4. **Monitorear Jobs**: Usa plugins como **Monitoring** para supervisar el rendimiento.
5. **Backup Regular**: Realiza copias de seguridad del directorio `JENKINS_HOME`.

---

Este cheatsheet cubre los aspectos más importantes de Jenkins. ¡Espero que te sea útil! Si necesitas más detalles sobre algún tema, no dudes en preguntar. 🚀
