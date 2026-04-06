# Tema 4: Rutas con Nombre (Named Routes)

Hasta ahora, en nuestras vistas hemos creado enlaces escribiendo la URL directamente, por ejemplo: `<a href="/mi-perfil">Ir al perfil</a>`. 

**🚨 ¿Por qué esto es un problema?**
Imagina que tienes una tienda online con 100 páginas que enlazan a `/carrito`. Un día, decides que la URL debe cambiar a `/mi-cesta`. Tendrías que abrir esas 100 páginas y modificar el enlace uno por uno. 

Para evitar esto, Laravel nos permite "bautizar" nuestras rutas con un nombre.

## 🏷️ Asignando nombres a las Rutas

Abre tu archivo `routes/web.php`. Para darle un nombre a una ruta, simplemente añade el método `->name('nombre_de_la_ruta')` al final:

```php
// Antes
Route::get('/mi-perfil', function () {
    return view('perfil');
});

// Ahora (Ruta con nombre)
Route::get('/mi-perfil', function () {
    return view('perfil');
})->name('perfil.usuario');
```

## 🔗 Usando las Rutas con Nombre en Blade

Ahora que nuestra ruta tiene nombre, ya no necesitamos escribir `/mi-perfil` en nuestro HTML. En su lugar, usaremos una función especial de Blade llamada `route()`.

Ve a tu vista (o a tu layout) y cambia los enlaces así:

```html
<a href="/mi-perfil">Ir al Perfil</a>

<a href="{{ route('perfil.usuario') }}">Ir al Perfil</a>
```

**La Magia:** Si mañana cambias la URL en `web.php` de `/mi-perfil` a `/ajustes-perfil`, **¡no tienes que tocar tus vistas!** Laravel generará automáticamente la nueva URL en todos los enlaces que usen `route('perfil.usuario')`.

## 📦 Rutas con parámetros y nombres

Si tu ruta requiere un parámetro (como el ID o nombre de un usuario), puedes pasarlo como segundo argumento en la función `route()`:

```php
// En web.php
Route::get('/usuario/{id}', function ($id) {
    return "Viendo usuario " . $id;
})->name('usuario.ver');
```

```html
<a href="{{ route('usuario.ver', ['id' => 5]) }}">Ver Perfil 5</a>
```