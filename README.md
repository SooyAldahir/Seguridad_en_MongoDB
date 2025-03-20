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
![Archivo mongod.cfg abierto](/img/fileMongod.png)![alt text](image.png)
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