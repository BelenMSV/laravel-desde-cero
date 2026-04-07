# Tema 4: Feedback de Errores y la función old()

En el tema anterior vimos que cuando la validación de Laravel falla, el sistema redirige automáticamente al usuario de vuelta al formulario. Pero si no le decimos a nuestra vista de Blade que muestre esos errores, el usuario simplemente verá cómo la página se recarga vacía y no entenderá qué hizo mal.

Para solucionar esto y crear una Experiencia de Usuario (UX) profesional, Laravel nos proporciona dos herramientas maravillosas en Blade: la directiva `@error` y la función `old()`.



## 🚨 1. Mostrando los Errores (`@error`)

Cuando la validación falla, Laravel envía automáticamente a la vista una variable especial que contiene todos los mensajes de error. 

Para mostrar el error específico de un campo, usamos la directiva de Blade `@error('nombre_del_campo')`. Todo el HTML que pongas dentro de esa directiva **solo se mostrará si hay un error para ese campo en específico**.

```html
<label>Título del Post:</label>
<input type="text" name="titulo">

@error('titulo')
    <div style="color: red; font-size: 12px;">
        {{ $message }} </div>
@enderror
```

*Nota: Por defecto, los mensajes de error de Laravel están en inglés (ej. "The titulo field is required"). En módulos más avanzados veremos cómo traducir toda tu aplicación al español.*

## 💾 2. Conservando los datos ingresados (`old()`)

Imagina que un usuario escribe un artículo de 5 párrafos, pero se le olvida poner el título. Al enviar, Laravel lo devuelve al formulario. Mostrarle el error en rojo está bien, **pero si el área de texto del artículo aparece vacía y tiene que volver a escribir todo, ese usuario cerrará tu web y no volverá jamás.**

Para evitar que se pierdan los datos correctos, Laravel guarda temporalmente en la sesión lo que el usuario escribió. Podemos recuperar esos datos usando la función `old('nombre_del_campo')`.

Se usa dentro del atributo `value` del `<input>`:

```html
<label>Título del Post:</label>
<input type="text" name="titulo" value="{{ old('titulo') }}">
```

Para un `<textarea>`, se pone entre las etiquetas de apertura y cierre:
```html
<textarea name="contenido">{{ old('contenido') }}</textarea>
```

## 🏆 3. El Formulario Perfecto (Juntando todo)

Así es como se ve un campo de formulario profesional y robusto en Laravel, combinando todo lo que hemos aprendido en este módulo:

```html
<form action="{{ route('posts.store') }}" method="POST">
    @csrf
    
    <div>
        <label>Título:</label>
        <input 
            type="text" 
            name="titulo" 
            value="{{ old('titulo') }}"
            style="border: {{ $errors->has('titulo') ? '1px solid red' : '1px solid black' }}"
        >
        
        @error('titulo')
            <span style="color: red;">{{ $message }}</span>
        @enderror
    </div>

    <button type="submit">Guardar</button>
</form>
```

---

### 🚀 Siguiente paso
Nuestro sistema de validación ya funciona perfectamente y el usuario tiene una excelente experiencia. Sin embargo, si nuestro formulario tuviera 20 campos, nuestro Controlador se llenaría de reglas de validación, volviéndose gigante y difícil de leer. En el siguiente tema, aprenderemos el "Estándar de Oro" de los profesionales: sacar toda esa validación del Controlador usando los **Form Requests**.