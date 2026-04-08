# Tema 6: Subida de Archivos e Imágenes

¿Qué sería de una aplicación moderna sin fotos de perfil o documentos adjuntos? Subir archivos es una funcionalidad vital, pero requiere un par de configuraciones especiales tanto en tu HTML como en tu código PHP.

## ⚠️ 1. El Formulario HTML (El error más común)

Para que un formulario HTML pueda enviar un archivo físico (y no solo texto), **es obligatorio** añadir el atributo `enctype="multipart/form-data"` a la etiqueta `<form>`. Si olvidas esto, Laravel nunca recibirá la imagen.

```html
<form action="{{ route('perfil.actualizar') }}" method="POST" enctype="multipart/form-data">
    @csrf
    
    <label>Sube tu foto de perfil:</label>
    <input type="file" name="avatar" accept="image/png, image/jpeg">
    
    <button type="submit">Subir Foto</button>
</form>
```

## 🛡️ 2. Validando la Imagen

¡Nunca confíes en un archivo subido por un usuario! Alguien podría intentar subir un script malicioso disfrazado de imagen. Afortunadamente, Laravel tiene reglas de validación específicas para esto en nuestro Controlador (o Form Request):

```php
use Illuminate\Http\Request;

public function actualizarPerfil(Request $request)
{
    $request->validate([
        // Validamos que sea un archivo de imagen, máximo 2MB (2048 KB)
        'avatar' => 'required|image|mimes:jpeg,png,jpg,gif|max:2048',
    ]);

    // Continuamos si la validación es exitosa...
}
```

## 💾 3. Guardando el Archivo

Si la validación pasa, guardar el archivo en el disco del servidor toma literalmente una línea de código. 

Usamos el método `store()`. Le pasamos dos cosas: el nombre de la carpeta donde queremos guardarlo, y el "disco" que queremos usar (usaremos el disco `public` para que los usuarios puedan ver la foto en la web).

```php
public function actualizarPerfil(Request $request)
{
    $request->validate(['avatar' => 'required|image|max:2048']);

    // 1. Verificamos que el archivo realmente venga en la petición
    if ($request->hasFile('avatar')) {
        
        // 2. Guardamos el archivo
        // Esto lo guardará en: storage/app/public/avatares/nombre_generado.jpg
        // Y devuelve la ruta para que la guardemos en la base de datos
        $rutaImagen = $request->file('avatar')->store('avatares', 'public');

        // 3. Aquí guardaríamos $rutaImagen en la base de datos
        // ej: $usuario->update(['avatar' => $rutaImagen]);

        return "Imagen subida y guardada en: " . $rutaImagen;
    }
}
```

## 🔗 4. El Comando Mágico: Storage Link

Por motivos de seguridad, la carpeta pública de tu web (la que la gente puede ver en el navegador) es `/public`. Sin embargo, Laravel guarda tus archivos en `/storage/app/public`. 

**Nadie puede acceder a la carpeta storage desde el navegador.** Para solucionar esto, Laravel nos permite crear un "atajo" o puente que conecte ambas carpetas. Solo tienes que ejecutar este comando **una sola vez** en tu terminal:

```bash
php artisan storage:link
```

Ahora, todo lo que subas a `storage/app/public` será visible instantáneamente en la carpeta `public/storage`.

## 🖼️ 5. Mostrando la Imagen en Blade

Finalmente, para mostrar la imagen que acabamos de guardar, usamos la función `asset()` de Blade, asegurándonos de añadir el prefijo `storage/`:

```html
<img src="{{ asset('storage/' . $usuario->avatar) }}" alt="Foto de perfil">
```

---

### 🎉 ¡Módulo 5 Completado!

¡Felicidades! Has dado un salto gigantesco en tu aprendizaje. Ahora eres capaz de construir puentes seguros entre los usuarios y tu base de datos. Sabes cómo prevenir ataques CSRF, simular métodos HTTP, validar datos rigurosamente, manejar errores con una excelente experiencia de usuario y subir archivos al servidor.

Tienes absolutamente todas las piezas del rompecabezas en tu poder. En el **Módulo 6: Proyecto CRUD Completo**, uniremos todo lo que hemos aprendido desde el Módulo 1 para construir una aplicación real desde cero. ¡Prepárate para aplicar tu conocimiento!