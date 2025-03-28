﻿# Seguridad_en_MongoDB

## Introducción
El manejo de la seguridad en MongoDB es un aspecto crucial para garantizar la integridad y protección de los datos almacenados en la base de datos. Este documento proporciona una guía paso a paso para habilitar la autenticación en MongoDB, crear usuarios con roles específicos, y asignar permisos adecuados según las necesidades de administración y acceso a los datos.

En este manual, se cubrirá cómo habilitar la autenticación en MongoDB editando su archivo de configuración, cómo crear un usuario administrador con privilegios completos, y cómo crear otros usuarios con diferentes niveles de acceso, tales como usuarios con permisos de lectura y escritura o solo lectura. Además, se valida el funcionamiento de estos usuarios con ejemplos prácticos para comprobar que sus permisos se aplican correctamente.

Este proceso asegura que solo los usuarios autorizados puedan acceder a las bases de datos y realizar operaciones, lo cual es fundamental para proteger la información sensible y mantener la seguridad en el entorno de trabajo con MongoDB.

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
### 2.3. Por defecto nos ubicara en una base de datos de prueba, asi que nos cambiamos a la base de datos admin usando el siguiente comando:
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

### 2.9. Validacion para el usuario de lectura.
- Aqui nos conectaremos a la base de datos con el usuario de lectura que creamos en el paso anterior, aqui te dejo el comando para hacerlo:
``` Bash
mongosh -u "readUser" -p "ReadPassword123!" --authenticationDatabase "Optimization"
```
Como ya sabemos, al conectarnos a una base de datos en MongoDB nos ubica en una base de pruebas (test), asi que nos cambiamos a la base de datos en la que tenemos permisos para leer (Optimization).
``` Bash
use Optimization
```
La base de datos en la que cree los usuarios utiliza el siguiente esquema:
```javascript
const productSchema = new mongoose.Schema({
    name: { type: String, required: true },
    category: { type: String, required: true },
    price: { type: Number, required: true },
    stock: { type: Number, required: true },
    date: { type: Date, required: true }
});
```
Por lo que para insertar datos quedaria de esta manera:
```json
db.products.insertOne({ name: "Producto Ejemplo", category: "electronica", price: 1200, stock: 10, date: new Date()})
```
![Usando el usuario de lectura](/img/readUserExample.png)

En la imagen se puede obeservar que nos conectamos con el usuario de lectura, se hicieron cuatro consultas:
- Listar todas las bases de datos.
- Agregar un nuevo producto.
- Eliminar un producto.
- Listar todos los productos.

La primer consulta nos permitio ver todas las bases de datos, en este caso solo tenemos acceso a una.
La segunda y tercera consulta nos arrojaron el error "MongoServerError[Unauthorized]", el cual nos dice claramente que el usuario no tiene autorizacion para realizar esas consultas.
En cambio la cuarta consulta nos mostro los primeros veinte prodcutos que tenemos almacenados en esa coleccion.

Con este ejercicio queda validado que el usuario "readUser" solo puede ver lo que contine la base de datos, no puede agregar, actualizar y mucho menos eliminar algo de la base de datos.

### 2.9. Validacion para el usuario de lectura y escritura.
- Nos conectaremos a la base de datos con el usuario de lectura y escritura que creamos en el paso anterior, aqui te dejo el comando para hacerlo:
``` Bash
mongosh -u "writeUser" -p "WritePassword123!" --authenticationDatabase "Optimization"
```

Aqui tambien haremos cuatro consiltas:
- Listar todas las bases de datos.
- Agregar un nuevo producto.
- Eliminar un producto.
- Listar todos los productos.

![Usando el usuario para lectura y escritura](/img/readWriteUserExample.png)

Al crear un usuario con permisos de escritura, se puede insertar, actualizar, eliminar y realizar otras operaciones de escritura en las colecciones de esa base de datos, por eso no nos marco ningun error la base de datos. 
Comprobando con estas consultas lo que el usuario "WriteUser" puede hacer en la base de datos.

## 3. Configurar conexiones seguras con SSL/TLS

### 3.1. Obtener o crear los certificados SSL/TLS

- Si no tienes OpenSSL instalado, puedes descargarlo desde la siguiente ruta:
``` bash
https://slproweb.com/products/Win32OpenSSL.html
```

### 3.2. Agregar OpenSSL al PATH en Windows
#### 3.2.1 Encontrar la ruta de instalacion de OpenSSL, generalmente, OpenSSL se intala en una ruta como la siguiete:
```Bash
C:\Program Files\OpenSSL-Win64\bin
```
![Ruta de OpenSSL](/img/routeOpenSSL.png)

#### 3.2.2. Agregar OpenSSL al PATH

- Abre el Panel de Control de Windows y ve a Sistema y seguridad > Sistema.

- Haz clic en Configuración avanzada del sistema (en la parte izquierda).

- En el cuadro de diálogo Propiedades del sistema, haz clic en Variables de entorno.

- En las Variables del sistema, busca la variable llamada Path y haz clic en Editar.

- Haz clic en Nuevo e ingresa la ruta del directorio donde se encuentra openssl.exe
``` Bash
C:\Program Files\OpenSSL-Win64\bin
```

- Haz clic en Aceptar para cerrar todas las ventanas.

![PATH OpenSSL](/img/pathOpenSSL.png)

#### 3.2.3. Verificar que OpenSSL este en el PATH.

- Abre una nueva ventana de PowerShell o Símbolo del sistema.

- Escribe el siguiente comando para verificar si OpenSSL está correctamente instalado:
```Bash
openssl version
```
![OpenSSL Version](/img/OpenSSLversion.png)

### 3.3. Una vez agregada la variable de entorno, abrimos el CMD como administrador y ejecutamos los siguientes comandos:
``` Bash
openssl req -newkey rsa:2048 -new -x509 -days 365 -nodes -out C:\mongodb-cert.pem -keyout C:\mongodb-key.pem
```

Estos comandos generan dos archivos:
- mongodb-key.pem: clave privada.
- mongodb-cert.pem: certificado.

![Certificado OpenSSL](/img/OpenSSLCertificated.png)

### 3.4. Convina el certificado y la clave privada en un solo archivo .pem:
``` Bash
type C:\mongodb-key.pem C:\mongodb-cert.pem > C:\mongodb.pem
```

![Archivos convinados](/img/CertificatedFile.png)

### 3.5. Configurar MongoDB para usar SSL

#### 3.5.1. Abrir el archivo mongod.cfg y agregar lo siguiente:
```Bash
net:
 ssl:
  mode: requireSSL
  PEMKeyFile: C:\certificados\mongodb.pem
```

### 3.6. Reinicia MongoDB para aplicar los cambios:
``` Bash
net stop mongodb
```

``` Bash
net start mongodb
```

Esta parte del manual no pudo completarse debido a que cuando se habilita lo que es la seguridad en el documeto .cfg, el servicio de mongodb no responde, aun desconozco el porque ocurre esto, y no solo pasa en mi equipo, tambien le pasa a mis compañeros, posiblemente estamos haciendo algo mal y no nos estamos dando cuenta, o tal vez sea porque OpenSSL funciona mejor en linux que en Windows, no se... pero espero pronto lograr encontrar el problema que se presentó.

## Conclusion
La implementación de la autenticación y la gestión de usuarios en MongoDB es una medida esencial para garantizar la seguridad y control sobre el acceso a los datos. A través de la habilitación de la autenticación, la creación de usuarios con roles y permisos específicos, y la validación de sus accesos, se asegura que solo aquellos usuarios con la debida autorización puedan realizar operaciones críticas sobre la base de datos.

Este proceso no solo protege los datos de accesos no autorizados, sino que también permite una administración más eficiente y segura del entorno de trabajo en MongoDB. Con una correcta configuración de seguridad, es posible prevenir posibles vulnerabilidades y gestionar de manera efectiva los privilegios de acceso según las necesidades del equipo o proyecto, manteniendo la integridad y confidencialidad de la información almacenada.
