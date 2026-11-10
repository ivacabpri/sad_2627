| Fichero | Qué es | Quién debe tenerlo |
| :--- | :--- | :--- |
| **ca.key** | Clave privada de la CA | Solo la CA. Si se filtra, cualquiera puede emitir certificados falsos en nombre de Torrent S.L. |
| **ca.crt** | Certificado de la CA (su clave pública) | Todos los clientes que deban confiar en ella |
| **srv.key** | Clave privada del servidor web | Solo el servidor |
| **srv.csr** | Petición de certificado | Se envía a la CA para que la firme |
| **srv.crt** | Certificado del servidor, firmado por la CA | Se publica: lo envía el servidor a cada cliente |
---

# Paso 0: Instalar paquetes y verificar Apache.

sudo apt update
sudo apt install -y apache2 openssl curl cron
systemctl status apache2 --no-pager
sudo ss -tlnp | grep ':80'

<img width="870" height="420" alt="image" src="https://github.com/user-attachments/assets/5f355003-3672-4600-acde-05cf6b4b1d3d" />

¿Qué indica el navegador sobre la seguridad de la conexión y por qué?

El navegador muestra la advertencia "No es seguro". Toda la información y los datos enviados entre el navegador y la máquina virtual viajan en texto plano.


# Paso 1: Crear estructura de carpetas.

mkdir -p ~/ca/private ~/ca/certs
chmod 700 ~/ca/private
cd ~/ca
ls -ld private

<img width="460" height="98" alt="image" src="https://github.com/user-attachments/assets/b4f021e5-e302-45a8-8c87-2090b32cabee" />

# Paso 2: Generar clave privada de la CA (elige una contraseña "1234").

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


# Paso 4: Inspeccionar el certificado raíz creado.

openssl x509 -in certs/ca.crt -noout -subject -issuer -dates

<img width="728" height="93" alt="image" src="https://github.com/user-attachments/assets/6582cef1-2287-4d2d-99a4-081fa8efe637" />

openssl x509 -in certs/ca.crt -noout -text | grep -A1 'Basic Constraints'

<img width="806" height="59" alt="image" src="https://github.com/user-attachments/assets/e7d95034-cbad-4c26-ad9b-9ecbe12d8e7d" />

¿Por qué coinciden Subject e Issuer?

Coinciden porque se trata de un certificado autofirmado. No existe ninguna entidad por encima que valida su identidad, por lo que la propia entidad emite y firma su propio certificado.

¿Hasta qué fecha es válida tu CA?

Es válida hasta el Nov 3 18:08:32 2036 GMT

# Paso 5: Generar clave privada del servidor y la CSR.

(srv.key): Se genera una clave RSA de 2048 bits. A diferencia de la clave de la CA (ca.key), esta clave va sin contraseña.

(srv.csr): Es el archivo de petición. Contiene la clave pública del servidor y sus datos de identidad.Este fichero se enviará a la CA para que lo firme. La CSR nunca contiene la clave privada.

openssl genrsa -out private/srv.key 2048
openssl req -new -key private/srv.key -out srv.csr \
-subj "/C=ES/O=Torrent S.L./CN=hmi.torrent.local"

<img width="549" height="60" alt="image" src="https://github.com/user-attachments/assets/33def5d4-6d4d-4650-b7a6-eccaf50fa294" />


# Paso 6: Crear el fichero srv.ext especificando la IP (192.168.56.102).

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


# Paso 7: La CA firma el certificado del servidor (Introduce la contraseña de la CA).

openssl x509 -req -in srv.csr \
-CA certs/ca.crt -CAkey private/ca.key -CAcreateserial \
-out certs/srv.crt -days 365 -sha256 -extfile srv.ext

<img width="466" height="109" alt="image" src="https://github.com/user-attachments/assets/a452f926-eeec-45c3-b365-576a6b2c51d8" />

¿Por qué el certificado del servidor dura 365 días y el de la CA 10 años?

Para reducir el impacto y la ventana de exposición si la clave del servidor web se compromete.

# Paso 8: Verificar la cadena de confianza.

openssl verify -CAfile certs/ca.crt certs/srv.crt

<img width="626" height="43" alt="image" src="https://github.com/user-attachments/assets/8baeb1f9-917c-4c5f-aa91-1102597426d2" />

openssl x509 -in certs/srv.crt -noout -subject -issuer -ext subjectAltName

<img width="820" height="95" alt="image" src="https://github.com/user-attachments/assets/7644a71b-687a-4e83-a2f3-83170e6f6854" />

# Paso 9: Copiamos el certificado y la clave a los directorios estándar del sistema y activamos el módulo SSL de Apache.

<img width="758" height="125" alt="image" src="https://github.com/user-attachments/assets/c7ccdd85-051b-4d28-a445-f8ec4626e881" />


# Editamos default-ssl.conf para cambiar nuestro certificado.

<img width="527" height="61" alt="image" src="https://github.com/user-attachments/assets/be4fe439-b1e0-486d-a66b-2bdd8ea9d8eb" />

# Activamos ssl, comprobamos la configuración y reiniciamos el servicio.

<img width="877" height="133" alt="image" src="https://github.com/user-attachments/assets/97f4e38e-e764-43cd-aaa6-8b630eccb1fb" />

# Paso 10: Probamos https.

<img width="768" height="35" alt="image" src="https://github.com/user-attachments/assets/da315899-0ed9-4e67-a2b6-e1572d789412" />

SSL certificate problem: unable to get local issuer certificate». curl no conoce a quien
firmó el certificado y por eso se niega a continuar.

Ahora le decimos a curl que confíe en nuestra CA, solo para esta orden:

<img width="677" height="189" alt="image" src="https://github.com/user-attachments/assets/587e2308-25ed-4fb8-af52-91fe1c4b06ec" />

# Paso 11: Para que el navegador confíe, hay que darle ca.crt. Lo publicamos en el propio Apache y calculamos su huella
digital (SHA-256).

<img width="844" height="845" alt="image" src="https://github.com/user-attachments/assets/0b9003d5-54a7-4ef2-8f88-3e92922f162f" />

# Descargamos e importamos el certificado.

<img width="885" height="79" alt="image" src="https://github.com/user-attachments/assets/c922cda0-5d1b-47de-a074-b95c74e6364b" />

<img width="672" height="110" alt="image" src="https://github.com/user-attachments/assets/26441aa7-e7b6-4e7d-a37a-f138fdab8dc4" />

# Paso 12: Comprobaremos la conexión cifrada TLS desde la terminal usando openssl s_client. 

Así analizaremos qué certificados envía Apache, quién los ha firmado y qué algoritmo de cifrado se ha negociado.

<img width="879" height="119" alt="image" src="https://github.com/user-attachments/assets/3ad9f519-6930-4690-96cb-b53ba92bab95" />

Emisor (i:): Torrent S.L. CA Raiz (tu propia entidad certificadora).

Sujeto (s:): hmi.torrent.local.

Cifrado: TLS_AES_256_GCM_SHA384 mediante TLSv1.3.

Verificación: Verify return code: 0 (ok).

---

# Preguntas de la Parte 1

## Explica con tus palabras la diferencia entre ca.key, ca.crt, srv.key y srv.crt: ¿cuáles son secretos y cuáles
públicos?

ca.key: Es la clave privada de la Entidad Certificadora (CA) raíz.

ca.crt: Es el certificado público autofirmado de la CA raíz. Se distribuye e instala en los navegadores o sistemas operativos de los clientes para que sospechen o confíen en las firmas emitidas por la CA.   

srv.key: Es la clave privada exclusiva del servidor web Apache. Sirve para descifrar el tráfico entrante HTTPS y demostrar la identidad del servidor durante la negociación TLS.

srv.crt: Es el certificado del servidor web emitido y firmado por la CA. Contiene la clave pública del servidor, sus datos de identidad y la firma digital que demuestra su autenticidad.

## Alguien roba ca.key (sin contraseña). ¿Qué puede hacer con ella? ¿Qué tendría que hacer Torrent S.L. para
recuperar la confianza?

Al poseer la clave privada de la CA sin cifrar, el atacante tiene control total sobre la autoridad de certificación. Puede emitir certificados falsos para cualquier dominio y realizar ataques indetectables para los usuarios que confíen en esa CA.

Torrent S.L deberá revocar la clave antigua y volver a crear una nueva.

## ¿Por qué descargar ca.crt por HTTP y no verificar la huella es un riesgo? ¿Quién podría aprovecharlo?

HTTP transmite los datos en texto plano. Si un atacante intercepta la descarga, puede sustituir el archivo ca.crt legítimo por el certificado de su propia CA maliciosa.   

Un atacante situado en la misma red local.

Si el usuario no comprueba la huella SHA-256 por un canal seguro alternativo, terminará instalando y confiando en la CA del atacante, quedando totalmente expuesto a que el atacante intercepte todo su tráfico HTTPS sin que el navegador muestre advertencias.
   
## ¿Es una buena idea que la CA y el servidor web estén en la misma máquina? ¿Qué problema de seguridad
crea?

No. Si el servidor web Apache sufre una vulnerabilidad, el atacante podría ganar acceso a la máquina. Al estar la CA en el mismo sistema, el atacante obtendría acceso directo al archivo.

---


















