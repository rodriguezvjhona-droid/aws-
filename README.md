# ☁️ AWS Cloud Learning — Documentación y prácticas

![AWS](https://img.shields.io/badge/Amazon_AWS-Cloud-orange?logo=amazonaws)
![AWS CLI](https://img.shields.io/badge/AWS_CLI-v2-orange?logo=amazonaws)
![Linux](https://img.shields.io/badge/Linux-Ubuntu-E95420?logo=ubuntu)
![Status](https://img.shields.io/badge/Aprendizaje-En_progreso-blue)

Repositorio dedicado al aprendizaje práctico de **Amazon Web Services (AWS)**, mediante ejercicios, configuraciones y documentación técnica desarrollados desde Ubuntu utilizando Windows Subsystem for Linux (WSL) y AWS CLI v2.

El objetivo es adquirir conocimientos sobre infraestructura en la nube, administración de identidades, almacenamiento, seguridad y gestión de recursos mediante la línea de comandos.

---

## 👨‍💻 Información del proyecto

| Información | Detalle |
|---|---|
| Estudiante | Jonathan Rodriguez Vanegas |
| Plataforma | Amazon Web Services |
| Sistema operativo | Ubuntu (WSL) |
| Herramienta | AWS CLI v2 |
| Región de trabajo | us-east-2 |
| Servicio principal | IAM y S3 |
| Lenguaje de documentación | Markdown |
| Repositorio | AWS Cloud Learning |

---

# 📚 Contenido del aprendizaje

## Semana 1 — Introducción y configuración de AWS

**Objetivo:** preparar el entorno de trabajo y establecer una conexión con AWS desde Ubuntu mediante AWS CLI.

### Actividades desarrolladas

- Instalación de AWS CLI versión 2.
- Actualización de paquetes de Ubuntu.
- Configuración inicial de AWS CLI.
- Configuración de credenciales.
- Selección de región AWS.
- Verificación de identidad mediante STS.
- Consulta de configuración local.

### Instalación de AWS CLI v2

Actualización del sistema:

```bash
sudo apt update
sudo apt install curl unzip -y
```

Descarga del instalador:

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
```

Descompresión:

```bash
unzip awscliv2.zip
```

Instalación:

```bash
sudo ./aws/install
```

Comprobación:

```bash
aws --version
```

### Configuración de AWS

```bash
aws configure
```

Configuración de región:

```text
us-east-2
```

Consulta de configuración:

```bash
aws configure list
```

Consulta de región:

```bash
aws configure get region
```

Verificación de identidad:

```bash
aws sts get-caller-identity
```

Consulta de usuarios IAM:

```bash
aws iam list-users
```

### Conceptos aprendidos

- AWS CLI.
- Credenciales de acceso.
- Regiones de AWS.
- Amazon STS.
- Identidad de usuarios IAM.
- Configuración local de AWS.

**Estado:** configuración e instalación trabajadas; autenticación verificada posteriormente durante las prácticas.

---

## Semana 2 — Administración de usuarios y permisos IAM

**Objetivo:** comprender cómo administrar usuarios, políticas y permisos mediante AWS Identity and Access Management (IAM).

### Actividades trabajadas

- Creación de usuarios IAM.
- Renombrado de usuarios.
- Administración de políticas.
- Asignación de permisos de solo lectura.
- Configuración de perfiles secundarios.
- Pruebas de acceso a servicios AWS.
- Eliminación de usuarios y políticas.

### Comandos principales

Consultar usuarios:

```bash
aws iam list-users
```

Consultar un usuario:

```bash
aws iam get-user --user-name lector-jonathan
```

Consultar políticas:

```bash
aws iam list-attached-user-policies \
--user-name lector-jonathan
```

Consultar claves de acceso:

```bash
aws iam list-access-keys \
--user-name lector-jonathan
```

Adjuntar una política de lectura de EC2:

```bash
aws iam attach-user-policy \
--user-name lector-jonathan \
--policy-arn arn:aws:iam::aws:policy/AmazonEC2ReadOnlyAccess
```

Configurar un perfil:

```bash
aws configure --profile lector
```

Consultar instancias EC2:

```bash
aws ec2 describe-instances --profile lector
```

### Conceptos aprendidos

- IAM Users.
- IAM Policies.
- Access Keys.
- AWS Profiles.
- AmazonEC2ReadOnlyAccess.
- Principio de mínimo privilegio.
- Administración de permisos.

**Estado:** creación y renombrado del usuario trabajados. La eliminación presentó un error `DeleteConflict` que requiere revisar las dependencias del usuario antes de completar la limpieza.

---

## Semana 3 — Ciclo de vida de un bucket en Amazon S3

**Objetivo:** aprender a administrar el almacenamiento de objetos mediante Amazon Simple Storage Service (S3).

### Configuración de la práctica

| Elemento | Valor |
|---|---|
| Usuario administrador | admin-jhona |
| Usuario de lectura | lector-s3-jonathan |
| Perfil secundario | lector-s3 |
| Bucket | practica-jonathan-4821 |
| Región | us-east-2 |
| Directorio de trabajo | practica-s3 |
| Documentación | evidencia.md |

### 1. Preparación del entorno

```bash
mkdir -p ~/practica-s3
cd ~/practica-s3
```

Verificar ubicación:

```bash
pwd
```

Verificar identidad:

```bash
aws sts get-caller-identity
```

### 2. Creación del bucket

```bash
aws s3 mb s3://practica-jonathan-4821
```

Consultar buckets:

```bash
aws s3 ls
```

Consultar región:

```bash
aws s3api get-bucket-location \
--bucket practica-jonathan-4821
```

### 3. Creación de archivos

```bash
echo "Estas son mis notas de aprendizaje sobre AWS S3." > notas.txt

echo "1. Crear bucket S3. 2. Subir archivos. 3. Configurar permisos." > tareas.txt

echo "Este archivo contiene informacion privada de la practica." > secreto.txt
```

### 4. Subida de archivos

Subir un archivo:

```bash
aws s3 cp notas.txt s3://practica-jonathan-4821/
```

Preparar los demás archivos:

```bash
mkdir carga
cp tareas.txt secreto.txt carga/
```

Sincronizar:

```bash
aws s3 sync carga/ s3://practica-jonathan-4821/
```

Consultar objetos:

```bash
aws s3 ls s3://practica-jonathan-4821/ \
--human-readable --summarize
```

### 5. Consulta y descarga

Mostrar contenido directamente:

```bash
aws s3 cp s3://practica-jonathan-4821/notas.txt -
```

Consultar metadatos:

```bash
aws s3api head-object \
--bucket practica-jonathan-4821 \
--key tareas.txt
```

Descargar un archivo:

```bash
mkdir descargas

aws s3 cp \
s3://practica-jonathan-4821/secreto.txt \
descargas/
```

### 6. Modificación de objetos

Renombrar un objeto:

```bash
aws s3 mv \
s3://practica-jonathan-4821/tareas.txt \
s3://practica-jonathan-4821/pendientes.txt
```

Actualizar notas:

```bash
echo "Estoy aprendiendo a administrar servicios de almacenamiento en AWS." >> notas.txt
```

Subir la versión actualizada:

```bash
aws s3 cp notas.txt s3://practica-jonathan-4821/
```

### 7. Enlaces temporales

Generar una URL prefirmada:

```bash
aws s3 presign \
s3://practica-jonathan-4821/notas.txt \
--expires-in 60
```

Este mecanismo permite compartir temporalmente un objeto sin hacer público el bucket.

### 8. Administración de usuarios IAM

Crear usuario:

```bash
aws iam create-user \
--user-name lector-s3-jonathan
```

Crear archivo de política:

```bash
nano solo-lectura.json
```

Contenido:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject"
      ],
      "Resource": "arn:aws:s3:::practica-jonathan-4821/*"
    }
  ]
}
```

Asignar política:

```bash
aws iam put-user-policy \
--user-name lector-s3-jonathan \
--policy-name leer-objetos \
--policy-document file://solo-lectura.json
```

Crear clave:

```bash
aws iam create-access-key \
--user-name lector-s3-jonathan
```

Configurar perfil:

```bash
aws configure --profile lector-s3
```

### 9. Pruebas de permisos

Lectura permitida:

```bash
aws s3 cp \
s3://practica-jonathan-4821/notas.txt \
- \
--profile lector-s3
```

Listado denegado:

```bash
aws s3 ls s3://practica-jonathan-4821/ \
--profile lector-s3
```

Eliminación denegada:

```bash
aws s3 rm \
s3://practica-jonathan-4821/secreto.txt \
--profile lector-s3
```

Se espera que el usuario pueda leer objetos, pero no listar el bucket ni eliminarlos.

### 10. Limpieza de recursos

Vaciar el bucket:

```bash
aws s3 rm s3://practica-jonathan-4821 --recursive
```

Eliminar bucket:

```bash
aws s3 rb s3://practica-jonathan-4821
```

Consultar claves:

```bash
aws iam list-access-keys \
--user-name lector-s3-jonathan
```

Eliminar clave:

```bash
aws iam delete-access-key \
--user-name lector-s3-jonathan \
--access-key-id TU_ACCESS_KEY_ID
```

Eliminar política:

```bash
aws iam delete-user-policy \
--user-name lector-s3-jonathan \
--policy-name leer-objetos
```

Eliminar usuario:

```bash
aws iam delete-user \
--user-name lector-s3-jonathan
```

Verificar eliminación:

```bash
aws s3 ls
```

```bash
aws iam get-user \
--user-name lector-s3-jonathan
```

**Estado:** procedimiento documentado y preparado para su ejecución y validación con evidencias.

---

# 🗂️ Estructura del repositorio

La estructura propuesta para organizar el aprendizaje es:

```text
AWS-Cloud-Learning/
│
├── README.md
│
├── Semana-01-Configuracion-AWS/
│   ├── README.md
│   └── evidencias/
│
├── Semana-02-Administracion-IAM/
│   ├── README.md
│   └── evidencias/
│
├── Semana-03-Ciclo-de-Vida-S3/
│   ├── evidencia.md
│   ├── solo-lectura.json
│   └── capturas/
│       ├── 01-identidad-region.png
│       ├── 02-creacion-bucket.png
│       ├── 03-archivos-subidos.png
│       ├── 04-notas-actualizadas.png
│       ├── 05-enlace-temporal.png
│       ├── 06-lectura-permitida.png
│       ├── 07-listado-denegado.png
│       ├── 08-eliminacion-denegada.png
│       ├── 09-bucket-no-vacio.png
│       └── 10-limpieza-recursos.png
│
└── .gitignore
```

---

# 🔐 Seguridad y buenas prácticas

Durante el desarrollo de las actividades se deben aplicar las siguientes medidas:

- No subir archivos `~/.aws/credentials` ni `~/.aws/config`.
- No publicar Access Key ID ni Secret Access Key.
- No incluir contraseñas o tokens en el repositorio.
- Utilizar políticas IAM con permisos mínimos.
- Evitar otorgar permisos públicos innecesarios a buckets.
- Eliminar las claves de acceso que ya no se utilicen.
- No publicar enlaces prefirmados vigentes.
- Verificar los recursos antes de eliminarlos.

### Archivo .gitignore

Crear un archivo `.gitignore` en la raíz:

```gitignore
# Credenciales AWS
.aws/
credentials
config

# Variables de entorno
.env
.env.*
!.env.example

# Archivos privados
*.pem
*.key
secrets/

# Archivos temporales
*.log
*.zip
*.bak

# Python
__pycache__/
```

**Importante:** nunca subir credenciales reales de AWS a GitHub, incluso si el repositorio es privado.

---

# 🎯 Objetivos de aprendizaje

Al finalizar las prácticas se busca adquirir conocimientos en:

- Administración básica de AWS.
- Configuración de AWS CLI v2.
- Gestión de identidades y permisos IAM.
- Administración de buckets Amazon S3.
- Transferencia de archivos hacia la nube.
- Consulta de metadatos.
- Generación de enlaces temporales.
- Aplicación del principio de mínimo privilegio.
- Seguridad y limpieza de recursos cloud.
- Documentación técnica con Markdown y GitHub.

---

# 📌 Estado general del proyecto

| Semana | Tema | Estado |
|---|---|---|
| 01 | Configuración de AWS CLI | En desarrollo |
| 02 | Administración IAM | En desarrollo |
| 03 | Ciclo de vida de Amazon S3 | En desarrollo |
| 04 | Próximas prácticas | Pendiente |

Los estados se actualizarán conforme se completen las actividades y se verifiquen sus resultados.

---

# 👨‍🎓 Autor

**Jonathan Rodriguez Vanegas**

Estudiante y desarrollador en formación, interesado en tecnologías cloud, infraestructura, desarrollo de software y administración de servicios AWS.

**Repositorio:** AWS Cloud Learning

**Tecnologías:** Amazon Web Services · AWS CLI · Ubuntu · WSL · IAM · Amazon S3 · Git · GitHub

---

*Documentación académica desarrollada como parte del proceso de aprendizaje práctico de computación en la nube.*
