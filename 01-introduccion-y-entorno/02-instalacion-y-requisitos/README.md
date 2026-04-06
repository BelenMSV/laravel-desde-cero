# Tema 2: Instalación y Requisitos Previos

¡Manos a la obra! Para empezar a construir aplicaciones increíbles con Laravel, primero necesitamos preparar nuestra computadora. Piensa en esto como equipar tu cocina antes de empezar a preparar una receta compleja.

## 🛠️ 1. Requisitos Indispensables

Laravel es un framework de PHP, por lo que tu computadora necesita entender este lenguaje. Además, necesitamos un gestor para descargar las piezas (paquetes) que conforman el framework. 

Asegúrate de tener instalados estos dos elementos:

* **PHP:** Laravel requiere una versión reciente de PHP (recomendado PHP 8.2 o superior).
* **Composer:** Es el "gestor de dependencias" de PHP. Es un programa que descarga e instala automáticamente todas las librerías de las que depende Laravel para funcionar.

## 🚀 2. La ruta fácil: Entornos Locales

Instalar PHP y configurar servidores web de forma manual puede ser un dolor de cabeza. Por suerte, existen herramientas todo-en-uno que te instalan todo con un par de clics:

### Si usas Windows:
* **[Laravel Herd](https://herd.laravel.com/):** Es la herramienta oficial y más rápida actualmente. Instala PHP y un entorno súper veloz sin configuraciones complejas.
* **[Laragon](https://laragon.org/):** Una excelente y clásica alternativa, muy visual y amigable para principiantes.

### Si usas macOS:
* **[Laravel Herd](https://herd.laravel.com/):** Nuevamente, la opción nativa y oficial recomendada por el equipo de Laravel.

*(Nota: Descarga, instala y sigue las instrucciones del programa que hayas elegido. Una vez instalado, abre tu terminal y verifica que todo funcione escribiendo `php -v` y `composer -v`)*.

---

## 🏗️ 3. Creando tu primer proyecto

Con PHP y Composer listos, crear un proyecto de Laravel es cuestión de escribir una sola línea en tu terminal. 

1. Abre tu terminal o consola de comandos.
2. Navega hasta la carpeta donde quieres guardar tus proyectos (por ejemplo: `cd Documentos/Proyectos`).
3. Ejecuta el siguiente comando de Composer:

```bash
composer create-project laravel/laravel mi-primera-app
```

*Nota: Puedes cambiar `mi-primera-app` por el nombre que quieras darle a tu proyecto.*

Composer empezará a descargar todos los archivos necesarios. Esto puede tardar uno o dos minutos dependiendo de tu conexión a internet.

---

## 🌐 4. Levantando el servidor de desarrollo

Una vez que Composer termine, ya tienes un proyecto de Laravel completo. Ahora vamos a encenderlo para verlo en el navegador:

1. Entra a la carpeta de tu nuevo proyecto:
   ```bash
   cd mi-primera-app
   ```
2. Inicia el servidor de desarrollo local integrado en Laravel usando **Artisan** (el asistente de línea de comandos de Laravel que usaremos muchísimo en este curso):
   ```bash
   php artisan serve
   ```

La terminal te mostrará un mensaje indicando que el servidor está corriendo, generalmente en `http://127.0.0.1:8000` o `http://localhost:8000`. 

¡Abre ese enlace en tu navegador y felicidades! Estás viendo tu primera aplicación de Laravel funcionando. En el próximo tema exploraremos qué significa cada carpeta y archivo que se acaba de descargar.