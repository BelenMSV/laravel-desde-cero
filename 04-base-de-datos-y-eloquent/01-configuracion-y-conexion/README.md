# Tema 1: Configuración y Conexión

Antes de poder guardar datos, Laravel necesita saber **dónde** guardarlos. En este tema configuraremos la "llave" que une nuestro código con el motor de base de datos.

## 🔑 El archivo `.env`

Como vimos en el Módulo 1, el archivo `.env` en la raíz de tu proyecto contiene las **variables de entorno**. Es aquí donde guardamos las credenciales de la base de datos (nombre, usuario, contraseña) porque son datos sensibles que no deben subirse a GitHub.

Busca la sección que empieza por `DB_`:

```env
DB_CONNECTION=sqlite
# DB_HOST=127.0.0.1
# DB_PORT=3306
# DB_DATABASE=laravel
# DB_USERNAME=root
# DB_PASSWORD=
```

## 🗳️ ¿Qué base de datos elegir?

Laravel soporta varios motores (MySQL, PostgreSQL, SQL Server), pero hoy en día tienes dos opciones principales para empezar:

1.  **SQLite (Recomendada para este curso):** Es la opción por defecto en Laravel 11. No necesitas instalar programas como XAMPP o MySQL. Toda tu base de datos vive en un solo archivo dentro de tu proyecto.
    * **Configuración:** Solo asegúrate de que `DB_CONNECTION=sqlite`. Laravel creará automáticamente un archivo en `database/database.sqlite`.

2.  **MySQL / MariaDB:** Si usas Laragon, Herd o XAMPP y prefieres un motor más robusto.
    * **Configuración:** Cambia `DB_CONNECTION=mysql` y completa los datos de tu servidor local (normalmente `DB_USERNAME=root` y `DB_PASSWORD=` vacío).



## 🛠️ Comprobando la conexión

Una vez configurado tu `.env`, la forma más rápida de saber si Laravel puede "hablar" con tu base de datos es intentar ejecutar las migraciones iniciales (veremos qué son en el siguiente tema).

Ejecuta en tu terminal:
```bash
php artisan migrate
```

* **Si ves una lista de tablas "Created":** ¡Felicidades! La conexión es exitosa.
* **Si ves un error:** Revisa tus credenciales en el `.env` o asegúrate de que tu servidor de base de datos (MySQL) esté encendido.

---

### 🚀 Siguiente paso
Ahora que el puente está construido, necesitamos diseñar las tablas. En el siguiente tema, aprenderemos a usar las **Migraciones** para crear tablas sin escribir ni una sola línea de SQL.