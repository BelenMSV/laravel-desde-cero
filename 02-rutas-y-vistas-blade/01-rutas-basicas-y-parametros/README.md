# Tema 1: Rutas Básicas y Parámetros

¡Bienvenido al Módulo 2! Es hora de darle vida a nuestra aplicación. En Laravel, todo el tráfico que entra a tu página web pasa por un sistema central de "Rutas" (*Routes*). Piensa en las rutas como el recepcionista de un hotel: reciben al visitante (la URL) y le indican a qué habitación debe ir.

## 📍 ¿Dónde están las Rutas?

Abre tu editor de código (VS Code) y dirígete a la carpeta `routes/`. Verás varios archivos, pero para aplicaciones web, el **99% del tiempo trabajaremos en el archivo `web.php`**.

Si abres `routes/web.php`, verás que Laravel ya trae una ruta por defecto:

```php
use Illuminate\Support\Facades\Route;

Route::get('/', function () {
    return view('welcome');
});
```
Esta ruta le dice a Laravel: *"Cuando alguien visite la raíz del sitio (`/`), muéstrale la vista llamada `welcome`"*.

## ✍️ Creando tu primera Ruta

Vamos a crear una ruta nosotros mismos. Al final de tu archivo `web.php`, añade el siguiente código:

```php
Route::get('/hola', function () {
    return '¡Hola, bienvenido a mi primer curso de Laravel!';
});
```

Asegúrate de que tu servidor esté encendido (`php artisan serve`). Si ahora vas a tu navegador y visitas `http://localhost:8000/hola`, ¡verás tu mensaje en pantalla!

### Los Verbos HTTP
En el ejemplo anterior usamos `Route::get`. `GET` es el método que usan los navegadores para "pedir" una página. Más adelante usaremos otros como `POST` (para enviar formularios), `PUT` (para actualizar) o `DELETE` (para borrar).

## 🔗 Rutas con Parámetros (URLs Dinámicas)

¿Qué pasa si queremos saludar a un usuario por su nombre a través de la URL (ej. `/saludo/juan`)? No vamos a crear mil rutas distintas. Usamos **parámetros**.

Añade esto a tu `web.php`:

```php
Route::get('/saludo/{nombre}', function ($nombre) {
    return "¡Hola " . $nombre . ", qué gusto verte por aquí!";
});
```
* Lo que va entre llaves `{nombre}` es el parámetro que estamos esperando en la URL.
* Ese valor se lo pasamos a la función `$nombre` para poder usarlo y mostrarlo en pantalla.

Prueba visitando: `http://localhost:8000/saludo/maria` o `http://localhost:8000/saludo/carlos`.

### Parámetros Opcionales
Si quieres que un parámetro no sea obligatorio, añades un signo de interrogación `?` al final del nombre en la URL, y le das un valor por defecto a la variable:

```php
Route::get('/curso/{tema?}', function ($tema = 'General') {
    return "Estás viendo el tema: " . $tema;
});
```
* Si visitas `/curso/laravel`, verás: *Estás viendo el tema: laravel*
* Si visitas solo `/curso`, verás: *Estás viendo el tema: General*

---

### 🚀 Siguiente paso
Devolver simples textos (*strings*) está bien para hacer pruebas, pero en una aplicación real queremos mostrar páginas HTML completas con diseños bonitos. En el siguiente tema, aprenderemos a separar estas rutas de nuestro código HTML utilizando las **Vistas y el motor Blade**.