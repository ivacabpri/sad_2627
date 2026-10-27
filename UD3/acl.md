# 1. Crear los tres grupos

sudo groupadd operarios
sudo groupadd ingenieria
sudo groupadd supervisores

# Crear los usuarios y asignarlos a sus respectivos grupos

sudo useradd -m -g operarios alberto
sudo useradd -m -g operarios oscar
sudo useradd -m -g operarios hugo

sudo useradd -m -g ingenieria nestor
sudo useradd -m -g ingenieria victor
sudo useradd -m -g ingenieria lucia

sudo useradd -m -g supervisores carlos
sudo useradd -m -g supervisores mary
sudo useradd -m -g supervisores john

# 2. Crear estructura de ficheros y directorios

touch hmi_planta
chmod +x hmi_planta

mkdir -p planos
touch planos/autoclave.dwg planos/caldera.dwg planos/destilacion.dwg

# 3. Establecer un propietario root/administrador y restringir accesos base (700 o 750)
sudo chown root:root hmi_planta
sudo chmod 700 hmi_planta

sudo chown root:root planos/
sudo chmod 700 planos/
sudo chmod 600 planos/*

# 4. Aplicando ACLs

# Grupo operarios: ejecutar
sudo setfacl -m g:operarios:--x hmi_planta

# Nestor: leer y ejecutar
sudo setfacl -m u:nestor:r-x hmi_planta

# Lucia: todo (leer, escribir, ejecutar)
sudo setfacl -m u:lucia:rwx hmi_planta

# Permiso de paso/navegación básico a la carpeta planos/
sudo setfacl -m g:operarios:--x planos/
sudo setfacl -m u:lucia:--x planos/

# Alberto: leer autoclave.dwg
sudo setfacl -m u:alberto:r-- planos/autoclave.dwg

# Oscar: leer caldera.dwg
sudo setfacl -m u:oscar:r-- planos/caldera.dwg

# Lucia: leer y escribir en autoclave.dwg
sudo setfacl -m u:lucia:rw- planos/autoclave.dwg

# Victor: todo (rwx) sobre la carpeta planos y todo su contenido
sudo setfacl -R -m u:victor:rwx planos/

# Grupo Supervisores: leer y ejecutar sobre la carpeta planos y todo su contenido
sudo setfacl -R -m g:supervisores:r-x planos/

# Víctor y Supervisores mantienen su acceso predeterminado en archivos nuevos
sudo setfacl -d -m u:victor:rwx planos/
sudo setfacl -d -m g:supervisores:r-x planos/

# 5. Comprobar permisos aplicados
getfacl hmi_planta
getfacl -R planos/

---

¿Por qué no bastan los permisos clásicos de Unix (usuario / grupo / resto) para implementar el esquema del diagrama? Pon un ejemplo concreto.   

Los permisos solo permiten definir permisos para un único usuario propietario, un único grupo propietario y "otros/resto".   

¿Qué es la máscara (mask) de una ACL y cómo afecta a los permisos efectivos de un usuario o grupo, aunque su entrada de ACL indique más permisos?   

La máscara (mask) define el límite máximo de permisos que se pueden conceder a cualquier usuario o grupo adicional mediante ACL.
Actúa como un filtro aunque una regla ACL le conceda a un grupo rwx, si la máscara está establecida en r--, los permisos efectivos del grupo serán únicamente de lectura (r--).

¿Qué diferencia hay entre una ACL de acceso y una ACL por defecto? ¿Cuándo se aplica cada una?   

ACL de acceso (setfacl -m): Determina los derechos actuales sobre un elemento concreto (fichero o directorio) en el momento presente.
ACL por defecto (setfacl -d): Se aplica exclusivamente a directorios. No cambia los permisos del directorio en sí, sino que define la regla de herencia: cualquier nuevo fichero o subdirectorio que se cree dentro de dicho directorio heredará automáticamente esas ACLs por defecto.

Si mañana se incorpora un nuevo operario, ¿qué pasos tendrías que repetir para darle exactamente el mismo acceso que al resto de operarios?   

No hace falta modificar o volver a ejecutar ninguna ACL. El único paso necesario es añadir al nuevo usuario al grupo operarios.
Al pertenecer automáticamente al grupo operarios, heredará al instante todas las ACLs asociadas a dicho grupo.
