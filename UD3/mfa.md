Qué se considera una contraseña «de calidad» se configura en `/etc/security/pwquality.conf`:

| Parámetro | Valor sugerido | Qué hace |
| :--- | :--- | :--- |
| **minlen** | 12 | Longitud mínima de la contraseña |
| **minclass** | 3 | Nº mínimo de tipos de carácter distintos (mayúsculas, minúsculas, dígitos, símbolos) |
| **dcredit / ucredit / lcredit / ocredit** | -1 cada uno | Exigen al menos un dígito / mayúscula / minúscula / símbolo (el signo negativo = obligatorio) |
| **dictcheck** | 1 | Rechaza contraseñas basadas en palabras de diccionario (usa cracklib) |
| **maxrepeat** | 3 | Máximo de caracteres idénticos seguidos (evita «aaaa...») |
| **enforce_for_root** | (sin valor, solo activarla) | Sin esta línea, root puede saltarse toda la política |

---

### 1 Aplicar la política de contraseñas

Se configura `/etc/security/pwquality.conf` con los parámetros de la directiva de seguridad.
Edita el archivo de configuración: sudo nano /etc/security/pwquality.conf y fija los valores de la tabla anterior (descomenta la línea o
añádela si no existe).

<img width="809" height="792" alt="image" src="https://github.com/user-attachments/assets/ec9d1e5f-7dad-4892-90fd-1114a2614468" />


Password: 1234

<img width="570" height="69" alt="image" src="https://github.com/user-attachments/assets/f8d120c7-2df1-468c-a9a6-25ff16a22da7" />

Password: Torrent.2026!Sec

<img width="404" height="69" alt="image" src="https://github.com/user-attachments/assets/acfae0da-7169-4df0-a9eb-9506bcd497cf" />

### 1.4 Caducidad y aviso de contraseñas con `chage`
Se definen políticas de expiración diferenciadas según el nivel de privilegios y acceso a recursos sensibles (60 días para ingeniería/supervisores y 90 días para operarios).

| Opción | Qué hace |
| :--- | :--- |
| `-m` | Días mínimos antes de poder volver a cambiarla (evita cambiarla 5 veces seguidas para recuperar la de siempre) |
| `-M` | Días máximos de validez: pasado ese tiempo, caduca |
| `-W` | Días de aviso antes de que caduque |
| `-l` | Lista la política actual aplicada a un usuario |

# Aplicar expiración a 60 días (aviso a los 7 días, mínimo 1 día para cambiarla) a ingeniería y supervisores

for u in nestor victor lucia carlos mary john; do
  sudo chage -m 1 -M 60 -W 7 $u
done

# Aplicar expiración a 90 días a operarios

for u in alberto oscar hugo; do
  sudo chage -m 1 -M 90 -W 7 $u
done

# Verificar la política aplicada sobre un usuario
sudo chage -l nestor

