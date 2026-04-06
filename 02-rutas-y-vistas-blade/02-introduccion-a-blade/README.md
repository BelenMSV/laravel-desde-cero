# Tema 2: Introducción a las Vistas y Blade

Como vimos en el tema anterior, devolver texto plano desde las rutas no es suficiente para construir una página web real. Para mostrar HTML, CSS y diseños estructurados, Laravel utiliza las **Vistas**.

## 🎨 ¿Qué es una Vista y qué es Blade?

Una **vista** es simplemente un archivo que contiene el código HTML que se le enviará al navegador del usuario. En Laravel, estas vistas viven dentro de la carpeta `resources/views/`.

**Blade** es el motor de plantillas que Laravel trae por defecto. Permite mezclar código HTML normal con código PHP de una forma muy limpia, segura y fácil de leer. Todos los archivos de vistas en Laravel deben terminar con la extensión `.blade.php`.

## 🛠️ Creando nuestra primera Vista

1. Ve a la carpeta `resources/views/`.
2. Crea un nuevo archivo llamado `perfil.blade.php`.
3. Añade un poco de HTML básico:

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Perfil de Usuario</title>
</head>
<body>
    <h1>Bienvenido a tu Perfil</h1>
    <p>Esta es tu primera vista en Laravel.</p>
</body>
</html>
```

## 🔗 Conectando la Ruta con la Vista

Ahora tenemos que decirle a `routes/web.php` que muestre este archivo en lugar de un texto. Abre `web.php` y añade esta ruta:

```php
Route::get('/mi-perfil', function () {
    // La función view() busca automáticamente en resources/views/
    // No es necesario poner la extensión .blade.php
    return view('perfil'); 
});
```
Ve a `http://localhost:8000/mi-perfil` en tu navegador. ¡Ahí está tu diseño HTML!

## 📦 Pasando datos a la Vista

Lo más interesante es enviar información desde la ruta hacia la vista para que nuestra página sea dinámica. 

Modifica la ruta que acabamos de crear para enviarle una variable:

```php
Route::get('/mi-perfil', function () {
    $nombreUsuario = 'Ana';
    
    // Pasamos los datos como un arreglo (array)
    return view('perfil', [
        'nombre' => $nombreUsuario
    ]); 
});
```

Ahora, en tu archivo `perfil.blade.php`, puedes mostrar esa variable usando la sintaxis de Blade de **dobles llaves** `{{ }}`:

```html
<body>
    <h1>Bienvenido, {{ $nombre }}</h1>
    <p>Esta es tu primera vista en Laravel.</p>
</body>
```
*Nota: Las dobles llaves `{{ }}` en Blade son súper seguras porque limpian el contenido automáticamente para evitar ataques XSS (inyección de código malicioso).*

## 🔀 Directivas Básicas de Blade

Blade hace que escribir lógica (como condicionales y bucles) dentro de HTML sea un placer usando palabras que empiezan por `@` (llamadas **directivas**).

### Condicionales (@if)
```html
@if($nombre == 'Ana')
    <p>¡Hola Ana, tienes permisos de administradora!</p>
@else
    <p>Hola, eres un usuario regular.</p>
@endif
```

### Bucles (@foreach)
Si desde la ruta enviáramos a la vista un arreglo de tareas (`$tareas = ['Comprar pan', 'Estudiar Laravel']`), podríamos recorrerlo fácilmente para crear una lista en HTML:

```html
<ul>
    @foreach($tareas as $tarea)
        <li>{{ $tarea }}</li>
    @endforeach
</ul>
```

---

### 🚀 Siguiente paso
Imagina tener que escribir la estructura base del HTML (`<head>`, `<body>`, un menú de navegación, un pie de página) repetida en todas y cada una de las vistas de tu proyecto. Sería agotador y muy difícil de mantener. En el siguiente tema, aprenderemos a usar **Layouts y Componentes** para no repetir código y crear una plantilla maestra.