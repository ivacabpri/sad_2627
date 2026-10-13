| Dato encontrado | Fuente (1-6) | ¿Para qué le sirve al atacante? | Contramedida propuesta |
| :--- | :---: | :--- | :--- |
| **Estructura y versiones de software** | Fuente 1 | Muestra la tecnología exacta utilizada en la web, facilitando la búsqueda de fallos conocidos. | Ocultar o eliminar la versión del software en la web pública. |
| **Servidor web alojado en sistema operativo desactualizado (Ubuntu 8.04)** | Fuente 2 | Sistema obsoleto sin soporte que contiene múltiples vulnerabilidades graves no corregidas. | Migrar a una versión moderna y con soporte de Linux. |
| **Puestos de trabajo con Windows 7** | Fuente 2 | Equipos sin parches de seguridad, fáciles de infectar con malware o controlar remotamente. | Actualizar el sistema operativo a Windows 10/11. |
| **Datos personales y de contacto directo en WHOIS** | Fuente 3 | Facilita el teléfono y correo personal para ataques directos (*phishing* o llamadas falsas). | Ocultar los datos mediante un servicio de privacidad WHOIS. |
| **Registro SPF permisivo (`v=spf1 +all`)** | Fuente 4 | Permite enviar correos falsos haciéndose pasar por la empresa sin que reboten. | Cambiar la política del registro SPF a restrictiva (`-all`). |
| **Subdominio de `pruebas` expuesto en DNS público** | Fuente 4 | Los entornos de prueba suelen tener menos seguridad, claves por defecto o código no probado. | Eliminar el subdominio del DNS público y ponerlo tras una red privada o VPN. |
| **Estructura de subdominios internos (`portal`, `nas`)** | Fuente 4 | Muestra qué servidores tiene la empresa y dónde guarda las copias de seguridad. | Borrar del DNS público los subdominios que sean de uso privado. |
| **Nota adhesiva en el monitor en la foto publicada** | Fuente 5 | Expone claves o datos de acceso a simple vista a través de redes sociales. | Prohibir anotar contraseñas en papel y usar un gestor de contraseñas. |
| **Uso de Wi-Fi pública sin protección en cafetería** | Fuente 5 | Permite a un atacante en la misma red interceptar la navegación y robar contraseñas. | Obligar al uso de VPN cifrada o datos móviles al trabajar fuera. |
| **Ausencia de Marta del 1 al 15 de agosto y delegación de pagos a Lucía** | Fuente 6 | Facilita el "Fraude del CEO" para engañar a Lucía pidiéndole un pago o cambio bancario urgente. | Confirmar siempre los cambios de cuenta o pagos por llamada telefónica antes de ejecutarlos. |
