

---

## **Conceptos Básicos de Terraform**
- **Infraestructura como Código (IaC)**: Definir y gestionar infraestructura mediante archivos de configuración.
- **Provider**: Plugin que interactúa con una plataforma específica (AWS, Azure, GCP, etc.).
- **Resource**: Componente de infraestructura (por ejemplo, una instancia EC2 en AWS).
- **Module**: Conjunto de recursos reutilizables y configurados.
- **State**: Archivo (`terraform.tfstate`) que almacena el estado actual de la infraestructura.
- **Plan**: Previsualización de los cambios que Terraform aplicará.
- **Apply**: Aplicar los cambios para alinear la infraestructura con la configuración.
- **Destroy**: Eliminar todos los recursos gestionados por Terraform.

---

## **Instalación de Terraform**
1. **Linux**:
   ```bash
   sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
   wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
   echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
   sudo apt update && sudo apt install terraform
   ```

2. **Windows**:
   - Descarga el binario desde [terraform.io](https://www.terraform.io/downloads.html).
   - Extrae y añade la ruta al PATH.

3. **Verificar Instalación**:
   ```bash
   terraform --version
   ```

---

## **Comandos Básicos de Terraform**
1. **Inicializar un Proyecto**:
   ```bash
   terraform init
   ```

2. **Validar la Configuración**:
   ```bash
   terraform validate
   ```

3. **Previsualizar Cambios**:
   ```bash
   terraform plan
   ```

4. **Aplicar Cambios**:
   ```bash
   terraform apply
   ```

5. **Destruir Infraestructura**:
   ```bash
   terraform destroy
   ```

6. **Formatear Archivos de Configuración**:
   ```bash
   terraform fmt
   ```

7. **Mostrar Estado Actual**:
   ```bash
   terraform show
   ```

8. **Refrescar Estado**:
   ```bash
   terraform refresh
   ```

9. **Importar Recursos Existentes**:
   ```bash
   terraform import <resource> <id>
   ```

10. **Listar Recursos**:
    ```bash
    terraform state list
    ```

---

## **Estructura de Archivos de Terraform**
1. **Archivo de Configuración Principal** (`main.tf`):
   - Define proveedores, recursos y módulos.
   ```hcl
   provider "aws" {
     region = "us-east-1"
   }

   resource "aws_instance" "example" {
     ami           = "ami-0c55b159cbfafe1f0"
     instance_type = "t2.micro"
   }
   ```

2. **Variables** (`variables.tf`):
   - Define variables para personalizar la configuración.
   ```hcl
   variable "instance_type" {
     description = "Tipo de instancia EC2"
     type        = string
     default     = "t2.micro"
   }
   ```

3. **Outputs** (`outputs.tf`):
   - Muestra valores útiles después de aplicar la configuración.
   ```hcl
   output "instance_id" {
     description = "ID de la instancia EC2"
     value       = aws_instance.example.id
   }
   ```

4. **Terraform State** (`terraform.tfstate`):
   - Almacena el estado actual de la infraestructura (no debe editarse manualmente).

---

## **Uso de Módulos**
1. **Módulo Local**:
   ```hcl
   module "vpc" {
     source = "./modules/vpc"
     cidr   = "10.0.0.0/16"
   }
   ```

2. **Módulo Remoto (Terraform Registry)**:
   ```hcl
   module "vpc" {
     source  = "terraform-aws-modules/vpc/aws"
     version = "3.0.0"
     cidr    = "10.0.0.0/16"
   }
   ```

---

## **Gestión del Estado (State)**
1. **Backend Remoto**:
   - Almacena el estado en un backend remoto (por ejemplo, S3, Azure Blob Storage).
   ```hcl
   terraform {
     backend "s3" {
       bucket = "my-terraform-state"
       key    = "path/to/my/state.tfstate"
       region = "us-east-1"
     }
   }
   ```

2. **Bloquear Estado**:
   - Usa **DynamoDB** para evitar conflictos en entornos colaborativos.

---

## **Workspaces**
1. **Crear un Workspace**:
   ```bash
   terraform workspace new dev
   ```

2. **Listar Workspaces**:
   ```bash
   terraform workspace list
   ```

3. **Cambiar de Workspace**:
   ```bash
   terraform workspace select dev
   ```

---

## **Solución de Problemas**
1. **Errores de Sintaxis**:
   - Usa `terraform validate` para verificar la configuración.

2. **Problemas con el Estado**:
   - Usa `terraform state` para inspeccionar o modificar el estado.

3. **Recursos Huérfanos**:
   - Usa `terraform import` para importar recursos existentes.

4. **Depuración**:
   - Usa `TF_LOG=DEBUG` para habilitar logs detallados:
     ```bash
     export TF_LOG=DEBUG
     terraform apply
     ```

---

## **Mejores Prácticas**
1. **Versionar Configuraciones**: Usa un sistema de control de versiones (Git) para tus archivos de Terraform.
2. **Usar Módulos**: Reutiliza código con módulos para evitar duplicación.
3. **Gestionar el Estado de Forma Remota**: Almacena el estado en un backend remoto para entornos colaborativos.
4. **Validar y Planificar Siempre**: Usa `terraform validate` y `terraform plan` antes de aplicar cambios.
5. **Limitar Permisos**: Usa políticas de IAM para restringir permisos en la nube.

---

## **Ejemplo Completo**
```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = var.instance_type

  tags = {
    Name = "example-instance"
  }
}

variable "instance_type" {
  description = "Tipo de instancia EC2"
  type        = string
  default     = "t2.micro"
}

output "instance_id" {
  description = "ID de la instancia EC2"
  value       = aws_instance.example.id
}
```

---

Este cheatsheet cubre los aspectos más importantes de Terraform. Si necesitas más detalles sobre algún tema, no dudes en preguntar. ¡Buena suerte con tu infraestructura como código! 🚀
