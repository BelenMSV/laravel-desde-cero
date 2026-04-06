# Tema 3: Estructura de Archivos y Carpetas

¡Felicidades por crear tu primer proyecto en Laravel! Al abrir tu proyecto en un editor de código (como VS Code), es probable que hayas notado una gran cantidad de carpetas y archivos. 

**No te asustes.** No necesitas memorizar para qué sirve cada uno de ellos de inmediato. La arquitectura de Laravel está diseñada para ser escalable, pero como principiante, solo vas a interactuar con un pequeño grupo de carpetas.

Vamos a hacer un recorrido por los lugares más importantes de tu aplicación:

## 📂 Las Carpetas Clave (Donde pasarás el 90% del tiempo)

* **`app/`**: Es el corazón de tu aplicación. Aquí vivirá la lógica principal de tu código. Dentro de esta carpeta encontrarás los **Modelos** (que representan tus tablas de la base de datos) y los **Controladores** (que procesan la información y toman decisiones).
* **`routes/`**: Como su nombre indica, aquí se definen las "Rutas" o URLs de tu aplicación. El archivo que más usarás al principio es `web.php`, donde le dirás a Laravel qué mostrar cuando un usuario visite una URL específica (ej. `tusitio.com/contacto`).
* **`resources/`**: Aquí viven los elementos no compilados de tu aplicación. La subcarpeta más importante para nosotros será **`resources/views/`**. Aquí es donde crearemos nuestros archivos de interfaz de usuario usando HTML y el motor de plantillas de Laravel llamado **Blade**.
* **`database/`**: Contiene todo lo relacionado con tu base de datos. Aquí crearemos las **Migraciones** (archivos que construyen las tablas de tu base de datos de forma automática) y los **Seeders** (archivos para rellenar esas tablas con datos de prueba).
* **`public/`**: Es la única carpeta que será visible para los usuarios en internet. Contiene el archivo `index.php`, que es el punto de entrada de toda la aplicación, además de tus imágenes, archivos CSS y JavaScript públicos.

## ⚙️ Archivos de Configuración Importantes

En la raíz de tu proyecto, verás varios archivos sueltos. El más importante, y al que debes prestarle atención desde el día 1, es el archivo oculto llamado **`.env`** (Environment o Entorno).

### El archivo `.env`
Este archivo contiene la configuración específica del entorno en el que estás trabajando (tu computadora local). Aquí es donde pondrás las credenciales de tu base de datos, contraseñas de correos electrónicos y claves secretas. 

> **⚠️ ¡Regla de Oro!** El archivo `.env` **NUNCA** debe subirse a GitHub ni compartirse en público, ya que contiene información sensible. Laravel ya lo ignora por defecto, pero es bueno que lo sepas.

---

### 🚀 Siguiente paso
Ahora que conoces el mapa de tu proyecto y sabes dónde vivirá tu código, es el momento perfecto para preparar nuestras herramientas de trabajo. En el siguiente tema, configuraremos nuestro editor de código (VS Code) con las extensiones necesarias para que programar en Laravel sea rápido, fácil y libre de errores. ¡Vamos a ello!