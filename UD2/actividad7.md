## Parte B · Preparar un entorno controlado (CE d)

Antes de analizar nada sospechoso, se prepara el laboratorio de análisis. Hacedlo en este orden:

Apagad Torrent-Auditor. En VirtualBox, comprobad que no tiene carpetas compartidas ni portapapeles/arrastrar y soltar habilitados con el equipo anfitrión.

Haced un snapshot con el nombre limpio-antes-malware.

Dejad el adaptador NAT activo solo durante la Parte D (instalar ClamAV y actualizar firmas). Se desactiva antes de ejecutar el programa sospechoso (Parte F).

Rellenad en vuestro documento:

| Medida de aislamiento | ¿Hecho? | ¿Qué riesgo evita? |
| :--- | :---: | :--- |
| Sin carpetas compartidas ni portapapeles | Si | Evita la propagación de archivos maliciosos entre la vm y el anfitrión.|
| Snapshot previo | Si | Permite revertir la maquina a un estado previo y limpio. |
| Sin red (o red interna aislada) al ejecutar | Si | Impide que el software malicioso realice conexiones. |
| Máquina que no contiene datos personales ni credenciales | Si | Garantizar que el malware no encuentre ningún dato personal. |

> **Idea clave:** un sandbox sirve en los dos sentidos: lo que pasa dentro no sale, y lo de fuera no influye en el análisis.

## Parte C · El fichero EICAR: una amenaza de mentira (CE e)

EICAR es una cadena de texto estándar de 68 caracteres que todos los antivirus detectan como si fuera un virus. No hace nada, pero permite comprobar que el antivirus funciona.

En el navegador del anfitrión abrid la página oficial de EICAR (https://www.eicar.org, sección Anti-Malware Testfile). Localizad la cadena de texto de prueba.

Copiadla en un fichero de la VM:
nano ~/eicar.txt     # pegad la cadena, guardad y salid
ls -l ~/eicar.txt

Si vuestro antivirus del anfitrión salta, es lo esperado: está haciendo su trabajo. Pegadla solo dentro de la VM.

Calculad el hash y buscadlo (solo el hash, nunca subáis ficheros desconocidos) en https://www.virustotal.com:
sha256sum ~/eicar.txt

Anotad: ¿cuántos motores lo detectan?

<img width="877" height="319" alt="image" src="https://github.com/user-attachments/assets/1e066af3-a1b1-426e-8bdc-20e24332bc1e" />


## Parte D · Instalar y usar ClamAV (CE e)
D1. Instalación (con NAT activo):

sudo apt update
sudo apt install -y clamav

D2. Actualizar las firmas (la "base de datos de virus"). Si falla con un mensaje de bloqueo, parad antes el servicio automático:

sudo systemctl stop clamav-freshclam
sudo freshclam
sudo systemctl start clamav-freshclam

Anotad la fecha de la base de datos: clamscan --version.

<img width="483" height="46" alt="image" src="https://github.com/user-attachments/assets/6b89bd5f-0845-4901-b56a-d2f8762f3081" />

D3. Escanear el fichero EICAR:

clamscan ~/eicar.txt

Veréis una línea terminada en FOUND y un resumen. Anotad el nombre con el que ClamAV bautiza la amenaza y el número de ficheros infectados.

<img width="745" height="437" alt="image" src="https://github.com/user-attachments/assets/69e1775f-3c7e-41f1-8d95-8174cae42f0c" />

D4. Escanear una carpeta entera, mostrando solo lo infectado:

clamscan -r -i -v ~

Opciones útiles:

| Opción | Qué hace |
| :--- | :--- |
| `-r` | Recursivo |
| `-i` | Muestra solo ficheros infectados |
| `-v` | Modo verboso |
| `--move=DIR` | Mueve los infectados a una carpeta (cuarentena) |
| `--remove` | **Borra** los infectados (peligroso con falsos positivos) |
| `-l fichero.log` | Guarda un registro |

D5. Cuarentena en vez de borrar:

mkdir -p ~/cuarentena
clamscan -r -i -v --move=$HOME/cuarentena ~
ls -l ~/cuarentena

Pregunta D: ¿Por qué suele ser mejor mover a cuarentena que borrar directamente? ¿Qué pasaría si ClamAV marcara por error un fichero importante del ERP de Torrent?

La cuarentena permite analizar el archivo, verificar si es una amenaza real y restaurarlo si se confirma que era un falso positivo.
Si ClamAV marcara por error un fichero importante podria detener los servicios de la empresa.

D6. Detectar rootkits (herramienta complementaria, también de la familia "paliativa"):

sudo apt install -y rkhunter
sudo rkhunter --update
sudo rkhunter --propupd
sudo rkhunter --check --sk --rwo

--propupd guarda una referencia (los hashes de los ficheros importantes). Es la misma idea del hash del sistema de la fase 4.

--rwo muestra solo avisos (warnings).

El informe completo queda en /var/log/rkhunter.log.

Ojo: rkhunter suele dar falsos positivos en un sistema recién instalado. Anotad cuáles salen y razonad si son preocupantes o no.

<img width="884" height="181" alt="image" src="https://github.com/user-attachments/assets/f896a6f5-e915-45f2-9aaf-34387d515940" />

User 'postfix' / Group 'postfix' has been added: Ocurre porque la instalación de rkhunter instaló el servicio de correo postfix como dependencia.   

The command '/usr/bin/lwp-request' has been replaced by a script: En Ubuntu, este binario es por diseño un script de Perl proporcionado por el paquete oficial libwww-perl.   

Hidden file found: /etc/.resolv.conf... y /etc/.updated: Son archivos legítimos creados por el propio sistema operativo para mantener la configuración de red y el estado de actualizaciones. 

## Parte E · Análisis estático: mirar sin ejecutar (CE d)

Ya sabéis que el análisis estático examina un fichero sin ejecutarlo. Probadlo con el fichero que os dará el profesor, sospechoso.sh:

file sospechoso.sh
sha256sum sospechoso.sh
cat sospechoso.sh

<img width="888" height="750" alt="image" src="https://github.com/user-attachments/assets/44dcd67c-8af5-483c-89cb-dbcae1e47808" />

Contestad por escrito, antes de ejecutarlo:

¿Qué crees que hace cada línea?
¿Crea ficheros? ¿Abre puertos? ¿Modifica algo para sobrevivir a un reinicio?

Crea un directorio /tmp/.cache_sistema y una copia actualizador.sh. 
Abre el puerto 8081. 
Sobrevive al reinicio programando un contrab

Si fuera un .exe binario, ¿qué habría que hacer en su lugar? (Pista: no se puede leer, habría que usar herramientas como strings o un desensamblador.)
Un exe no se puede leer, habría que usar herramientas como strings

## Parte F · Análisis dinámico: observar qué hace (CE d)

Desactivad el adaptador NAT de la VM (o pasadla a red interna sin otros equipos).

Haced un snapshot: antes-de-ejecutar.

Tomad una foto del estado inicial (guardad la salida en ficheros para comparar luego):

ps aux > ~/antes_procesos.txt
ss -tulpn > ~/antes_puertos.txt
crontab -l > ~/antes_cron.txt 2>&1
ls -la /tmp > ~/antes_tmp.txt

Ejecutad el programa como usuario normal (no como root):

bash sospechoso.sh

Repetid la foto y comparad:

ps aux > ~/despues_procesos.txt
ss -tulpn > ~/despues_puertos.txt
Repetid la foto y comparad:
pscro auxntab -l > ~/despues_cron.txt 2>&1
ls -la /tmp > ~/despues_tmp.txt

diff ~/antes_procesos.txt ~/despues_procesos.txt
diff ~/antes_puertos.txt ~/despues_puertos.txt
diff ~/antes_cron.txt ~/despues_cron.txt
diff ~/antes_tmp.txt ~/despues_tmp.txt

Rellenad el informe de comportamiento:

| Qué hace | Evidencia (comando y salida) | ¿Qué fase del ataque recuerda? |
| :--- | :--- | :--- |
| Crea ficheros o carpetas | drwxr-xr-x 2 linux linux 4096 .cache_sistema | Fase 5: Borrar huellas |
| Abre algún puerto o proceso | python3 -m http.server 8081 --bind 127.0.0.1 tcp LISTEN 127.0.0.1:8081 | Fase 3: Obtener acceso |
| Intenta persistir (sobrevivir a un reinicio) | @reboot /bin/bash /tmp/.cache_sistema/actualizador.sh | Fase 4: Mantener el acceso |

Comprobad la persistencia: reiniciad la VM y volved a mirar si el comportamiento reaparece.

Erradicación: documentad cómo lo eliminaríais (parar el proceso, quitar la entrada de arranque, borrar los ficheros) y verificad con los mismos comandos del paso 3 que ha desaparecido. Después podéis volver al snapshot.

# Matamos el proceso y verificamos que se ha detenido
<img width="599" height="52" alt="image" src="https://github.com/user-attachments/assets/a3eaf772-e018-469f-87fe-cbf18918f1bc" />

# Eliminamos el cron y verificamos que no haya ninguna tarea programada
<img width="313" height="69" alt="image" src="https://github.com/user-attachments/assets/2c35924b-f48b-42ef-bc4e-84e214d2350f" />

# Eliminamos archivos creados 
<img width="696" height="127" alt="image" src="https://github.com/user-attachments/assets/9c2008dd-ab0a-4784-98b8-0bc1863b4b73" />

Pregunta F: Habéis encontrado el comportamiento comparando "antes" y "después". ¿Cuál es el equivalente en un servidor real de Torrent si nadie hizo la foto inicial? ¿Qué relación tiene con el inventario de servicios que hicisteis en la práctica anterior?

El administrador tendría que consultar los procesos y puertos en busca de anomalias.
En vez de comparar dos archivos, el servidor compara los servicios y paquetes con una verificación de integridad.

## Parte G · Preventivo o paliativo (CE c, e)
Clasificad cada medida:

| Medida | Preventiva | Paliativa |
| :--- | :---: | :---: |
| Mantener el sistema actualizado | X | |
| ClamAV escaneando a demanda un disco | | X |
| Descargar software solo de repositorios oficiales | X | |
| Copia de seguridad semanal desconectada de la red | | X |
| Cuenta de usuario sin privilegios de administrador | X | |
| `rkhunter` tras sospechar una intrusión | | X |
| Formación del personal contra el phishing | X | |
