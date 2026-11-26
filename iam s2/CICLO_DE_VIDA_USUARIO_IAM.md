# Semana 2 – Ciclo de vida de un usuario IAM

**Estudiante:** Jonathan Rodriguez Vanegas  
**Servicio:** Amazon Web Services (AWS)  
**Área:** Administración de usuarios y permisos IAM

## 1. Objetivo

Realizar el ciclo de vida de un usuario IAM desde AWS CLI, aprendiendo a crear, consultar, modificar, asignar permisos, comprobar accesos y eliminar usuarios, aplicando el principio de menor privilegio.

## 2. Actividades realizadas

### Parte 1. Creación del usuario

Se creó un usuario IAM llamado `dev-jonathan` mediante AWS CLI.

Comando utilizado:

```bash
aws iam create-user --user-name dev-jonathan
```

Posteriormente, se consultaron los usuarios de la cuenta y los datos del usuario creado.

```bash
aws iam list-users
aws iam get-user --user-name dev-jonathan
```

### Parte 2. Edición del usuario

Se agregó una etiqueta para identificar el área de desarrollo y posteriormente se cambió el nombre del usuario a `lector-jonathan`.

Comandos utilizados:

```bash
aws iam tag-user --user-name dev-jonathan --tags Key=area,Value=desarrollo
```

```bash
aws iam update-user --user-name dev-jonathan --new-user-name lector-jonathan
```

Se verificó el nuevo nombre y la etiqueta:

```bash
aws iam get-user --user-name lector-jonathan
aws iam list-user-tags --user-name lector-jonathan
```

### Parte 3. Asignación de permisos

Se adjuntó la política administrada `AmazonEC2ReadOnlyAccess`, que permite consultar información de EC2 sin otorgar permisos administrativos generales.

```bash
aws iam attach-user-policy \
--user-name lector-jonathan \
--policy-arn arn:aws:iam::aws:policy/AmazonEC2ReadOnlyAccess
```

Se verificó la política:

```bash
aws iam list-attached-user-policies --user-name lector-jonathan
```

### Parte 4. Prueba del principio de menor privilegio

Se creó una clave de acceso para el usuario `lector-jonathan` y se configuró un perfil independiente llamado `lector` en AWS CLI.

```bash
aws configure --profile lector
```

Se comprobó la identidad:

```bash
aws sts get-caller-identity --profile lector
```

Se realizaron dos pruebas:

**Acción permitida:**

```bash
aws ec2 describe-instances --profile lector
```

La consulta se ejecutó correctamente, demostrando que el usuario tenía acceso de lectura a EC2.

**Acción denegada:**

```bash
aws iam list-users --profile lector
```

Se comprobó que el usuario no debía tener autorización para consultar los usuarios de IAM, debido a que solamente contaba con permisos de lectura sobre EC2.

### Parte 5. Retirada de permisos

Se retiró la política `AmazonEC2ReadOnlyAccess` utilizando el usuario administrador.

```bash
aws iam detach-user-policy \
--user-name lector-jonathan \
--policy-arn arn:aws:iam::aws:policy/AmazonEC2ReadOnlyAccess
```

Después se repitió la consulta de EC2 con el perfil lector:

```bash
aws ec2 describe-instances --profile lector
```

La operación dejó de estar autorizada, demostrando el efecto de retirar los permisos.

### Parte 6. Eliminación del usuario

Durante el proceso se presentó el error `DeleteConflict`, porque AWS no permite eliminar un usuario mientras conserva claves de acceso asociadas.

Se consultaron las claves:

```bash
aws iam list-access-keys --user-name lector-jonathan
```

Se eliminó la clave de acceso y posteriormente el usuario IAM.

```bash
aws iam delete-access-key \
--user-name lector-jonathan \
--access-key-id IDENTIFICADOR_DE_LA_LLAVE
```

```bash
aws iam delete-user --user-name lector-jonathan
```

Finalmente, se verificó la lista de usuarios:

```bash
aws iam list-users
```

## 3. Preguntas de reflexión

**1. ¿Qué podía hacer el usuario justo después de crearlo, antes de adjuntarle una política? ¿Por qué?**

No podía realizar acciones sobre los servicios de AWS porque inicialmente no tenía permisos asignados. Esto sucede porque IAM aplica por defecto la denegación de acceso cuando no existe una autorización explícita.

**2. En el error del paso 13, ¿qué tres datos te dice el mensaje?**

El error permite identificar el usuario que intenta realizar la operación, la acción que quiere ejecutar (`iam:ListUsers`) y el motivo por el cual AWS le niega el acceso, debido a que no cuenta con los permisos necesarios.

**3. ¿Por qué cambió el ARN al renombrar el usuario?**

Porque el nombre del usuario forma parte de su ARN. Al modificar el nombre de `dev-jonathan` a `lector-jonathan`, AWS actualizó la ruta de identificación del usuario.

**4. ¿Por qué AWS obliga a borrar las llaves antes de borrar el usuario?**

Porque las claves de acceso son credenciales asociadas al usuario. AWS exige eliminarlas primero para evitar que queden credenciales activas vinculadas a una identidad que se intenta eliminar.

**5. ¿Qué pasaría si le hubieras dado AdministratorAccess en vez de solo lectura? ¿En qué caso real estaría justificado?**

El usuario habría tenido permisos administrativos mucho más amplios para gestionar diferentes servicios y recursos de AWS, aumentando el riesgo de errores o modificaciones no autorizadas. Este permiso estaría justificado únicamente para cuentas administrativas que realmente necesiten gestionar toda la infraestructura y cuenten con controles de seguridad adecuados.

## 4. Conclusión

Durante esta semana aprendí a administrar usuarios mediante IAM y AWS CLI, realizar modificaciones en sus datos, asignar políticas y comprobar los permisos mediante diferentes perfiles.

También comprendí la importancia del principio de menor privilegio, que consiste en entregar únicamente los permisos necesarios para cumplir una función.

Finalmente, aprendí a retirar permisos, eliminar claves de acceso y borrar usuarios siguiendo el orden establecido por AWS.

## 5. Evidencias

Las capturas de pantalla y salidas de los comandos se encuentran organizadas en la carpeta `evidencias/`.

| Archivo | Evidencia |
|---|---|
| `01_usuario_creado.png` | Consulta del usuario y ARN (paso 3) |
| `02_usuario_editado.png` | Cambio de nombre y etiqueta (paso 6) |
| `03_politica_adjunta.png` | Política de EC2 adjunta (paso 8) |
| `04_acceso_permitido_ec2.png` | Consulta permitida de EC2 (paso 12) |
| `05_acceso_denegado_iam.png` | Acceso denegado a IAM (paso 13) |
| `06_permisos_retirados.png` | Consulta denegada después de retirar permisos (paso 15) |
| `07_usuario_eliminado.png` | Confirmación de eliminación (paso 19) |

**Nota de seguridad:** No se incluyen claves secretas, contraseñas ni credenciales de acceso en este documento ni en las evidencias.