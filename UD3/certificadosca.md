| Fichero | Qué es | Quién debe tenerlo |
| :--- | :--- | :--- |
| **ca.key** | Clave privada de la CA | Solo la CA. Si se filtra, cualquiera puede emitir certificados falsos en nombre de Torrent S.L. |
| **ca.crt** | Certificado de la CA (su clave pública) | Todos los clientes que deban confiar en ella |
| **srv.key** | Clave privada del servidor web | Solo el servidor |
| **srv.csr** | Petición de certificado | Se envía a la CA para que la firme |
| **srv.crt** | Certificado del servidor, firmado por la CA | Se publica: lo envía el servidor a cada cliente |
---

# Paso 0: Instalar paquetes y verificar Apache

sudo apt update
sudo apt install -y apache2 openssl curl cron
systemctl status apache2 --no-pager
sudo ss -tlnp | grep ':80'

<img width="870" height="420" alt="image" src="https://github.com/user-attachments/assets/5f355003-3672-4600-acde-05cf6b4b1d3d" />

¿Qué indica el navegador sobre la seguridad de la conexión y por qué?

El navegador muestra la advertencia "No es seguro". Toda la información y los datos enviados entre el navegador y la máquina virtual viajan en texto plano.


# Paso 1: Crear estructura de carpetas

mkdir -p ~/ca/private ~/ca/certs
chmod 700 ~/ca/private
cd ~/ca
ls -ld private

<img width="460" height="98" alt="image" src="https://github.com/user-attachments/assets/b4f021e5-e302-45a8-8c87-2090b32cabee" />

# Paso 2: Generar clave privada de la CA (elige una contraseña "1234")

genrsa crea una clave privada RSA, la opción -aes256 cifra el fichero

openssl genrsa -aes256 -out private/ca.key 4096

<img width="592" height="59" alt="image" src="https://github.com/user-attachments/assets/c591ee17-ef00-4338-aca8-f87b00a04985" />

¿Por qué conviene proteger con contraseña la clave privada de una CA aunque esté en un directorio con permisos restrictivos?

La clave privada de la CA (ca.key) es el elemento más crítico de toda la infraestructura de clave pública (PKI). Si un atacante consigue esta clave, podría emitir certificados falsos para cualquier dominio o servidor en nombre de Torrent S.L.

# Paso 3: Crear certificado raíz autofirmado (Introduce la contraseña elegida)

openssl req -x509 -new -key private/ca.key -sha256 -days 3650 \
-out certs/ca.crt \
-subj "/C=ES/O=Torrent S.L./CN=Torrent S.L. CA Raiz" \
-addext "basicConstraints=critical, CA:TRUE" \
-addext "keyUsage=critical, keyCertSign, cRLSign"

<img width="742" height="106" alt="image" src="https://github.com/user-attachments/assets/71cb637c-9e76-46ee-a0f7-229e97249e2f" />

| Opción | Significado |
| :--- | :--- |
| `req -x509` | Crea directamente un certificado (sin esta opción crearía solo una petición de certificado). |
| `-days 3650` | Validez de 10 años. Las raíces duran mucho; los certificados de servidor, poco. |
| `-subj` | Los datos de identidad (país, organización y nombre de la CA). |
| `basicConstraints CA:TRUE` | Declara «este certificado pertenece a una CA y puede firmar otros». |
| `keyUsage keyCertSign` | Limita para qué puede usarse la clave: firmar certificados. |


# Paso 4: Inspeccionar el certificado raíz creado

openssl x509 -in certs/ca.crt -noout -subject -issuer -dates

<img width="728" height="93" alt="image" src="https://github.com/user-attachments/assets/6582cef1-2287-4d2d-99a4-081fa8efe637" />

openssl x509 -in certs/ca.crt -noout -text | grep -A1 'Basic Constraints'

<img width="806" height="59" alt="image" src="https://github.com/user-attachments/assets/e7d95034-cbad-4c26-ad9b-9ecbe12d8e7d" />

¿Por qué coinciden Subject e Issuer?

Coinciden porque se trata de un certificado autofirmado. No existe ninguna entidad por encima que valida su identidad, por lo que la propia entidad emite y firma su propio certificado.

¿Hasta qué fecha es válida tu CA?

Es válida hasta el Nov 3 18:08:32 2036 GMT

# Paso 5: Generar clave privada del servidor y la CSR

(srv.key): Se genera una clave RSA de 2048 bits. A diferencia de la clave de la CA (ca.key), esta clave va sin contraseña.

(srv.csr): Es el archivo de petición. Contiene la clave pública del servidor y sus datos de identidad.Este fichero se enviará a la CA para que lo firme. La CSR nunca contiene la clave privada.

openssl genrsa -out private/srv.key 2048
openssl req -new -key private/srv.key -out srv.csr \
-subj "/C=ES/O=Torrent S.L./CN=hmi.torrent.local"

<img width="549" height="60" alt="image" src="https://github.com/user-attachments/assets/33def5d4-6d4d-4650-b7a6-eccaf50fa294" />


# Paso 6: Crear el fichero srv.ext especificando la IP (192.168.56.102)

cat <<EOF > srv.ext
subjectAltName = DNS:hmi.torrent.local, IP:192.168.56.102
basicConstraints = CA:FALSE
keyUsage = digitalSignature, keyEncipherment
extendedKeyUsage = serverAuth
EOF

<img width="464" height="105" alt="image" src="https://github.com/user-attachments/assets/0a1cae7d-62f9-4012-9cf4-9b71c3a5652d" />

subjectAltName: Define los nombres de dominio (DNS:hmi.torrent.local) e IP (IP:192.168.56.102) por los que se podrá acceder 
basicConstraints = CA:FALSE: Especifica que este certificado pertenece a un servidor final y no puede utilizarse para firmar otros certificados.   
keyUsage / extendedKeyUsage: Restringen el uso de la clave exclusivamente para autenticar a un servidor web (serverAuth).


# Paso 7: La CA firma el certificado del servidor (Introduce la contraseña de la CA)

openssl x509 -req -in srv.csr \
-CA certs/ca.crt -CAkey private/ca.key -CAcreateserial \
-out certs/srv.crt -days 365 -sha256 -extfile srv.ext

<img width="466" height="109" alt="image" src="https://github.com/user-attachments/assets/a452f926-eeec-45c3-b365-576a6b2c51d8" />

¿Por qué el certificado del servidor dura 365 días y el de la CA 10 años?

Para reducir el impacto y la ventana de exposición si la clave del servidor web se compromete.

# Paso 8: Verificar la cadena de confianza

openssl verify -CAfile certs/ca.crt certs/srv.crt
openssl x509 -in certs/srv.crt -noout -subject -issuer -ext subjectAltName

FALTA IMAGEN









