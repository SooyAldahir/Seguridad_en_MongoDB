# Seguridad_en_MongoDB

## Introducción

## 1. Habilitar la Autenticacion en MongoDB

### 1.1. Ubicar el archivo mongod.cfg, es posible que lo encuentres en la siguinete ruta: 
```Bash
C:\Program Files\MongoDB\Server\8.0\bin
```
### 1.2. Ejecutar el bloc de notas con permisos de administrador.
### 1.3. Cuando se haya abierto, desde la esquina superior izquierda abriremos el archivo de configuracion de mongo.
#### 1.3.1. Seleccionamos archivo
#### 1.3.2. Abrir
#### 1.3.3. Copiamos la ruta "C:\Program Files\MongoDB\Server\8.0\bin" en el buscador del explorador de archivos.
#### 1.3.4. Para poder ver el archivo de configuracion seleccionamos en ver "Todos los archivos"
Nos debe aparecer algo como esto:
![Archivo mongod.cfg en el explorador de aerchivos](/img/mongod.png)
#### 1.3.5. Abrimos el archivo.
![Archivo mongod.cfg abierto](/img/fileMongod.png)
### 1.4. Agrega o modifica la siguiente configuracion en la seccion "Security"
``` Bash
security:
  authorization: enabled
```
### 1.5. Guarda los cambios y reinicia MongoDB desde el CMD, PowerShell, etc. como administrador con los siguientes comandos
``` Bash
net stop MongoDB
```
```Bash
net start MongoDB
```
![Deteniendo e iniciando los servicios de MongoDB](/img/image.png)

## 2. Crear Usuarios con Roles y Permisos Específicos
### 2.1. Abrimos el PowerShell como administrador
### 2.2. Nos conectamos a MongoDB con el siguiente comando:
``` Bash
mongosh
```
![Coneccion a la base de datos](/img/connection.png)
### 2.3.Por defecto nos ubicara en una base de datos de prueba, asi que nos cambiamos a la base de datos admin usando el siguiente comando:
``` Bash
use admin
```
### 2.4. Una vez estemos en la base de datos "admin", comenzaremos a crear nuestros usuarios, como primera instacia, debemos crear un usuario admin con permisos para admnistrar toda nuestra base de datos, para esto usaremos los siguientes comandos:
``` Bash
db.createUser({user: "admin",pwd: "aldemm19",roles:[{role: "root", db: "admin" }]});
```

![Creamos usuario administrador](/img/admin.png)

### 2.5. Una vez que hayamos creado nuestro usuario nos desconectaremos de la base de datos utilizando el siguiente comando:
``` Bash
exit
```
### 2.6. Para continuar con la creacion de nuestros usuarios, es necesario que nos conectemos con el usuario que creamos, aqui te dejo el comando para hacerlo, es necesario abrir el PowerShell como administrador:
``` bash
mongosh -u "admin" -p "aldemm19" --authenticationDatabase "admin"
```
![Inicio con el usuario admin](/img/loginadmin.png)
### 2.7. Contimuamos con la creacion de los usuarios, en este laboratorio crearemos tres:
- Un usuario que administre a los demas usuarios.
- Un usuario de lectura.
- Un usuario de lectura y escritura.
Procedemos a crear el usuario, despues nos desconectamos de la base de datos e inciamos con el usuarios que acabamos de crear y nos cambiamos a la base "admin", aqui te dejo el comando para crear al ususario que administrara a los demas usuarios:

``` Bash
db.createUser({user: "adminUser", pwd: "AdminPassword123!", roles:[{ role: "userAdminAnyDatabase", db: "admin" }]});
```
![Creacion del usuario administrador](/img/userAdmin.png)

### 2.8. Creamos los usuarios que hacen falta, aqui te dejo los comandos:
- Hay que tener en consideracion que debemos cambiarnos a la base de datos en la que queremos crear el usuario, yo utilizare una base de datos llamada "Optimization", en la cual ya tengo datos agregados con los que puedo mostrar el funcionamiento.

- Para crear el usuario de lectura
```Bash
use Optimization;
db.createUser({user: "readUser", pwd: "ReadPassword123!", roles:[{ role: "read", db: "Optimization" }]});
``` 

- Para crear el usuario de lectura y escritura
```Bash
db.createUser({user: "writeUser", pwd: "WritePassword123!",roles:[{ role: "readWrite", db: "Optimization" }]});
``` 
![Cracion de los usuarios de lectura y de lectura y escritura](/img/readWriteUsers.png)



