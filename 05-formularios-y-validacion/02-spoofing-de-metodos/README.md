# Tema 2: Spoofing de Métodos (PUT, PATCH y DELETE)

Si recuerdas el Tema 4 del Módulo 3 (Controladores de Recursos), vimos que para seguir el estándar profesional de un CRUD, las rutas debían usar diferentes **Verbos HTTP**:
* `POST` para guardar algo nuevo.
* `PUT` o `PATCH` para actualizar algo existente.
* `DELETE` para borrar algo.

## 🛑 1. El gran problema del HTML

Aquí nos encontramos con una limitación técnica muy antigua: **los navegadores web (HTML) solo entienden los métodos `GET` y `POST` en sus formularios.**

Si intentas hacer esto en tu vista Blade:
```html
<form action="/posts/1" method="DELETE">
```
El navegador lo ignorará por completo y enviará la petición como un `GET` normal, lo cual no solo no borrará tu post, sino que probablemente te devuelva un error de ruta no encontrada.

## 🎭 2. La magia del "Spoofing" (Suplantación)

Para solucionar esto, Laravel utiliza una técnica llamada **Method Spoofing** (Suplantación de Método). 

El truco consiste en enviar el formulario como un `POST` normal, pero incluir una "nota secreta" (un campo oculto) que le dice a Laravel: *"Oye, aunque esto parece un POST, trátalo como si fuera un DELETE"*.



## 🛠️ 3. La directiva `@method`

Para no tener que escribir ese campo oculto a mano, Blade nos ofrece la directiva `@method`. 

Vamos a ver cómo crear correctamente un formulario para **eliminar** un registro. Fíjate que el atributo `method` del `<form>` sigue siendo `POST`:

```html
<form action="{{ route('posts.destroy', 1) }}" method="POST">
    
    @csrf @method('DELETE') <button type="submit">Borrar Post</button>

</form>
```

Si inspeccionamos el código fuente, veremos lo que Laravel genera por debajo:
```html
<form action="[http://tusitio.com/posts/1](http://tusitio.com/posts/1)" method="POST">
    <input type="hidden" name="_token" value="abc123xyz...">
    <input type="hidden" name="_method" value="DELETE"> <button type="submit">Borrar Post</button>
</form>
```

## 🔄 4. Formularios de Actualización (PUT)

La regla aplica exactamente igual cuando quieres crear un formulario para **editar/actualizar** un registro existente. Usaremos `@method('PUT')`:

```html
<form action="{{ route('posts.update', $post->id) }}" method="POST">
    
    @csrf
    @method('PUT')
    
    <label>Título:</label>
    <input type="text" name="titulo" value="{{ $post->titulo }}">
    
    <button type="submit">Guardar Cambios</button>

</form>
```

> **💡 Regla de Oro:** Siempre que la ruta en `web.php` requiera un método `PUT`, `PATCH` o `DELETE`, tu formulario HTML debe tener `method="POST"`, y dentro debes incluir las directivas `@csrf` y `@method('EL_METODO_QUE_NECESITES')`.

---

### 🚀 Siguiente paso
Ahora ya sabes enviar cualquier tipo de petición al servidor desde tus vistas. Pero, ¿qué pasa si el usuario nos envía el campo del "título" vacío, o intenta subir un PDF cuando pedimos una imagen? En el siguiente tema daremos un paso vital para la seguridad y calidad de tu app: **La Validación Básica**.