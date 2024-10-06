# Cypher Graph

Este repositorio contiene ejemplos y ejercicios realizados como parte de la materia **Seminario Intensivo de Tópicos Avanzados en Datos Complejos** - Comisión: **ECD.2023.B**. 

## Instalación en Ubuntu

Para ejecutar el código de este repositorio, es necesario tener instalado Neo4j

#### Paso 1: Actualizar los paquetes de Ubuntu

```bash
sudo apt update 
sudo apt upgrade
```

#### Paso 2: Instalar Java

Neo4j requiere Java para ejecutarse. Neo4j es compatible con las versiones 8, 11 y 17 de OpenJDK. En la clase usamos la versión 17.

```bash
sudo apt install openjdk-17-jdk
sudo apt install openjdk-17-jre
```

#### Paso 3: Descargar e instalar Neo4j

Descargar la última versión de Neo4j *Community* desde `https://neo4j.com/deployment-center/` e instalar el paquete:

```bash
sudo dpkg -i neo4j_5.23.0_all.deb
```

#### Paso 4: Iniciar el servicio de Neo4j

Una vez completada la instalación, iniciar Neo4j como un servicio y haz que arranque automáticamente con el sistema:

```bash
sudo systemctl enable neo4j
sudo systemctl start neo4j
```

Para verificar que Neo4j se esté ejecutando correctamente, usa el siguiente comando:

```bash
sudo systemctl status neo4j
```

#### Paso 5: Acceder a Neo4j

Acceder a la interfaz web de **Neo4j Browser** desde el navegador. Por defecto, estará disponible en la siguiente URL:

```
http://localhost:7474
```

La primera vez que inicies sesión, Neo4j te pedirá que establezcas una contraseña para el usuario `neo4j`. El nombre de usuario predeterminado es `neo4j` y la contraseña inicial es también `neo4j`. Después de iniciar sesión, te solicitará cambiar la contraseña.

#### Paso 6: Cargar la base de datos de la materia

**IMPORTANTE:** Si el archivo es un dump, no colocar la palabra .dump en el comando

```bash
sudo systemctl stop neo4j
sudo neo4j-admin database load --verbose --from-path="/home/matias/Downloads/" telco.db
sudo systemctl start neo4j
```

#### Paso 7: Darle permisos a la nueva DB (opcional)

Posiblemente no te haya aparecido la base de datos nueva por problemas de permisos. Hay que aplicar los siguientes comandos

```bash
sudo systemctl stop neo4j
sudo chown -R neo4j:neo4j /var/lib/neo4j/data/databases/telco.db
sudo chown -R neo4j:neo4j /var/lib/neo4j/data/transactions/telco.db
sudo chmod -R 755 /var/lib/neo4j/data/databases/telco.db
sudo chmod -R 755 /var/lib/neo4j/data/transactions/telco.db
sudo systemctl start neo4j
```

#### Paso 8: Configuración adicional (opcional)

Para que arranque neo4j con la DB de telco hay que colocar el siguiente texto en el archivo neo4j.conf

```bash
sudo nano /etc/neo4j/neo4j.conf
```

Y poner:

```
# The name of the default database
initial.dbms.default_database=telco.db
```

## Instalar plugins

Mueve el archivo JAR al directorio `plugins`.Si descargaste el archivo en tu directorio de descargas, por ejemplo, puedes moverlo de esta manera:

```bash
sudo mv /ruta/del/plugin.jar /var/lib/neo4j/plugins/
```

Modifica el archivo de configuración de Neo4j (`neo4j.conf`) para habilitar el plugin, si es necesario. Para habilitar el plugin, agrega o modifica la configuración correspondiente. Por ejemplo, para habilitar APOC:

```bash
# A comma separated list of procedures and user defined functions that are allowed
# full access to the database through unsupported/insecure internal APIs.
#dbms.security.procedures.unrestricted=gds.,my.extensions.example,my.procedures.
dbms.security.procedures.unrestricted=gds.*,apoc.*

# A comma separated list of procedures to be loaded by default.
# Leaving this unconfigured will load all procedures found.
#dbms.security.procedures.allowlist=apoc.coll.,apoc.load.,gds.*
dbms.security.procedures.allowlist=apoc.*,gds.*
```

Guarda los cambios y reinicia Neo4j para que se cargue el plugin:

```bash
sudo systemctl restart neo4j
```
