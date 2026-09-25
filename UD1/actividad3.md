| N º Vulnerabilidades | Severidad / CVSS |
|---|---|
| 10 | Critica |
| 6 | Alta |
| 24 | Media |
| 9 | Baja |
| 140 | Informativa |

---

| Vulnerabilidad | Severidad / CVSS | Origen) | Descripción |
|---|---|---|---|
| VNC  server password | 10 | Uso | El servicio VNC se ha dejado configurado por defecto una clave muy débil permietiendo un acceso no autenbtificado |
| SSL V2 and 3 Protocol Detection | 9,8 | Diseño | Hay que remplazar los protocolos de seguridad porque SSL 2 y 3 tienen fallos de diseño |
| Rlogin Service Detection | 7,5 | Diseño | Este servicio transmite información sin cifrar en texto plano |

---

# Profundización en Vulnerabilidades Detectadas



## 1. VNC Server 'password' Password

* **Qué es:** Se trata de un servicio de escritorio remoto (VNC) que está configurado de fábrica con una clave de acceso débil.
* **Cómo se podría explotar:** Un atacante solo necesita escanear la red para localizar el puerto abierto, abrir un cliente VNC común e ingresar la palabra password cuando el sistema le pida credenciales para acceder y controlar la pantalla de la máquina remotamente.
* **Cómo se mitiga:** Cambiar la contraseña, o restringir el puerto mediante un firewall para que solo se pueda acceder a través de un túnel SSH cifrado o una red VPN.
* **Referencia:** Nessus Plugin #61708

---

## 2. SSL Version 2 and 3 Protocol Detection

* **Qué es:** Es un fallo en el diseño del cifrado de la web donde el servidor acepta comunicaciones mediante versiones muy antiguas e inseguras de SSL, las cuales tienen debilidades conocidas.
* **Cómo se podría explotar:** Un atacante situado en la misma red (como un Wi-Fi público) puede interceptar la conexión y forzar al navegador a "bajar" el nivel de seguridad al protocolo antiguo, para luego descifrar los datos o robar la sesión del usuario.
* **Cómo se mitiga:** Desactivar por completo el soporte para SSL 2.0 y SSL 3.0 en la configuración del servidor web/aplicación y forzar el uso exclusivo de protocolos modernos como TLS.
* **Referencia:** Nessus Plugin #20007

---

## 3. rlogin Service Detection

* **Qué es:** Es la presencia de un protocolo de acceso remoto que envía todas las credenciales e información en texto plano sin cifrar.
* **Cómo se podría explotar:** Un atacante en la red puede capturar el tráfico con un analizador de red para leer las contraseñas en texto, o bien falsificar su dirección IP para hacerse pasar por un equipo de confianza y entrar al sistema sin contraseña.
* **Cómo se mitiga:** Desactivar y eliminar el servicio rlogin del sistema y sustituirlo por SSH para cualquier acceso remoto.
* **Referencia:** Nessus Plugin #10205
