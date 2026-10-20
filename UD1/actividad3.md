| N º Vulnerabilidades | Severidad / CVSS |
|---|---|
| 10 |  9.0 – 10.0 Crítica |
| 6 | 7.0 – 8.9 Alta |
| 24 | 4.0 – 6.9 Media |
| 9 | 0.1 – 3.9 Baja |
| 140 | 0.0 Informativa |

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

## CVSS
* **Cómo se podría explotar:** Un atacante en la red puede capturar el tráfico con un analizador de red para leer las contraseñas en texto, o bien falsificar su dirección IP para hacerse pasar por un equipo de confianza y entrar al sistema sin contraseña.
* **Cómo se mitiga:** Desactivar y eliminar el servicio rlogin del sistema y sustituirlo por SSH para cualquier acceso remoto.
* **Referencia:** Nessus Plugin #10205

---

## CVSS
### AV — Attack Vector
¿Desde dónde se puede lanzar el ataque?

| Sigla | Valor | Significado |
| :---: | :--- | :--- |
| **N** | Network | Remoto, por red — el caso más peligroso, sin cercanía ni acceso previo |
| **A** | Adjacent | Solo desde la misma red local/segmento (misma wifi o VLAN) |
| **L** | Local | Requiere acceso local al equipo (sesión ya iniciada, terminal) |
| **P** | Physical | Requiere tocar físicamente el dispositivo |

> **Nota:** Cuanto más lejos se pueda atacar sin necesitar cercanía física, más peligrosa es la vulnerabilidad.

## AC y PR
¿Qué tan difícil es, y qué necesita tener ya el atacante?

### AC — Attack Complexity
| Sigla | Valor | Significado |
| :---: | :--- | :--- |
| **L** | Low | Se explota de forma fiable, sin condiciones especiales |
| **H** | High | Requiere condiciones fuera del control del atacante (interceptar tráfico, ganar una carrera de tiempos...) |

### PR — Privileges Required
| Sigla | Valor | Significado |
| :---: | :--- | :--- |
| **N** | None | Ninguna cuenta ni credencial previa |
| **L** | Low | Necesita privilegios básicos de usuario normal |
| **H** | High | Necesita privilegios administrativos previos |

## UI y Scope
¿Necesita que alguien más haga algo, y hasta dónde llega el daño?

### UI — User Interaction
| Sigla | Valor | Significado |
| :---: | :--- | :--- |
| **N** | None | El atacante actúa solo, sin depender de nadie |
| **R** | Required | Necesita que una víctima haga algo (clic, abrir un archivo, tener sesión activa) |

### S — Scope
| Sigla | Valor | Significado |
| :---: | :--- | :--- |
| **U** | Unchanged | El impacto se queda dentro del propio componente vulnerable |
| **C** | Changed | El impacto se "escapa" a recursos fuera del control de seguridad de ese componente |

## C, I, A — las métricas de impacto
Las tres se leen igual: solo cambia sobre qué pata de la tríada preguntan.[cite: 4]

| Sigla | Valor | Significado |
| :---: | :--- | :--- |
| **N** | None | Sin impacto en esa propiedad |
| **L** | Low | Impacto parcial — se compromete algo, no todo |
| **H** | High | Impacto total — se compromete por completo |

---

### C — Confidentiality
¿Cuánta información puede leer el atacante?

### I — Integrity
¿Cuánto puede modificar el atacante?

### A — Availability
¿Cuánto puede interrumpir el servicio el atacante?

## Métricas temporales (opcionales)
Aparecen en un segundo vector — no cambian el problema de fondo, matizan cómo de madura está la amenaza.

| Métrica | Pregunta | Valores posibles |
| :---: | :--- | :--- |
| **E** | ¿Existe ya forma fácil de explotarla? | Unproven · PoC · Functional · High |
| **RL** | ¿Hay ya solución? | Official Fix · Temporary Fix · Workaround · Unavailable |
| **RC** | ¿Cuánto nos fiamos del informe? | Unknown · Reasonable · Confirmed |

> **En Nessus:**
> Se ve como un segundo vector junto al base, ej. `CVSS:3.0/E:U/RL:O/RC:C` — no hace falta memorizarlo, solo reconocerlo.

## Ejemplo resuelto — Samba Badlock

**CVE-2016-2118** · Vector: `AV:N/AC:H/PR:N/UI:R/S:U/C:H/I:H/A:H` · Nota: 7.5 (Alta)

| Métrica | Valor | Lectura |
| :---: | :---: | :--- |
| **AV** | N | Se ataca por red |
| **AC** | H | Pero es difícil: necesita interceptar tráfico (MITM) |
| **PR** | N | No necesita ninguna cuenta previa |
| **UI** | R | Necesita una sesión cliente-servidor en curso |
| **S** | U | El daño se queda dentro de Samba |
| **C / I / A** | H / H / H | Impacto total si se consigue explotar |

> **Por qué no es crítica pese a C-I-A al máximo:** las dos barreras de entrada (`AC:H` y `UI:R`) bajan la nota — CVSS mide el daño ponderado por lo difícil que es causarlo, no el daño en bruto.
