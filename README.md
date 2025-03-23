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
