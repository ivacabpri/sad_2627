# Parte A · ¿Está actualizado el sistema?

A1. Anotad qué versión de sistema operativo y kernel tenéis:

lsb_release -a
uname -r
A2. Pedid al sistema la lista de paquetes disponibles y qué se puede actualizar:

sudo apt update
apt list --upgradable
Anotad: ¿cuántos paquetes tienen actualización pendiente?

A3. Simulad la actualización sin aplicarla:

sudo apt-get -s upgrade | head -30
La opción -s es "simular". Fijaos en que dice qué instalaría, pero no toca nada.

A4. ¿Y si el sistema ya no recibe parches? Buscad en https://endoflife.date la fecha de fin de soporte de:

La versión de Ubuntu/Debian de vuestro Torrent-Auditor.
Ubuntu 8.04, la que tiene Torrent-Vulnerable (Metasploitable2).
Rellenad:

| Sistema	| Fecha de fin de soporte	| ¿Recibe parches hoy? |
|---|---|---|
| Torrent-Auditor	|	| |
|Torrent-Vulnerable (Ubuntu 8.04) | | |

Pregunta A: En la oferta de empleo de Torrent (dossier de la UD02) decían que trabajaban con "Ubuntu 8.04". Aunque pusieran todas las contraseñas bien, ¿qué problema sigue sin tener solución?

# Parte B · ¿De dónde vienen los programas instalados?
B1. Mirad de qué repositorios se descarga vuestro sistema:

cat /etc/apt/sources.list
ls /etc/apt/sources.list.d/
<img width="648" height="275" alt="image" src="https://github.com/user-attachments/assets/3f8e7709-0838-48ba-b19c-cbe8dc97e8d5" />

Investiga el fichero donde están configurados los repositorios y comenta el significado de cada una de las líneas y su contenido.
<img width="110" height="29" alt="image" src="https://github.com/user-attachments/assets/1e674d16-45ac-472e-8126-17753201bd1c" />

Types: Define el tipo de paquete al que se está accediendo.

deb:Indica un repositorio binario que contiene paquetes listos para instalar
<img width="428" height="26" alt="image" src="https://github.com/user-attachments/assets/84aff67f-3362-46e0-81f7-e8c1bad324f5" />

URL: Dirección URL.
<img width="438" height="29" alt="image" src="https://github.com/user-attachments/assets/7f45e36c-e5f6-40a5-a69f-5fd943551999" />

Suites: Define la versión del sistema operativo de la que se obtendrán los paquetes.

noble: Es el nombre en clave (codename) de Ubuntu 24.04 LTS. Incluye el software en el momento del lanzamiento base.

noble-updates: Contiene actualizaciones recomendadas lanzadas después de la versión inicial para corregir errores no críticos.

noble-backports: Contiene versiones más nuevas.

noble-security: Contiene parches y actualizaciones urgentes de seguridad.

<img width="479" height="25" alt="image" src="https://github.com/user-attachments/assets/73ea9973-0f94-4871-b6af-d2e7fda9834c" />

Components: Define el nivel de soporte y tipo de licencia del software dentro de esa versión.

main: Software libre y de código abierto (FOSS) soportado oficialmente de forma directa por Canonical.

restricted: Controladores o software propietario esenciales para el funcionamiento del hardware.

universe: Software libre mantenido por la comunidad, no garantizado directamente por Canonical.

multiverse: Software no libre o restringido por cuestiones legales/patentes.
<img width="598" height="27" alt="image" src="https://github.com/user-attachments/assets/e3599ed1-fb94-4ca9-90c6-35dba888fe22" />

Signed-By: Ruta del archivo que contiene la clave criptográfica (GPG) utilizada para verificar la autenticidad e integridad del repositorio.

/usr/share/keyrings/ubuntu-archive-keyring.gpg: Es la clave pública con la que APT valida que los paquetes han sido firmados oficialmente por Canonical


B2. apt solo instala paquetes firmados por claves en las que confía. Mirad dónde están esas claves:

ls /etc/apt/trusted.gpg.d/ /usr/share/keyrings/ 2>/dev/null
Nessus, por ejemplo, no viene de los repositorios de Ubuntu, lo descargasteis de Tenable. Si lo instalasteis con dpkg -i, apt no comprobó ninguna firma, la confianza la pusisteis vosotros.

Reflexiona: ¿qué pasaría si un atacante introduce una clave falsa en /usr/share/keyrings/? ¿y si intercepta o reemplaza la clave oficial de Ubuntu ubuntu-archive-keyrings.gpg?

B3. Información de un paquete concreto. Elegid openssh-server o curl:

apt policy curl
Anotad: versión instalada, versión candidata, repositorio de origen y el significado de la tabla de versiones.

B4. Buscad paquetes instalados que no proceden de ningún repositorio (instalados a mano):

apt list --installed 2>/dev/null | grep -i local
Cada línea que salga merece una pregunta: ¿quién lo instaló, de dónde, y quién lo verificó?

B5. Comprobad si algún fichero de un paquete ha cambiado desde que se instaló:

sudo dpkg -V
Si no sale nada, todo coincide con lo instalado.
Si salen líneas, la primera columna indica qué cambió (5 = el hash MD5 es distinto). Los ficheros de configuración (c) cambian de forma normal. Un binario (/usr/bin/...) modificado es una alarma.
Pregunta B: dpkg -V ¿detectaría un rootkit que además hubiera modificado la base de datos de dpkg? ¿Por qué conviene comparar con una copia guardada fuera del sistema? (Pista: recordad la fase 4 de la intrusión.)

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

C3. Verificad la firma usando la lista de claves de GNU:

gpg --no-default-keyring --keyring ./gnu-keyring.gpg --verify hello-2.12.1.tar.gz.sig hello-2.12.1.tar.gz
Deberíais ver Good signature from .... También saldrá un aviso WARNING: This key is not certified with a trusted signature. Es normal, significa que vosotros no habéis dicho personalmente que esa clave sea de confianza. Anotad:

Nombre de quien firmó.
Fecha de la firma.
Identificador de la clave.
C4. El experimento: ¿qué pasa si alguien toca el fichero? Haced una copia y añadid un carácter:

cp hello-2.12.1.tar.gz hello-manipulado.tar.gz
echo "x" >> hello-manipulado.tar.gz
sha256sum hello-2.12.1.tar.gz hello-manipulado.tar.gz
cp hello-2.12.1.tar.gz.sig hello-manipulado.tar.gz.sig
gpg --no-default-keyring --keyring ./gnu-keyring.gpg --verify hello-manipulado.tar.gz.sig hello-manipulado.tar.gz
Anotad: ¿cómo cambia el hash? ¿Qué dice ahora gpg? (Debe decir BAD signature.)

Esto es lo que habría detectado el caso de vsftpd 2.3.4: una firma que no encaja.

C5. Pregunta C: completad:

| Situación	| ¿Basta con el hash?	| ¿Hace falta la firma? ¿Por qué? |
|---|---|---|
| El fichero se corrompió al descargarlo | | |		
| Un atacante cambió el fichero y el hash publicado en la misma web | | |		
| Descargas desde un espejo (mirror) que no es de confianza	| | |	

# Parte D · Política de actualizaciones para Torrent
Torrent tiene tres socios y nadie se encarga del mantenimiento. Redactad una política de máximo 6 líneas que responda:

¿Quién se encarga de las actualizaciones y con qué frecuencia?
¿Se actualiza todo automáticamente o primero se prueba? (Pensad en el equipo del servidor de impresión y en el ERP.)
¿De dónde se puede instalar software y quién lo autoriza?
¿Qué se hace con el equipo que ya no recibe parches (EOL)?
Para ver la opción automática en Ubuntu/Debian (solo mirad, no hace falta activarla):

apt policy unattended-upgrades
cat /etc/apt/apt.conf.d/20auto-upgrades 2>/dev/null
Debate: actualizar automáticamente reduce el riesgo de que Torrent esté desactualizado, pero una actualización puede romper el ERP un viernes por la tarde. ¿Qué ganáis y qué perdéis con cada opción?

Windows: los mismos conceptos existen allí con otros nombres. winver (versión), Windows Update (parches), Get-FileHash fichero (hash) y Get-AuthenticodeSignature fichero (firma de un ejecutable).
