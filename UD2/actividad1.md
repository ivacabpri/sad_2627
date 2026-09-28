# Ataques de suplantación de identidad

---

## 1. SMTP spoofing

### Qué es: suplantación de correo electronico

### Cómo se lleva a cabo: el protocolo SMTP tradicional no incluye mecanismos nativos de autenticación de identidad. El atacante utiliza scripts, herramientas de envío o servidores de correo mal configurados para editar los metadatos de las cabeceras.

### Qué categoría(s) de amenaza compromete: integridad, autenticidad, confidencialidad

### Ejemplo o caso real: El caso de fraude del banco Crelan en Bélgica, donde atacantes suplantaron la identidad del CEO mediante correos electrónicos falsificados para engañar al personal financiero y transferir más de 70 millones de euros.

### Medida de prevención: SPF, DKIM, DMARK

### Fuente: puesta en común

---

## 2. DNS spoofing (mi grupo)

### Qué es: Es un ciberataque donde se alteran los registros de un servidor o caché de dns para redirigir el tráfico de los usuarios hacia páginas web falsas y maliciosas

### Cómo se lleva a cabo: El atacante introduce datos falsos en la caché de un servidor DNS o intercepta la consulta del usuario para devolver una IP fraudulenta.

### Qué categoría(s) de amenaza compromete:
Confidencialidad: Al redirigir a la víctima a una web falsa el atacante captura credenciales, datos bancarios y personales.
Integridad: Se altera la autenticidad de la resolución de nombres y el contenido al que accede el usuario.
Disponibilidad: Puede utilizarse para bloquear o denegar el acceso a servicios legítimos redirigiendo el tráfico a servidores inexistentes o deshabilitados.

### Ejemplo o caso real:
En 2015, un grupo de hackers conocido como Lizard Squad lanzó un ataque de envenenamiento DNS contra Malaysia Airlines en el que redirigían a los visitantes de la página a un sitio web falso que les animaba a iniciar sesión solo para ser recibidos por un mensaje 404 y la imagen de un lagarto.
Este ataque causó estragos en la aerolínea, que ya venía de un año difícil en el que se perdieron dos vuelos. En segundo lugar, planteó dudas sobre si el grupo de hackers robó o no información personal de alguno de los usuarios que se conectaron al sitio web falso.

### Medida de prevención: 
Los proveedores pueden usar DNSSEC: Cuando el propietario de un dominio configura las entradas de DNS, DNSSEC añade una firma criptográfica a las entradas requeridas antes de que estos acepten las búsquedas de DNS como auténticas.
Usar HTTPS: Garantiza el cifrado de la conexión.

### Fuente: 
https://www.proofpoint.com/es/threat-reference/dns-spoofing 
https://www.cloudflare.com/es-es/learning/dns/dns-cache-poisoning/

---

## 3. IP spoofing

### Qué es: un atacante modifica la dirección IP de origen presente en la cabecera de los paquetes I

### Cómo se lleva a cabo: el atacante intercepta o genera paquetes de red y sobrescribe el campo "Source IP Address" del paquete IP con una IP falsa o perteneciente a un equipo legítimo

### Qué categoría(s) de amenaza compromete: integridad, autenticidad

### Ejemplo o caso real:

### Medida de prevención: filtrado de paquetes

### Fuente: puesta en común

---

## 4. Captura de cuentas de usuario y contraseñas

### Qué es: obtención de usuarios y contraseñas o autorizados

### Cómo se lleva a cabo:

### Qué categoría(s) de amenaza compromete: integridad, autenticidad, confidencialidad

### Ejemplo o caso real:

### Medida de prevención: implementación de MFA / 2FA, cifrado mediante HTTPS/TLS

### Fuente: puesta en común

---

## Aplicado a Estudio Torrent

De los cuatro, ¿cuál creéis que sería el más plausible contra Estudio Torrent (wifi de oficina, ERP online, disco compartido con clientes)? Razonad la respuesta 
en 3-4 líneas, usando lo que habéis aprendido de los cuatro ataques, no solo del vuestro.

Al no tener ninguna medida de seguridad los 4 son plausibles, si hubiera que elegir uno yo diría que la captura de usuarios y contraseñas ya que estas
están expuestas a simple vista escritas en post-it. Además de utilizar el portátil para administrar su ERP compartidos con los clientes, el atacante de
interceptar mediante phishing el Wi-Fi de la oficina y coger una credencial tendría acceso a toda la información sin necesidad de hacer un ataque as agresivo,
ademas la ingenieria social es donde se producen mas ataques.

