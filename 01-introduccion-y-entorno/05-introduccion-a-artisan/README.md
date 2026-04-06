# Tema 5: Introducción a Artisan

Si hay algo que hace que Laravel sea especial, es **Artisan**. Artisan es la interfaz de línea de comandos (CLI) incluida en Laravel que te ayuda a automatizar tareas repetitivas.

En lugar de crear archivos manualmente y escribir todo el código repetitivo (*boilerplate*), le pediremos a Artisan que lo haga por nosotros.

## 🤔 ¿Dónde está Artisan?

Artisan no es un programa que se instala en Windows o Mac, es un **archivo PHP** que vive en la raíz de cada proyecto de Laravel. Por eso, para usarlo, siempre debes estar dentro de la carpeta de tu proyecto en la terminal.

## 🛠️ Comandos Básicos que debes conocer

Prueba ejecutar estos comandos en tu terminal (dentro de la carpeta de tu proyecto):

### 1. El listado de poderes
```bash
php artisan list
```
Este comando te mostrará todos los comandos disponibles. ¡Hay muchísimos! No intentes aprenderlos todos ahora, los iremos viendo uno por uno.

### 2. Ayuda específica
Si no sabes cómo usar un comando, puedes escribir `--help` al final:
```bash
php artisan make:controller --help
```

### 3. El comando "Mágico" (`make`)
La mayoría del tiempo usaremos Artisan para **crear cosas**. Casi todos empiezan por `make:`:
* `make:controller`: Crea un controlador.
* `make:model`: Crea un modelo de base de datos.
* `make:migration`: Crea un archivo para modificar la base de datos.

## 💡 ¿Por qué usar la terminal?

Al principio puede dar un poco de miedo la pantalla negra de la terminal, pero usar Artisan te hace un desarrollador mucho más rápido y evita errores de escritura (typos) al crear archivos. 

---

### 🏆 ¡Módulo 1 Finalizado!
¡Enhorabuena! Has completado la introducción más completa posible. Tienes la teoría, el entorno, la estructura, el editor configurado y sabes usar la consola. 

**¡Estás listo para el Módulo 2: Rutas y Vistas Blade!** Nos vemos allí para empezar a crear tu primera página web real.