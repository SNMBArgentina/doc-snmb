# Instalación del Portal de Diseminación en servidor Ubuntu 18.04

El Portal de Diseminación se basa en una serie de componentes que deben estar configurados e instalados en el servidor. Al tratarse de una aplicación desarrollada usando la tecnología Java, lo primero que deberemos tener instalado y configurado en el servidor es un contenedor de aplicaciones Tomcat. 

La instalación y configuración del contenedor de aplicaciones escapa al propósito de este documento. Aun así referenciamos a documentación que será de ayuda en el caso de tener que realizar la instalación del contenedor en el servidor.

## Instalación de Tomcat en Ubuntu 18.04

**IMPORTANTE**: La versión de Tomcat usada en el portal es la 8.5

Como prerequisitos deberemos tener instalado el OpenJDK 11 en nuestro servidor:

```bash
$ java --version
bash: java: command not found
```

en caso de que aparezca el mensaje anterior, realizaremos la instalación de la versión 11 de Java:

```bash
$ apt install openjdk-11-jre
```

tras lo cual comprobaremos que la versión instalada es la correcta:

```bash
$ java --version
openjdk 11.0.7 2020-04-14
OpenJDK Runtime Environment (build 11.0.7+10-post-Ubuntu-2ubuntu218.04)
OpenJDK 64-Bit Server VM (build 11.0.7+10-post-Ubuntu-2ubuntu218.04, mixed mode, sharing)

```

Una vez instalado Java correctamente seguiremos con la instalación del contenedor de aplicaciones Tomcat.

```bash
$ apt install tomcat8
```
Una vez instalado y configurado Tomcat correctamente, deberíamos poder acceder a:

[http://`<IP_DEL_SERVIDOR>`:8080](http://<IP_DEL_SERVIDOR>:8080)

## Instalación del Portal de Diseminación

El Portal de Diseminación se compone de una aplicación servidor desarrollada en Java y que deberá instalarse en el Tomcat recien instalado en el servidor. Para ello podemos descargar el archivo ```war``` del repositorio de integración continua o podemos compilar una versión propia del portal.

Una copia del `war` instalable se puede descargar desde [aquí](https://cloud.geomati.co/s/Hm79DPMqAYHSDLx)

El archivo `war` que descarguemos deberemos depositarlo en la carpeta `webapps` de nuestra instalación de Tomcat.

Seguidamente deberemos descargar el [directorio de configuración del Portal de Diseminación](https://github.com/SNMBArgentina/app-snmb/archive/v1.0.0.zip). El Directorio de Configuración del portal es donde se encuentra toda la configuración de las capas, accesos a base de datos, usuarios, etc. Para más información sobre este Directorio puede revisar la [documentación de usuario](config.md)

Una vez descargado y descomprimido, deberemos realizar las configuraciones necesarias para que la aplicación haga uso del directorio de configuración. En la documentación de configuración se indica como realizar el proceso. Lo principal será definir la variable de entorno `GEOLADRIS_CONFIG_DIR` a la ruta donde hayamos descomprimido el directorio de configuración.

* Variable de entorno: export GEOLADRIS_CONFIG_DIR=/var/geoladris.
* Propiedad Java: -DGEOLADRIS_CONFIG_DIR=/var/geoladris.

Una manera de setear las variables de entorno es crear un archivo `setenv.sh` en la carpeta `<TOMCAT_DIR>/bin` en incluir en el las variables que necesitamos.

Una vez que hemos definido la variable de entorno en Tomcat deberemos instalar los plugins necesarios para el Portal de Diseminación. Los plugins se pueden descargar desde [aquí](https://cloud.geomati.co/s/DyTpMRZMyipRrF7) y descomprimir dentro de la carpeta del directorio de configuración de la manera `<GEOLADRIS_CONFIG_DIR>/plugins`.

### Conexión a la base de datos

Una parte de la funcionalidad del portal se apoya en el uso de una base de datos PostGIS. Deberemos tener instalado en el servidor el sistema gestor de base de datos PostGIS.

Una vez instalado y configurado los usuarios y accesos crearemos un esquema y una base de datos que utilizaremos en el portal. La manera de conectar el portal a la base de datos es mediante, primero el uso de variables de entorno, incluyendo las necesarias en el archivo `setenv.sh` [Configuracion de la Base de datos](config.md#base-de-datos).

En el Directorio de configuración debe existir un archivo `portal.properties` donde definimos el nombre del esquema donde se encuentran las tablas necesarias para el portal `db-schema=portal_redd`.

La copia de la base de datos deberá ser suministrada por el departamento responsable.

### Gestión de los usuarios

Los usuarios y roles del sistema serán definidos mediante la configuración de dos archivos. Por un lado el archivo con nombre `<role>.json` dentro de la carpeta `role_conf` y los usuarios definidos en Tomcat en el archivo `<TOMCAT_DIR>/conf/tomcat-users.xml`. En el archivo `<CONFIG_DIR>/role_conf/<role>.json` incluiremos los permisos de los roles a los diferentes plugins, en la manera:

```json
{
	"layer-order" : {
		"_enabled" : true
	},
	"layers-editor" : {
		"_enabled" : true
	}
}
```

en este caso se le dará acceso a los plugins `layer-order` y `layer-editor` al rol `viewer`.

Y en la configuración de usuarios de Tomcat, modificaremos el archivo `tomcat-user.xml` de la siguiente manera:

```xml
<tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
  <role rolename="viewer"/>
  <user username="demo" password="demo" roles="viewer"/>
</tomcat-users>
```

donde `rolename=viewer` relaciona el nombre del archivo en la carpeta `<CONFIG_DIR>/role_conf/<role>.json` con el rol en Tomcat.

Para comprobar que la gestión de usuarios está funcionando correctamente, deberemos acceder al login con las credenciales que hayamos indicado en el archivo `<TOMCAT_DIR>/conf/tomcat-users.xml`, en este caso `demo:demo`.

## Resumen de instalación

Esta instalación está realizada con el usuario de Ubuntu `root`. En caso de usar otro usuario se deberán adaptar los comandos mediante el uso de `sudo` así como los directorios de las carpetas

```
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list

apt update

apt install openjdk-11-jre unzip tomcat8 postgresql-10 postgresql-10-postgis-2.4 yarn

wget -O app-snmb.zip https://github.com/SNMBArgentina/app-snmb/archive/v1.0.0.zip

unzip app-snmb.zip

mv app-snmb-1.0.0/ /srv/app-snmb

cd /srv/app-snmb

wget -O plugins.zip https://github.com/SNMBArgentina/plugins/archive/7.1.x.zip

unzip plugins.zip

mv plugins-7.1.x/ plugins

rm -rf plugins.zip

cd plugins

for i in `ls */package.json`; do
   pushd `dirname $i`
   yarn
   popd
done

chown -R tomcat8:tomcat8 /srv/app-snmb/

# Tener en cuenta el archivo `plugin_imports.css` en la carpeta `static`

cd /var/lib/tomcat8/webapps

wget -O app-snmb.war https://cloud.geomati.co/s/iKy6L65dgHe2ryS/download

cat > /usr/share/tomcat8/bin/setenv.sh <<EOL
export JAVA_OPTS="$JAVA_OPTS -DGEOLADRIS_CONFIG_DIR=/srv -DGEOLADRIS_DB_URL=jdbc:postgresql://localhost:5432/geoladris -DGEOLADRIS_DB_USER=geoladris -DGEOLADRIS_DB_PASS=geoladris"
EOL # IMPORTANTE el nombre del war deberá ser el mismo que el nombre del directorio de configuración, en este caso app-snmb

su postgres

createuser -sPE geoladris
createuser -PE snmb

psql -d postgres

postgres=# CREATE DATABASE geoladris WITH OWNER geoladris;

postgres=# \c geoladris

geoladris=# create extension postgis;

geoladris=# \q

wget -O geoladris.sql https://cloud.geomati.co/s/66kj7PsZHmGiM5H/download

pg_restore -U geoladris -W geoladris.sql

cat > /etc/tomcat8/tomcat-users.xml <<EOF
<?xml version="1.0" encoding="UTF-8"?>
<tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd" version="1.0">
  <role rolename="viewer"/>
  <user username="user" password="pass" roles="viewer"/>
</tomcat-users>
EOF 

service tomcat8 restart
```
