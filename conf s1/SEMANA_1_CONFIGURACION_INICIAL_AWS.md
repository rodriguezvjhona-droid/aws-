Documentación – Semana 1: Configuración inicial de AWS
Aprendizaje y configuración del entorno de trabajo

Objetivo: Configurar el acceso a Amazon Web Services (AWS) mediante un usuario IAM y la herramienta AWS CLI para administrar servicios desde la terminal.
Actividades realizadas
1. Configuración de IAM: Se trabajó en la creación de un usuario de trabajo para administrar los servicios de AWS y sus permisos.
2. Instalación de AWS CLI: Se instaló AWS Command Line Interface versión 2 en Ubuntu mediante WSL.
3. Configuración de credenciales: Se configuraron las credenciales de acceso utilizando el comando aws configure.
4. Configuración de región: Se estableció la región us-east-2 para trabajar con los servicios de AWS.
5. Verificación de configuración: Se utilizó aws configure list para consultar las credenciales y la configuración de la terminal.
6. Pruebas de conexión: Se realizaron pruebas con comandos como aws sts get-caller-identity y aws iam list-users para comprobar la identidad y los permisos del usuario.
Comandos utilizados
aws --version
aws configure
aws configure list
aws configure get region
aws sts get-caller-identity
aws iam list-users


Conocimientos adquiridos
- Comprender qué es IAM y cómo funcionan los usuarios y permisos.
- Instalar y configurar AWS CLI.
- Conectar una terminal Linux con una cuenta de AWS.
- Identificar la región de trabajo.
- Reconocer la importancia de proteger las credenciales de acceso.