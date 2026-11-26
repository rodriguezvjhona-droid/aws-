# SEMANA 3 — CICLO DE VIDA DE UN BUCKET EN AMAZON S3

**Proyecto:** Administración de servicios en AWS  
**Estudiante:** Jonathan Rodriguez Vanegas  
**Plataforma:** Amazon Web Services (AWS)  
**Servicio:** Amazon S3 e IAM  
**Sistema operativo:** Ubuntu mediante WSL  
**Herramienta:** AWS CLI v2  
**Región:** us-east-2 (Ohio)  
**Usuario administrador:** admin-jhona  
**Usuario de lectura:** lector-s3-jonathan  
**Perfil secundario:** lector-s3  
**Bucket:** practica-jonathan-4821  
**Archivo de documentación:** evidencia.md  

---

## 1. Introducción

En esta práctica se desarrolla el ciclo de vida de un bucket en Amazon Simple Storage Service (Amazon S3), un servicio de almacenamiento de objetos ofrecido por Amazon Web Services.

El propósito es aprender a administrar archivos almacenados en la nube, realizar operaciones de carga y descarga, modificar objetos, generar enlaces temporales y administrar permisos de acceso mediante AWS Identity and Access Management (IAM).

También se implementará un usuario con permisos limitados de lectura para comprobar la importancia del principio de mínimo privilegio en la seguridad de los servicios en la nube.

## 2. Objetivos

### Objetivo general

Comprender y ejecutar las operaciones principales del ciclo de vida de un bucket de Amazon S3 utilizando AWS CLI v2.

### Objetivos específicos

- Verificar la configuración y autenticación de AWS CLI.
- Crear y consultar un bucket de Amazon S3.
- Crear, subir y descargar archivos.
- Consultar los metadatos de los objetos.
- Modificar y renombrar archivos almacenados.
- Generar enlaces temporales mediante URL prefirmadas.
- Crear un usuario IAM con permisos de lectura.
- Comprobar los permisos permitidos y denegados.
- Eliminar los recursos creados y verificar su limpieza.

## 3. Herramientas y recursos utilizados

| Recurso | Configuración |
|---|---|
| Servicio de almacenamiento | Amazon S3 |
| Administración de usuarios | AWS IAM |
| Interfaz | AWS CLI v2 |
| Terminal | Ubuntu WSL |
| Región | us-east-2 |
| Usuario administrador | admin-jhona |
| Usuario de lectura | lector-s3-jonathan |
| Perfil secundario | lector-s3 |
| Bucket | practica-jonathan-4821 |

## 4. Desarrollo de la práctica

### PASO 1. Verificación de AWS CLI y preparación del entorno

Se verifica que AWS CLI esté instalado y configurado correctamente.

**Comandos utilizados:**

```bash
aws --version
aws sts get-caller-identity
aws configure get region
```

El comando `aws --version` permite comprobar la versión instalada.

El comando `aws sts get-caller-identity` permite identificar la cuenta y el usuario autenticado.

El comando `aws configure get region` permite consultar la región configurada.

Posteriormente, se crea la carpeta de trabajo:

```bash
mkdir -p ~/practica-s3
cd ~/practica-s3
pwd
```

**Resultado esperado:** contar con un entorno de trabajo organizado y una sesión de AWS correctamente autenticada.

**Evidencia 1:** identidad del usuario administrador y región configurada.

### PASO 2. Creación del bucket S3

Se crea un bucket que servirá como almacenamiento principal de la práctica.

```bash
aws s3 mb s3://practica-jonathan-4821
```

Para comprobar que el bucket fue creado:

```bash
aws s3 ls
```

Se consulta la región del bucket:

```bash
aws s3api get-bucket-location \
--bucket practica-jonathan-4821
```

**Explicación:**

- `mb`: crea un bucket.
- `ls`: muestra los buckets disponibles.
- `get-bucket-location`: consulta la región donde se encuentra el bucket.

**Resultado esperado:** el bucket aparece en la lista y su ubicación corresponde a la región configurada.

**Evidencia 2:** captura del comando `get-bucket-location` y su resultado.

### PASO 3. Creación y carga de archivos

Se crean tres archivos de texto para realizar las pruebas de almacenamiento.

```bash
echo "Estas son mis notas de aprendizaje sobre AWS S3." > notas.txt

echo "1. Crear bucket S3. 2. Subir archivos. 3. Configurar permisos." > tareas.txt

echo "Este archivo contiene informacion privada de la practica." > secreto.txt
```

Se comprueba su existencia:

```bash
ls -lh
```

Se consulta su contenido:

```bash
cat notas.txt
cat tareas.txt
cat secreto.txt
```

#### 3.1 Carga del primer archivo

Se utiliza `aws s3 cp` para subir `notas.txt`.

```bash
aws s3 cp notas.txt s3://practica-jonathan-4821/
```

#### 3.2 Carga de los otros archivos mediante sync

Se crea una carpeta auxiliar:

```bash
mkdir carga
cp tareas.txt secreto.txt carga/
```

Se sincronizan los archivos con S3:

```bash
aws s3 sync carga/ s3://practica-jonathan-4821/
```

#### 3.3 Verificación de objetos

```bash
aws s3 ls s3://practica-jonathan-4821/ --human-readable --summarize
```

**Explicación:**

- `cp`: copia archivos entre el equipo local y S3.
- `sync`: sincroniza archivos entre una carpeta local y un destino S3.
- `--human-readable`: presenta tamaños en un formato fácil de interpretar.
- `--summarize`: presenta el total de objetos y su tamaño.

**Resultado esperado:** encontrar los tres objetos:

1. notas.txt
2. tareas.txt
3. secreto.txt

**Evidencia 3:** captura de los tres archivos, sus tamaños y el resumen total.

### PASO 4. Consulta y descarga de objetos

Se realizan diferentes operaciones para consultar la información almacenada.

#### 4.1 Visualización directa de notas.txt

```bash
aws s3 cp s3://practica-jonathan-4821/notas.txt -
```

El símbolo `-` permite enviar el contenido directamente a la salida de la terminal sin crear un archivo local.

#### 4.2 Consulta de metadatos

```bash
aws s3api head-object \
--bucket practica-jonathan-4821 \
--key tareas.txt
```

Se identifican los siguientes campos:

- `ContentLength`: tamaño del archivo en bytes.
- `LastModified`: fecha de última modificación.
- `ContentType`: tipo de contenido del objeto.

#### 4.3 Descarga de secreto.txt

```bash
mkdir descargas

aws s3 cp s3://practica-jonathan-4821/secreto.txt descargas/
```

Se comprueba la descarga:

```bash
ls -lh descargas/
cat descargas/secreto.txt
```

**Resultado esperado:** consultar los datos de un objeto y descargarlo correctamente al equipo local.

### PASO 5. Modificación de objetos

Se realizan operaciones de renombrado y actualización de archivos.

#### 5.1 Renombrar tareas.txt

```bash
aws s3 mv \
s3://practica-jonathan-4821/tareas.txt \
s3://practica-jonathan-4821/pendientes.txt
```

Se verifica el cambio:

```bash
aws s3 ls s3://practica-jonathan-4821/
```

En Amazon S3, el renombrado se realiza mediante una operación de copia al nuevo nombre y eliminación del objeto anterior.

#### 5.2 Actualizar notas.txt

Se agrega una segunda línea al archivo local:

```bash
echo "Estoy aprendiendo a administrar servicios de almacenamiento en AWS." >> notas.txt
```

Se vuelve a subir:

```bash
aws s3 cp notas.txt s3://practica-jonathan-4821/
```

#### 5.3 Comprobar la actualización

```bash
aws s3 cp s3://practica-jonathan-4821/notas.txt -
```

**Resultado esperado:** visualizar las dos líneas del archivo actualizado.

**Evidencia 4:** captura del contenido actualizado de `notas.txt`.

### PASO 6. Generación de enlaces temporales

Se utiliza una URL prefirmada para compartir temporalmente un objeto almacenado en S3.

```bash
aws s3 presign \
s3://practica-jonathan-4821/notas.txt \
--expires-in 60
```

El parámetro `--expires-in 60` establece una vigencia de 60 segundos.

**Procedimiento:**

1. Generar el enlace temporal.
2. Abrirlo en un navegador.
3. Comprobar que permite visualizar el contenido.
4. Esperar más de 60 segundos.
5. Intentar abrir nuevamente el enlace.
6. Comprobar que el enlace ha expirado.

**Explicación:** las URL prefirmadas permiten conceder acceso temporal a un objeto sin tener que hacer público el bucket.

**Evidencia 5:** captura del enlace funcionando y captura del intento de acceso después de su expiración.

**Seguridad:** no compartir públicamente las URL prefirmadas, ya que funcionan como enlaces de acceso temporal.

### PASO 7. Creación de un usuario IAM de solo lectura

Se crea un usuario independiente para comprobar el control de acceso a los objetos.

#### 7.1 Crear el usuario

```bash
aws iam create-user \
--user-name lector-s3-jonathan
```

Si el usuario ya existe, se debe verificar con:

```bash
aws iam get-user \
--user-name lector-s3-jonathan
```

#### 7.2 Crear la política de permisos

Se crea el archivo:

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

Esta política permite consultar los objetos del bucket, siempre que se conozca su nombre exacto.

No concede permisos para listar el bucket ni eliminar objetos.

#### 7.3 Adjuntar la política

```bash
aws iam put-user-policy \
--user-name lector-s3-jonathan \
--policy-name leer-objetos \
--policy-document file://solo-lectura.json
```

#### 7.4 Crear una clave de acceso

```bash
aws iam create-access-key \
--user-name lector-s3-jonathan
```

Se deben configurar las credenciales en el perfil secundario:

```bash
aws configure --profile lector-s3
```

Se introducen:

- Access Key ID.
- Secret Access Key.
- Región: us-east-2.
- Formato: json.

**Seguridad:** las claves de acceso son credenciales privadas. Nunca deben publicarse en capturas, repositorios, archivos de documentación o mensajes.

### PASO 8. Comprobación de permisos

Se realizan tres pruebas utilizando el perfil `lector-s3`.

#### 8.1 Comprobar lectura permitida

```bash
aws s3 cp \
s3://practica-jonathan-4821/notas.txt \
- \
--profile lector-s3
```

**Resultado esperado:** acceso permitido y visualización del contenido.

**Evidencia 6:** captura de la lectura exitosa.

#### 8.2 Comprobar listado denegado

```bash
aws s3 ls s3://practica-jonathan-4821/ \
--profile lector-s3
```

**Resultado esperado:** `AccessDenied`.

Esto sucede porque la política no contiene el permiso `s3:ListBucket`.

**Evidencia 7:** captura del error de acceso denegado.

#### 8.3 Comprobar eliminación denegada

```bash
aws s3 rm \
s3://practica-jonathan-4821/secreto.txt \
--profile lector-s3
```

**Resultado esperado:** `AccessDenied`.

Esto sucede porque el usuario no dispone del permiso `s3:DeleteObject`.

**Evidencia 8:** captura del error de eliminación denegada.

### PASO 9. Eliminación de recursos

Una vez guardadas todas las evidencias, se procede a limpiar los recursos creados.

#### 9.1 Intentar eliminar el bucket sin vaciarlo

```bash
aws s3 rb s3://practica-jonathan-4821
```

**Resultado esperado:** error indicando que el bucket no está vacío.

**Evidencia 9:** captura del intento de eliminación.

#### 9.2 Vaciar el bucket

```bash
aws s3 rm s3://practica-jonathan-4821 --recursive
```

Se eliminan todos los objetos almacenados.

#### 9.3 Eliminar el bucket

```bash
aws s3 rb s3://practica-jonathan-4821
```

#### 9.4 Consultar las claves de acceso

```bash
aws iam list-access-keys \
--user-name lector-s3-jonathan
```

Se identifica el AccessKeyId correspondiente y se elimina:

```bash
aws iam delete-access-key \
--user-name lector-s3-jonathan \
--access-key-id TU_ACCESS_KEY_ID
```

#### 9.5 Eliminar la política IAM

```bash
aws iam delete-user-policy \
--user-name lector-s3-jonathan \
--policy-name leer-objetos
```

#### 9.6 Eliminar el usuario IAM

```bash
aws iam delete-user \
--user-name lector-s3-jonathan
```

#### 9.7 Eliminar el perfil local

Se abre el archivo de credenciales:

```bash
nano ~/.aws/credentials
```

Se elimina únicamente el bloque `[lector-s3]`.

Después se abre:

```bash
nano ~/.aws/config
```

Se elimina únicamente el bloque `[profile lector-s3]`.

No se deben eliminar las credenciales del usuario administrador.

#### 9.8 Verificación final

Se comprueba que el bucket fue eliminado:

```bash
aws s3 ls
```

Se verifica que el usuario IAM ya no existe:

```bash
aws iam get-user \
--user-name lector-s3-jonathan
```

**Resultado esperado:** error `NoSuchEntity`.

Se comprueba también:

```bash
aws iam list-access-keys \
--user-name lector-s3-jonathan
```

La consulta debe indicar que el usuario no existe.

**Evidencia 10:** captura de la lista de buckets y de la verificación del usuario eliminado.

---

## 5. Control de evidencias

La práctica debe contar con las siguientes diez evidencias:

| N.º | Evidencia | Estado |
|---|---|---|
| 01 | Identidad del administrador y región | Pendiente |
| 02 | Región del bucket S3 | Pendiente |
| 03 | Tres objetos y tamaño total | Pendiente |
| 04 | Archivo notas.txt actualizado | Pendiente |
| 05 | Enlace temporal funcionando y expirado | Pendiente |
| 06 | Lectura permitida | Pendiente |
| 07 | Listado denegado | Pendiente |
| 08 | Eliminación denegada | Pendiente |
| 09 | Error al eliminar bucket no vacío | Pendiente |
| 10 | Verificación de limpieza de recursos | Pendiente |

**Nota:** los estados se actualizarán cuando se ejecuten y comprueben las operaciones. No se deben registrar resultados que no hayan sido verificados.

## 6. Conceptos aprendidos

Durante la práctica se trabajan los siguientes conceptos:

**Amazon S3:** servicio de almacenamiento de objetos en la nube.

**Bucket:** contenedor donde se almacenan los objetos.

**Objeto:** archivo almacenado dentro de un bucket.

**AWS CLI:** herramienta que permite administrar servicios AWS mediante comandos.

**IAM:** servicio de administración de identidades y permisos.

**Política IAM:** documento que define qué acciones puede realizar un usuario o recurso.

**URL prefirmada:** enlace temporal que permite acceder a un objeto mediante permisos firmados.

**Mínimo privilegio:** principio de seguridad que establece que cada usuario debe recibir únicamente los permisos necesarios para realizar sus funciones.

## 7. Preguntas de reflexión

**1. ¿Cuál es la diferencia entre los comandos cp, mv y sync?**

`cp` permite copiar archivos entre el equipo local y S3. `mv` permite mover o renombrar objetos. `sync` compara el contenido de dos ubicaciones y transfiere los archivos necesarios para sincronizarlas.

**2. ¿Por qué una URL prefirmada tiene tiempo de expiración?**

Porque permite compartir temporalmente un objeto sin conceder acceso permanente. Esto ayuda a reducir los riesgos de seguridad.

**3. ¿Por qué el usuario lector puede descargar un archivo, pero no listar el bucket?**

Porque tiene el permiso `s3:GetObject`, pero no tiene el permiso `s3:ListBucket`. Los permisos de lectura de objetos y listado del bucket son independientes.

**4. ¿Qué sucede cuando se intenta eliminar un bucket que contiene objetos?**

Amazon S3 impide eliminarlo mientras tenga objetos almacenados. Primero se deben eliminar los objetos y después eliminar el bucket.

**5. ¿Por qué es importante eliminar las claves de acceso y usuarios que ya no se utilizan?**

Porque mantener credenciales y usuarios innecesarios aumenta la superficie de riesgo. Eliminarlos reduce las posibilidades de accesos no autorizados y mejora la administración de la seguridad.

## 8. Conclusiones

La práctica permitió conocer y ejecutar las operaciones principales de administración de almacenamiento en Amazon S3.

Se estudiaron los procesos de creación de buckets, carga y descarga de archivos, consulta de metadatos, modificación de objetos y generación de enlaces temporales.

También se comprendió la importancia de AWS IAM para controlar los permisos de los usuarios y aplicar el principio de mínimo privilegio.

Finalmente, se realizaron pruebas de acceso permitido y denegado, junto con la eliminación de los recursos utilizados.

Esta actividad fortalece los conocimientos fundamentales sobre almacenamiento en la nube, administración de recursos AWS y seguridad mediante permisos.

---

**FIN DE LA DOCUMENTACIÓN — SEMANA 3**

**Estudiante:** Jonathan Rodriguez Vanegas  
**Servicio principal:** Amazon S3  
**Servicio complementario:** AWS IAM  
**Herramienta:** AWS CLI v2