# Paso 1 — Crear tu subdominio en DuckDNS
Se registra el subdominio `torrent-caballero.duckdns.org` apuntando a la IP pública o local correspondiente (`79.117.102.148` / `192.168.56.102`). Se verifica la resolución de nombres con `getent hosts`:

<img width="553" height="48" alt="image" src="https://github.com/user-attachments/assets/100bac40-1af3-4947-9abe-f2d66b92d373" />


# Paso 2 — Instalar acme.sh (leyendo antes lo que se va a ejecutar)
Se descarga el script de instalación del cliente ACME y se revisa su contenido antes de ejecutarlo

<img width="867" height="428" alt="image" src="https://github.com/user-attachments/assets/da1f1390-43b5-4cf0-8403-cf82ebdc36e3" />

# Paso 3 — Elegir Let’s Encrypt como autoridad
Cambiamos CA por defecto

<img width="813" height="41" alt="image" src="https://github.com/user-attachments/assets/a3109161-88b0-4709-935f-f511f9d460b5" />

# Paso 4 — Pedir el certificado (desafío DNS-01)
<img width="899" height="348" alt="image" src="https://github.com/user-attachments/assets/22998c67-29be-4d26-b0d2-00e5fb8b4f37" />

# Paso 5 — Instalar el certificado donde Apache pueda usarlo
<img width="880" height="237" alt="image" src="https://github.com/user-attachments/assets/7f9e666b-977d-49cd-b77a-b7bcb959253f" />

# Paso 6 — Cambiar Apache al certificado de Let’s Encrypt

<img width="501" height="44" alt="image" src="https://github.com/user-attachments/assets/da7ded81-8eb8-4aec-aa1a-45e7491fa1fe" />

Para asegurar la continuidad del servicio web en el entorno virtualizado, se reestablecen las directivas SSL hacia la CA privada de Torrent S.L.

<img width="878" height="72" alt="image" src="https://github.com/user-attachments/assets/e4145b4c-97e4-407e-abf4-e1d040d18e76" />

# Paso 7 — Comprobar el resultado
<img width="871" height="166" alt="image" src="https://github.com/user-attachments/assets/6f3fdce1-99ad-4d59-b7d0-6257fa4e1f78" />


# Paso 8 — Mirar la cadena y la lista de CA de confianza
<img width="884" height="108" alt="image" src="https://github.com/user-attachments/assets/e27a9b85-3a9f-4f85-a514-42673534dd18" />

¿Está la CA de Torrent S.L. en /etc/ssl/certs por defecto? 
No, porque es una CA privada interna.

¿Quién decide las CAs públicas del sistema? 
Canonical/Debian.

# Paso 9 — La renovación automática

<img width="766" height="145" alt="image" src="https://github.com/user-attachments/assets/3f495ff2-0dd3-4dca-a7c2-b08a946067c3" />

---

| Pregunta | CA propia de Torrent S.L. | Let's Encrypt |
| :--- | :--- | :--- |
| **¿Quién firma el certificado?** | La Autoridad Certificadora (CA) privada interna creada localmente (`Torrent S.L. CA Raiz`). | La Autoridad Certificadora pública y reconocida internacionalmente (`Let's Encrypt / ISRG Root X1`). |
| **¿El navegador lo acepta sin hacer nada?** | **No**, el navegador muestra una advertencia de seguridad (candado abierto / `SEC_ERROR_UNKNOWN_ISSUER`). | **Sí**, todos los navegadores modernos confían en él automáticamente sin mostrar advertencias. |
| **¿Qué tuviste que hacer para que confiara?** | Importar e instalar manualmente el certificado público de la CA raíz (`torrent-ca.crt`) en el almacén de certificados del navegador (Firefox). | Nada en el cliente; la CA raíz ya viene preinstalada en el almacén del sistema operativo y los navegadores. |
| **Validez del certificado del servidor** | Configurable según las necesidades de la empresa (en nuestra práctica se emitió para **1 año / 365 días**). | **90 días** (período fijo corto diseñado para promover la automatización y la seguridad). |
| **Coste y esfuerzo de renovarlo** | **Gratuito**, pero requiere **esfuerzo manual** de generación de CSR, firma y despliegue cada vez que caduca. | **Gratuito** y con **cero esfuerzo manual**, gestionado al 100% de forma automática mediante `acme.sh` y `cron`. |
| **¿Qué se comprueba antes de firmar?** | Nada técnico automatizado; la empresa decide internamente qué emitir de forma discrecional. | Se valida de forma automatizada el control sobre el dominio mediante un desafío (desafío **DNS-01** o **HTTP-01**). |
| **¿Valdría para un servidor sin Internet?** | **Sí**, funciona perfectamente en redes aisladas, intranets corporativas o entornos locales. | **No**, requiere conexión activa a Internet para comunicarse con la API de Let's Encrypt y resolver las validaciones ACME. |

---
## 3. Preguntas finales

## 1. ¿Por qué el navegador confía en Let’s Encrypt y no en la CA de Torrent S.L.? ¿Quién decide en qué entidades confía un navegador?
 El navegador confía en **Let's Encrypt** porque su certificado raíz viene preinstalado del sistema operativo y del propio navegador. Por el contrario, la **CA de Torrent S.L.** es una entidad privada e interna cuyo certificado raíz no ha sido auditado, por lo que el navegador la rechaza como desconocida hasta que el usuario la importa manualmente.
 Lo deciden los desarrolladores de los navegadores y los mantenedores de los sistemas operativos, basándose en estrictos estándares de auditoría internacional.

---

## 2. ¿Por qué los certificados de Let’s Encrypt duran solo 90 días? Piensa en qué pasa si alguien roba la clave privada del servidor.
  Si un atacante compromete o roba la clave privada del servidor, solo podrá suplantar la identidad del sitio durante un máximo de 90 días, limitando gravemente el impacto.

---

## 3. La página interna de la planta de Torrent S.L. no tiene salida a Internet. ¿Qué CA usarías, la propia o Let’s Encrypt? ¿Y para la web pública de un cliente?
  Usaría la CA propia. Al ser una red aislada u offline, los servidores de Let's Encrypt no pueden validar los desafíos ACME.
  Usaría **Let's Encrypt** . Garantiza confianza inmediata en cualquier dispositivo del mundo sin mostrar advertencias de seguridad al público general.

---

## 4. HTTPS protege la confidencialidad y la integridad de lo que viaja. Pon dos ejemplos de ataques que HTTPS no impide.
**Ataques a nivel de aplicación / Software** HTTPS cifra la comunicación en tránsito, pero si la aplicación web tiene fallos de código, un atacante puede explotar la base de datos o infectar el servidor a través de la conexión cifrada.
**Ingeniería social y Phishing** HTTPS no garantiza la veracidad del contenido ni la intención del sitio. Un atacante puede montar una web maliciosa con un certificado HTTPS válido para engañar al usuario y hacer que introduzca sus contraseñas.

---

## 5. Una empresa de 3 personas, ¿montaría una CA propia? ¿Cuándo sí tiene sentido? Relaciónalo con lo que haremos en la Fase 2 (VPN).
  No para un servidor web convencional, debido al coste operativo y a la complejidad de mantenimiento de la infraestructura de clave pública.
  Tiene sentido cuando se necesitan autenticar dispositivos, servicios internos o usuarios en redes privadas de forma segura.
  En la Fase 2, para desplegar una VPN, sí es imprescindible contar con una CA propia. La CA permite emitir certificados digitales individuales a cada uno de los 3 empleados/clientes.
