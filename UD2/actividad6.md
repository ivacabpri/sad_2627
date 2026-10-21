# Parte A · ¿Está actualizado el sistema?

A1. Anotad qué versión de sistema operativo y kernel tenéis:

lsb_release -a
uname -r

<img width="378" height="181" alt="image" src="https://github.com/user-attachments/assets/9a9e9a8c-0cd1-456b-b228-fd8584d1bef2" />

A2. Pedid al sistema la lista de paquetes disponibles y qué se puede actualizar:

sudo apt update
apt list --upgradable
Anotad: ¿cuántos paquetes tienen actualización pendiente?

A3. Simulad la actualización sin aplicarla:

sudo apt-get -s upgrade | head -30
La opción -s es "simular". Fijaos en que dice qué instalaría, pero no toca nada.

<img width="800" height="731" alt="image" src="https://github.com/user-attachments/assets/b9539c4a-42f4-4dba-9067-89ca1d92a3b7" />


A4. ¿Y si el sistema ya no recibe parches? Buscad en https://endoflife.date la fecha de fin de soporte de:

La versión de Ubuntu/Debian de vuestro Torrent-Auditor.
Ubuntu 8.04, la que tiene Torrent-Vulnerable (Metasploitable2).
Rellenad:

| Sistema	| Fecha de fin de soporte	| ¿Recibe parches hoy? |
|---|---|---|
| Torrent-Auditor	| 29 de mayo de 2031	| Si |
| Torrent-Vulnerable (Ubuntu 8.04) | 12 de mayo de 2013 | No |

Pregunta A: En la oferta de empleo de Torrent (dossier de la UD02) decían que trabajaban con "Ubuntu 8.04". Aunque pusieran todas las contraseñas bien, ¿qué problema sigue sin tener solución?

Siguen existiendo vulnerabilidades conocidas no corregidas. Cualquier fallo de seguridad a nivel de software red o servicios puede ser explotado.

# Parte B · ¿De dónde vienen los programas instalados?
B1. Mirad de qué repositorios se descarga vuestro sistema:

cat /etc/apt/sources.list
ls /etc/apt/sources.list.d/

<img width="648" height="275" alt="image" src="https://github.com/user-attachments/assets/3f8e7709-0838-48ba-b19c-cbe8dc97e8d5" />

Investiga el fichero donde están configurados los repositorios y comenta el significado de cada una de las líneas y su contenido.

---

Types: Define el tipo de paquete al que se está accediendo.

deb:Indica un repositorio binario que contiene paquetes listos para instalar

---

URL: Dirección URL.

---

Suites: Define la versión del sistema operativo de la que se obtendrán los paquetes.

noble: Es el nombre en clave (codename) de Ubuntu 24.04 LTS. Incluye el software en el momento del lanzamiento base.

noble-updates: Contiene actualizaciones recomendadas lanzadas después de la versión inicial para corregir errores no críticos.

noble-backports: Contiene versiones más nuevas.

noble-security: Contiene parches y actualizaciones urgentes de seguridad.

---

Components: Define el nivel de soporte y tipo de licencia del software dentro de esa versión.

main: Software libre y de código abierto (FOSS) soportado oficialmente de forma directa por Canonical.

restricted: Controladores o software propietario esenciales para el funcionamiento del hardware.

universe: Software libre mantenido por la comunidad, no garantizado directamente por Canonical.

multiverse: Software no libre o restringido por cuestiones legales/patentes.

---

Signed-By: Ruta del archivo que contiene la clave criptográfica (GPG) utilizada para verificar la autenticidad e integridad del repositorio.

/usr/share/keyrings/ubuntu-archive-keyring.gpg: Es la clave pública con la que APT valida que los paquetes han sido firmados oficialmente por Canonical

---

B2. apt solo instala paquetes firmados por claves en las que confía. Mirad dónde están esas claves:

ls /etc/apt/trusted.gpg.d/ /usr/share/keyrings/ 2>/dev/null

<img width="796" height="287" alt="image" src="https://github.com/user-attachments/assets/a24904e3-f85e-4d3e-97ac-8b1f546f0237" />


Nessus, por ejemplo, no viene de los repositorios de Ubuntu, lo descargasteis de Tenable. Si lo instalasteis con dpkg -i, apt no comprobó ninguna firma, la confianza la pusisteis vosotros.

Reflexiona: ¿qué pasaría si un atacante introduce una clave falsa en /usr/share/keyrings/? ¿y si intercepta o reemplaza la clave oficial de Ubuntu ubuntu-archive-keyrings.gpg?

Si un atacante remplaza con una clave falsa, el gestor aceptara como legítimo el paquete y repositorio, dando acceso al atacante.

Si remplaza la clave oficial de ubuntu, el sistema perderá la capacidad de verificar actualizaciones legitimas.


B3. Información de un paquete concreto. Elegid openssh-server o curl:

apt policy curl
Anotad: versión instalada, versión candidata, repositorio de origen y el significado de la tabla de versiones.

<img width="834" height="254" alt="image" src="https://github.com/user-attachments/assets/4d76ada5-3a18-4229-a723-eff8d396125b" />

Versión instalada: Muestra la versión activa en el sistema (o (ninguna) si no se ha instalado).

Versión candidata: La versión más reciente disponible para ser instalada en los repositorios configurados.

El valor delante de la URL es la prioridad del repositorio. 500 es el valor estándar y 100 corresponde a los paquetes instalados localmente.

B4. Buscad paquetes instalados que no proceden de ningún repositorio (instalados a mano):

apt list --installed 2>/dev/null | grep -i local

<img width="877" height="180" alt="image" src="https://github.com/user-attachments/assets/c8e65506-6c3c-4101-a469-f164cbf27117" />

Cada línea que salga merece una pregunta: ¿quién lo instaló, de dónde, y quién lo verificó?

Lo instalo el administrador del equipo, fue descargado manualmente, no fue verificado al carecer de repositorio.

B5. Comprobad si algún fichero de un paquete ha cambiado desde que se instaló:

sudo dpkg -V

Si no sale nada, todo coincide con lo instalado.
Si salen líneas, la primera columna indica qué cambió (5 = el hash MD5 es distinto). Los ficheros de configuración (c) cambian de forma normal. Un binario (/usr/bin/...) modificado es una alarma.

<img width="562" height="157" alt="image" src="https://github.com/user-attachments/assets/b80fcb7e-b5e3-48ad-96e3-9827ee734405" />

Pregunta B: dpkg -V ¿detectaría un rootkit que además hubiera modificado la base de datos de dpkg? ¿Por qué conviene comparar con una copia guardada fuera del sistema? (Pista: recordad la fase 4 de la intrusión.)

No, si el rootkit hubiera modificado la base de datos de auditoria de dpkg como dpkg compara los ficheros del sistema contra la base de datos almacenada localmente, este habría modificado para que coincidan.

Conviene comparar con la copia externa por que esa no ha sido alterada.

# Parte C · Verificar una descarga: hash y firma
Descargaremos un programa pequeño y real, GNU Hello, junto a su firma y la lista de claves de GNU.

C1. Descarga:

mkdir -p ~/verif && cd ~/verif
wget https://ftp.gnu.org/gnu/hello/hello-2.12.1.tar.gz
wget https://ftp.gnu.org/gnu/hello/hello-2.12.1.tar.gz.sig
wget https://ftp.gnu.org/gnu/gnu-keyring.gpg

C2. Calculad el hash:

sha256sum hello-2.12.1.tar.gz

Comparadlo con vuestro compañero de al lado. ¿Sale igual? Debería.

<img width="862" height="56" alt="image" src="https://github.com/user-attachments/assets/8fa57733-fba0-4bb3-8b45-094c3ed5e684" />

C3. Verificad la firma usando la lista de claves de GNU:

gpg --no-default-keyring --keyring ./gnu-keyring.gpg --verify hello-2.12.1.tar.gz.sig hello-2.12.1.tar.gz

Deberíais ver Good signature from .... También saldrá un aviso WARNING: This key is not certified with a trusted signature. Es normal, significa que vosotros no habéis dicho personalmente que esa clave sea de confianza. Anotad:

<img width="884" height="205" alt="image" src="https://github.com/user-attachments/assets/d8adbd18-e01b-4617-ba7c-ff2c6ec8dd9b" />

Nombre de quien firmó. Reuben Thomas
Fecha de la firma. Sun 29 May 2022 11:05:00 PM UTC
Identificador de la clave. 24093F016FFE8602EF449BB84C8EF3DA3FD37230

C4. El experimento: ¿qué pasa si alguien toca el fichero? Haced una copia y añadid un carácter:

cp hello-2.12.1.tar.gz hello-manipulado.tar.gz
echo "x" >> hello-manipulado.tar.gz
sha256sum hello-2.12.1.tar.gz hello-manipulado.tar.gz
cp hello-2.12.1.tar.gz.sig hello-manipulado.tar.gz.sig
gpg --no-default-keyring --keyring ./gnu-keyring.gpg --verify hello-manipulado.tar.gz.sig hello-manipulado.tar.gz
Anotad: ¿cómo cambia el hash? ¿Qué dice ahora gpg? (Debe decir BAD signature.)
<img width="877" height="271" alt="image" src="https://github.com/user-attachments/assets/71eb928a-75cf-4847-8670-c27942be58ea" />

Esto es lo que habría detectado el caso de vsftpd 2.3.4: una firma que no encaja.

C5. Pregunta C: completad:

| Situación	| ¿Basta con el hash?	| ¿Hace falta la firma? ¿Por qué? |
|---|---|---|
| El fichero se corrompió al descargarlo | Si | No.El hash detecta errores de transmision de bits |		
| Un atacante cambió el fichero y el hash publicado en la misma web | No | Si. Si el servidor esta comprometido, el atacante puede sustituir el fichero y el hash. La firma requiere la clave privada del autor original que el atacante no posee. |		
| Descargas desde un espejo (mirror) que no es de confianza	| No | Si puede alojar binario alterado y publicar el hash alterado. Solo la firma con la clave del autor garantiza la autenticidad.|	

# Parte D · Política de actualizaciones para Torrent
Torrent tiene tres socios y nadie se encarga del mantenimiento. Redactad una política de máximo 6 líneas que responda:

¿Quién se encarga de las actualizaciones y con qué frecuencia?
¿Se actualiza todo automáticamente o primero se prueba? (Pensad en el equipo del servidor de impresión y en el ERP.)
¿De dónde se puede instalar software y quién lo autoriza?
¿Qué se hace con el equipo que ya no recibe parches (EOL)?
Para ver la opción automática en Ubuntu/Debian (solo mirad, no hace falta activarla):

apt policy unattended-upgrades
cat /etc/apt/apt.conf.d/20auto-upgrades 2>/dev/null

El administrador de sistemas aplicará las actualizaciones del sistema operativo mensualmente fuera del horario laboral, reservando las pruebas previas en un entorno de laboratorio para cambios en el ERP y el servidor de impresión. Queda estrictamente prohibida la instalación de software no autorizado fuera de los repositorios oficiales de Ubuntu sin la previa verificación de firma y aprobación del responsable. Finalmente, cualquier equipo que alcance el fin de soporte (EOL) será aislado de forma inmediata en una VLAN sin acceso a Internet y sustituido en un plazo máximo de 30 días.


Debate: actualizar automáticamente reduce el riesgo de que Torrent esté desactualizado, pero una actualización puede romper el ERP un viernes por la tarde. ¿Qué ganáis y qué perdéis con cada opción?

¿Qué ganamos?
El sistema se mantiene protegido siempre contra fallos de seguridad conocidos sin depender de que alguien se acuerde de actualizarlo a mano.

¿Qué perdemos?
Hay riesgo de que una actualización automática rompa alguna dependencia o servicio crítico.
