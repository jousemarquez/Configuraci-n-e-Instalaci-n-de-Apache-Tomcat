# Configuración-e-Instalación-de-Apache-Tomcat
Este repositorio proporciona instrucciones detalladas para la instalación y configuración de Apache Tomcat en un entorno Linux. Además, se incluyen pasos para desplegar proyectos web y configurar un certificado SSL autogenerado para habilitar la conexión segura a través del protocolo HTTPS.

## Instalación de Tomcat

1. Acceda al sistema con permisos de root:

sudo su -

2. Actualice el sistema y aplique las actualizaciones de seguridad:

apt update
apt upgrade

3. Instalar Java, Tomcat 9 y sus documentos, incluyendo el administrador. Se puede instalar algunos ejemplos también:

apt-get install default-jdk
apt-get install tomcat9
apt-get install tomcat9-docs tomcat9-examples tomcat9-admin

4. Se puede gestionar el estado del servidor Tomcat:

service tomcat9 status    # start | restart | stop

## Despliegue de Proyectos

1. Configurar los usuarios del administrador en el archivo tomcat-users.xml:

cd /var/lib/tomcat9/conf
nano tomcat-users.xml

2. Desplieguar proyectos utilizando el administrador y Filezilla.
 - Despliegue a través del administrador:
    - Se accede al administrador con las credenciales configuradas.
    - Subir el archivo .war y desplegarlo.

 - Despliegue a través de Filezilla:
    - Subir el archivo .war a /etc/tomcat9/webapps.
    - Mover el archivo a la carpeta de destino con mv path/archivo.war .

3. Si se quiere que el proyecto sea accesible directamente desde la raíz, renombrar la carpeta ROOT:

mv ROOT ROOTCOPY
mv nombreproyecto ROOT.war

Ahora, el proyecto estará en url:puerto.

4. Configurar puertos (por defecto he usado el 8080) y otras opciones en /var/lib/tomcat9/server.xml.

## Certificado SSL Autogenerado

1. Generar un certificado usando keytool:

keytool -genkey -keyalg RSA -alias tomcat -keystore selfsigned.jks -validity 365 -keysize 2048

2. Verificar la generación del certificado
   
keytool -list -v -keystore selfsigned.jks

3. Exportar el certificado a un archivo .cer:

keytool -export -keystore selfsigned.jks -storepass <password> -alias tomcat -file selfsigned.cer

4. Importar el certificado al trustore en /etc/ssl/certs:

keytool -import -noprompt -trustcacerts -alias tomcat -file selfsigned.cer -keystore /etc/ssl/certs -storepass <password>

5. Verifique la instalación del certificado:

keytool -list -keystore /etc/ssl/certs/cacerts -storepass <password>

6. Colocar el certificado en /var/lib/tomcat9/conf y configure el conector SSL en server.xml.

Nota: Al utilizar el puerto 8443, el navegador puede mostrar que el certificado no es válido, pero puede confirmar que los datos coinciden con la configuración realizada.

```<Connector port="8443" protocol="org.apache.coyote.http11.Http11NioProtocol" maxThreads="150" SSLEnabled="true">
   <SSLHostConfig>
      <Certificate certificateKeystoreFile="conf/selfsigned.jks" certificateKeystorePassword="tomcatkey" type="RSA" />
   </SSLHostConfig>
</Connector>
