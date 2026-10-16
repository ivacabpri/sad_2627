# Parte A Entendiendo las amenazas

Adware: Software que muestra o descarga publicidad no deseada en tu pantalla.

Backdoor: Método que permite saltarse la autenticación normal para acceder de forma remota y secreta a un sistema.

Bomba Fork: Tipo de ataque de denegación de servicio que multiplica los procesos del sistema hasta colapsar sus recursos.

Bots: Programas automatizados que ejecutan tareas repetitivas.

Bug: Error o fallo de programación en un software.

Caballo de Troya (Troyano): Malware camuflado como un programa legítimo que, concede a un atacante acceso no autorizado.

Cookies: Archivos de texto legítimos que los sitios web guardan en tu navegador para recordar información; no son malware, pero pueden comprometer tu privacidad si se interceptan.

Crackers: Personas que rompen la seguridad de sistemas o programas informáticos con intenciones maliciosas o con el fin de lucrarse.

Cryptovirus / Ransomware: Malware que cifra los archivos.

Exploits: Fragmentos de código o software diseñados para aprovechar una vulnerabilidad específica.

Hijacker: Malware que secuestra las configuraciones del navegador para redirigirte a sitios maliciosos.

Hoax / Joke / Bulo: Mensajes falsos para su propagación.

Keystroke / Keylogger: Software o hardware spyware registrado para grabar cada pulsación de tecla y robar contraseñas.

Ladilla virtual: Programas molestos e invasivos que se adhieren a otros archivos y son difíciles de eliminar.

Leapfrog: Técnica utilizada para saltar de un sistema comprometido a otro dentro de una misma red.

Parásito Informático: Programa malicioso que se adhiere a otros archivos o al sistema operativo para consumir recursos o alterar el funcionamiento.

Pharming: Técnica que redirige el tráfico de un sitio web legítimo a uno falso.

Phishing: Correos maliciosos para engañar a las víctimas y conseguir que entreguen datos confidenciales.

Rabbit o conejos: Programas diseñados para duplicarse a sí mismos continuamente consumiendo espacio en disco o recursos del sistema hasta bloquearlo.

Riskware: Programas legítimos (como herramientas de administración remota) que representan un riesgo de seguridad si los explota un atacante.

Rootkit: Conjunto de herramientas para ocultar la presencia de malware y dar acceso con privilegios de administrador al atacante.

Scumware / Escoria: Software no deseado que altera el navegador o la interfaz del usuario para inyectar publicidad y enlaces engañosos.

Spam: Correo electrónico no solicitado enviado de forma masiva, a menudo utilizado para distribuir publicidad engañosa o malware.

Spyware: Software espía que recopila información de un usuario o equipo sin su consentimiento para enviarla a un tercero.

Pop-Ups / Ventanas emergentes: Ventanas publicitarias que aparecen de forma repentina.

Worms / Gusanos: Malware con capacidad de autorreplicarse y propagarse de forma autónoma a través de las redes.

Bomba lógica: fragmento de código malicioso oculto dentro de un programa que permanece hasta que se cumple una condición o fecha específica


Parte B Clasificar las amenazas

| # | Incidente en Torrent | Tipo | ¿Se replica solo? | ¿Necesita al usuario? |
|--:|:---|:---|:---:|:---:|
| 1 | Un socio abre `factura_marzo.pdf.exe`; poco después todo el disco compartido está cifrado y aparece un mensaje pidiendo 800 € | Troyano | No | Si |
| 2 | Un "optimizador gratuito" de PC que, además, envía todo lo que se teclea a un servidor externo | Keylogger | No | Si |
| 3 | Sin que nadie haga nada, un programa salta de portátil en portátil a través de la wifi del estudio | Gusano | Si | No |
| 4 | Al abrir ciertos ejecutables, éstos infectan a su vez a otros ficheros del equipo | Virus | Si | Si |
| 5 | Tras instalar un editor de PDF gratuito, aparecen ventanas de publicidad constantemente | Adware | No| Si |
| 6 | Un antiguo colaborador dejó un código que borrará los planos el 31 de diciembre | Bomba lógica| No | No |
| 7 | El ordenador del estudio está enviando spam sin que nadie lo sepa, bajo las órdenes de un servidor externo | Bot | No | No |
| 8 | Un conjunto de programas modificados que esconde procesos y ficheros del intruso dentro del propio sistema | Rootkit |  No | No |
| 9 | Un servicio escuchando en un puerto que permite entrar al equipo sin contraseña | Backdoor | No | No |
| 10 | Correo: "Tu banco necesita que confirmes tus datos en este enlace" | Phising | No | Si |
| 11 | Mensaje: "Reenvía esto a 10 contactos o tu cuenta se cerrará" | Bulo | No | Si |
| 12 | Una web idéntica a la del ERP a la que se llega porque el DNS ha sido manipulado | Pharming | No | Si |
| 13 | Un programa que registra sin permiso los hábitos de navegación y los vende | Spyware | No | Si |

Pregunta A: ¿Cuáles de estos incidentes no son "malware" propiamente dicho, sino fraude o ingeniería social? ¿Por qué un antivirus no los detendría?
El 10 (Phishing), el 11 (Bulo) y el 12 (Pharming).
No consisten en ejecutar un programa si no en engañar a la persona.
