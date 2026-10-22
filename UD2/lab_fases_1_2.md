# Laboratorio fases 1 y 2 — Estudio Torrent | putty >> vt100 192.168.56.2 >> conectar meta


# sudo nmap -sV 192.168.10.11 -oN antes.txt

sudo: Ejecuta el escaneo con privilegios de superusuario.

-sV: Escanea los puertos abiertos para determinar qué servicio y versión exacta se está ejecutando en cada uno.

192.168.10.11: Dirección IP del equipo objetivo.

-oN antes.txt: Guarda la salida en formato de texto plano en el archivo antes.txt.

<img width="755" height="484" alt="image" src="https://github.com/user-attachments/assets/1e658cdd-c377-49d6-b1b3-8d1ff3fc2c65" />

# sudo netstat -tulpn

-t: Muestra conexiones TCP.

-u: Muestra conexiones UDP.

-l: Filtra solo los sockets que están en estado LISTEN (escuchando conexiones entrantes).

-p: Muestra el PID (Identificador de Proceso) y el nombre del programa/dueño.

-n: Muestra direcciones y números de puertos en formato numérico (evita la resolución DNS y muestra puertos como 21 en vez de ftp).

Para ver quien es el puerto nos fijamos despues de: 0.0.0.0:nºproceso 

A la derecha del PID veremos el nombre del Dueño

<img width="858" height="943" alt="image" src="https://github.com/user-attachments/assets/eb338901-aaa3-4bba-807a-41106731fe72" />


# Tenemos 2 tipos de servicio: permanentes (proceso propio) y a demanda (inetd/xinetd)
<img width="619" height="508" alt="image" src="https://github.com/user-attachments/assets/7c4d506d-5cb9-4d4b-82c2-47c9a56bb8e1" />

# Resumen
<img width="858" height="205" alt="image" src="https://github.com/user-attachments/assets/e9374216-952a-4c68-9f76-1fe8d223a971" />



# Los servicios gestionados por xinetd tienen archivos de configuración individuales en la carpeta /etc/xinetd.d/

Vamos a cerrar el puerto 21 vsftpd

<img width="390" height="61" alt="image" src="https://github.com/user-attachments/assets/a9c04641-7f55-4b65-8455-e53a8b30cc5d" />

# Editamos el archivo >> disable = yes
<img width="596" height="304" alt="image" src="https://github.com/user-attachments/assets/e1b8232f-1d6b-4fb6-a97a-aca490e149c8" />

# Reiniciamos con sudo /etc/init.d/xinetd reload

Importante no detener xinetd ya que este gestiona múltiples servicios a la vez, con reload liberas el puerto sin interrumpir lso demas.

<img width="490" height="48" alt="image" src="https://github.com/user-attachments/assets/6c442e2a-7638-4e82-b4c4-fc40db0eb84a" />

# Los servicios administrados por inetd comparten un único archivo /etc/inetd.conf

vamos a cerrar el puerto 23, editamos el archivo en /etc/inetd.conf

<img width="421" height="23" alt="image" src="https://github.com/user-attachments/assets/2c15bdb6-cfc3-4a92-8382-547851cc4717" />

# Agregamos # delante de telnet que es el servicio que vamos a cerrar

Al recargar el servicio, se ignoraran las líneas comentadas con # y se dejara de escuchar el servicio.

<img width="754" height="159" alt="image" src="https://github.com/user-attachments/assets/25612027-d363-48c5-9839-19524ea9e082" />

# Reiniciamos con sudo /etc/init.d/xinetd reload

Comprobamos que se cerro correctamente filtrando con grep y el numero de puerto: sudo netstat -tulpn | grep:23

<img width="572" height="92" alt="image" src="https://github.com/user-attachments/assets/e18dc895-b808-4211-bcb0-e44a7d2f206a" />

# Vamos a cerrar el puerto 513 login, podemos filtrar con grep para buscar el dueño
<img width="771" height="50" alt="image" src="https://github.com/user-attachments/assets/077e4b54-7c13-443b-8ff4-98be2b9fb46b" />

# Buscamos el puerto 513
No nos devolverá ningún resultado porque el nombre del protocolo será otro
<img width="881" height="46" alt="image" src="https://github.com/user-attachments/assets/049058ba-98f1-470a-baa1-fad6e9cc28a3" />

# Buscamos el nombre del puerto con /etc/services

El nombre es login, aparece en la primera columna de la izquierda

<img width="438" height="55" alt="image" src="https://github.com/user-attachments/assets/5f5623f5-616d-4c25-8a7a-d4ab38e2ac5e" />

# Ponemos # delante de login
<img width="769" height="179" alt="image" src="https://github.com/user-attachments/assets/50875fd8-7be8-4099-97dd-06cc7bf78b9a" />

# Ahora cerramos un servicio permanente, el programa base se encontrara en /etc/init.d/, las carpetas de arranque (runleves) 7 en total se encontraran en /etc/rc0.d/ hasta /etc/rc6.d/ siendo /etc/rc2.d/ la más común.

Vamos a cerrar el puerto 2121, buscamos quien es el dueño

<img width="831" height="37" alt="image" src="https://github.com/user-attachments/assets/91a25044-dc6e-455c-92c8-151f2dfd5088" />

# Para detener el servicio: sudo /etc/init.d/proftpd stop

Con stop detendremos el proceso activo en memoria para liberar el puerto inmediatamente.

<img width="722" height="52" alt="image" src="https://github.com/user-attachments/assets/b358aaea-bada-4fae-bee6-ec0530814255" />

# Para deshabilitar: sudo update-rc.d -f proftpd remove

Eliminamos el acceso directo que inicia el programa al encender el equipo.

<img width="587" height="162" alt="image" src="https://github.com/user-attachments/assets/d6126d4d-157e-46af-ab4c-0ed8bc0dc6a2" />

# Vamos a detener el puerto 3306 buscamos el dueño
<img width="717" height="97" alt="image" src="https://github.com/user-attachments/assets/a36a4df9-4c0d-4cba-b07d-931e280ad0bc" />


# Buscamos el rc
<img width="446" height="85" alt="image" src="https://github.com/user-attachments/assets/5752c251-7ba5-4e35-a42e-4ffda9e59b4e" />


<img width="603" height="169" alt="image" src="https://github.com/user-attachments/assets/5677215c-4bc7-454c-b881-e4b962041c78" />

# Repetimos el proceso anterior: sudo update-rc.d -f mysql remove
<img width="714" height="67" alt="image" src="https://github.com/user-attachments/assets/c5e283ce-4836-4493-9bd3-d2df29badc52" />




## B. Antes

| PORT | STATE | SERVICE | VERSION |
|---|---|---|---|
| 21/tcp | open | ftp | vsftpd 2.3.4 |
| 22/tcp | open | ssh | OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0) |
| 23/tcp | open | telnet | Linux telnetd |
| 25/tcp | open | smtp | Postfix smtpd |
| 53/tcp | open | domain | ISC BIND 9.4.2 |
| 80/tcp | open | http | Apache httpd 2.2.8 ((Ubuntu) DAV/2) |
| 111/tcp | open | rpcbind | 2 (RPC #100000) |
| 139/tcp | open | netbios-ssn | Samba smbd 3.X - 4.X (workgroup: WORKGROUP) |
| 445/tcp | open | netbios-ssn | Samba smbd 3.X - 4.X (workgroup: WORKGROUP) |
| 512/tcp | open | exec | netkit-rsh rexecd |
| 513/tcp | open | login | Netkit rlogind |
| 514/tcp | open | shell | Netkit rshd |
| 1099/tcp | open | java-rmi | GNU Classpath grmiregistry |
| 1524/tcp | open | bindshell | Metasploitable root shell |
| 2049/tcp | open | nfs | 2-4 (RPC #100003) |
| 2121/tcp | open | ftp | ProFTPD 1.3.1 |
| 3306/tcp | open | mysql | MySQL 5.0.51a-3ubuntu5 |
| 5432/tcp | open | postgresql | PostgreSQL DB 8.3.0 - 8.3.7 |
| 5900/tcp | open | vnc | VNC (protocol 3.3) |
| 6000/tcp | open | X11 | (access denied) |
| 6667/tcp | open | irc | UnrealIRCd |
| 8009/tcp | open | ajp13 | Apache Jserv (Protocol v1.3) |
| 8180/tcp | open | http | Apache Tomcat/Coyote JSP engine 1.1 |

## B. Después

<img width="773" height="128" alt="image" src="https://github.com/user-attachments/assets/ba59ef32-4756-450d-95da-d18b75d65d06" />


| Puerto | Servicio | ¿Necesario? | Decisión / Método aplicado |
| :---: | :---: | :---: | :--- |
| 21/tcp | ftp (vsftpd 2.3.4) | No | Deshabilitar en `/etc/xinetd.d/vsftpd` (`disable = yes`) y recargar `xinetd`. |
| 22/tcp | ssh (OpenSSH 4.7p1) | Sí | **MANTENER**. Necesario para administración remota según la suposición de trabajo. |
| 23/tcp | telnet (Linux telnetd) | No | Deshabilitar en `/etc/xinetd.d/telnet` (`disable = yes`) y recargar `xinetd`. |
| 25/tcp | smtp (Postfix smtpd) | No | Detener (`sudo /etc/init.d/postfix stop`) y deshabilitar del arranque (`update-rc.d -f postfix remove`). |
| 53/tcp | domain (ISC BIND 9.4.2) | No | Detener (`sudo /etc/init.d/bind9 stop`) y deshabilitar del arranque (`update-rc.d -f bind9 remove`). |
| 80/tcp | http (Apache httpd 2.2.8) | Sí | **MANTENER**. Necesario para ofrecer servicio web según la suposición de trabajo. |
| 111/tcp | rpcbind | No | Detener (`sudo /etc/init.d/portmap stop`) y deshabilitar del arranque (`update-rc.d -f portmap remove`). |
| 139/tcp | netbios-ssn (Samba) | No | Detener (`sudo /etc/init.d/samba stop`) y deshabilitar del arranque (`update-rc.d -f samba remove`). |
| 445/tcp | netbios-ssn (Samba) | No | Deshabilitado conjuntamente con el servicio Samba. |
| 512/tcp | exec (netkit-rsh rexecd) | No | Comentar entrada correspondiente en `/etc/inetd.conf` y recargar portero. |
| 513/tcp | login (Netkit rlogind) | No | Comentar entrada correspondiente en `/etc/inetd.conf` y recargar portero. |
| 514/tcp | shell (Netkit rshd) | No | Comentar entrada correspondiente en `/etc/inetd.conf` y recargar portero. |
| 1099/tcp | java-rmi (GNU Classpath) | No | Detener proceso asociado en memoria y eliminar script de inicio. |
| 1524/tcp | bindshell (Metasploitable) | No | Finalizar proceso en memoria (`kill -9 <PID>`). Puerta trasera innecesaria. |
| 2049/tcp | nfs | No | Detener (`sudo /etc/init.d/nfs-kernel-server stop`) y deshabilitar del arranque. |
| 2121/tcp | ftp (ProFTPD 1.3.1) | No | Detener (`sudo /etc/init.d/proftpd stop`) y deshabilitar del arranque. |
| 3306/tcp | mysql (MySQL 5.0.51a) | No | Detener (`sudo /etc/init.d/mysql stop`) y deshabilitar del arranque (`update-rc.d -f mysql remove`). |
| 5432/tcp | postgresql (PostgreSQL 8.3) | No | Detener (`sudo /etc/init.d/postgresql-8.3 stop`) y deshabilitar del arranque (`update-rc.d -f postgresql-8.3 remove`). |
| 5900/tcp | vnc | No | Detener servicio de escritorio remoto VNC. |
| 6000/tcp | X11 | No | Deshabilitar servidor gráfico X11. |
| 6667/tcp | irc (UnrealIRCd) | No | Detener (`sudo /etc/init.d/unrealircd stop`) y deshabilitar del arranque. |
| 8009/tcp | ajp13 (Apache Jserv) | No | Detener o deshabilitar dentro del servicio contenedor Tomcat. |
| 8180/tcp | http (Apache Tomcat 1.1) | No | Detener (`sudo /etc/init.d/tomcat5.5 stop`) y deshabilitar del arranque. |



## Reflexión

¿Qué contramedida ha reducido más lo que ve el atacante? ¿Por qué? La contramedida que más reduce la información que puede ver el atacante es deshabilitar y detener los servicios que no son necesarios.

Al cerrar los puertos innecesarios, el atacante encuentra muchos menos servicios al realizar un escaneo de puertos.
Si solo ocultáis un banner pero el servicio sigue activo, ¿la vulnerabilidad sigue ahí? Razonad la respuesta. Sí. Ocultar el banner no elimina la vulnerabilidad del servicio este sigue ejecutandose.

Torrent-Vulnerable usa un sistema operativo sin soporte desde hace años. ¿Puede una contramedida de estas sustituir a actualizarlo? ¿Qué haríais en una empresa real?
No. Las contramedidas pueden reducir el riesgo, pero no sustituyen a actualizar o sustituir un sistema operativo sin soporte.
Habría que hacer una copia de seguridad y migrar el servicio a un entorno más seguro.

De todas las contramedidas de las partes A y B, ¿cuáles evitan que el atacante encuentre información y cuáles solo ayudan a detectar que lo están intentando? (Aún no las hemos aplicado todas, pero pensad en cuáles serían de cada tipo.)
Deshabilitar servicio ayudan a que el atacante no encuentre información.
