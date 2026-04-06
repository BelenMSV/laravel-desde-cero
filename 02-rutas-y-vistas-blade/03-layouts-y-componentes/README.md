# Tema 3: Layouts y Componentes de Blade

En el tema anterior creamos una vista de perfil con toda la estructura HTML (`<!DOCTYPE html>`, `<head>`, `<body>`, etc.). 

**El problema:** Si tienes 50 páginas en tu sitio web (Inicio, Contacto, Quiénes somos...), ¿vas a copiar y pegar esa misma estructura 50 veces? ¿Qué pasa si un día quieres cambiar el menú de navegación o el pie de página? Tendrías que editar 50 archivos uno por uno. ¡Una pesadilla!

**La solución:** Cumplir con el principio **DRY** (*Don't Repeat Yourself* - No te repitas) utilizando una **plantilla maestra** (Layout).

## 🧩 Creando nuestra Plantilla Maestra (Componente)

En las versiones modernas de Laravel, la forma más limpia de crear un layout es usando **Componentes Anónimos**.

1. Dentro de `resources/views/`, crea una nueva carpeta llamada `components`.
2. Dentro de esa carpeta, crea un archivo llamado `layout.blade.php`.
3. Pega la estructura base de tu sitio web:

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mi Aplicación en Laravel</title>
</head>
<body>
    <nav>
        <a href="/">Inicio</a> | 
        <a href="/mi-perfil">Perfil</a>
    </nav>

    <main>
        {{ $slot }}
    </main>

    <footer>
        <p>Derechos reservados © {{ date('Y') }}</p>
    </footer>
</body>
</html>
```

### ¿Qué es `{{ $slot }}`?
Es la palabra mágica. `$slot` es una variable especial de Blade que le dice a Laravel: *"Todo el contenido que me pasen desde otras vistas, insértalo exactamente aquí"*.

## 🏗️ Usando el Layout en nuestras Vistas

Ahora vamos a refactorizar (mejorar) la vista `perfil.blade.php` que creamos en el tema anterior. Bórralo todo y déjalo así de limpio:

```html
<x-layout>
    <h1>Bienvenido, {{ $nombre }}</h1>
    <p>Esta es tu vista de perfil, ahora usando una plantilla maestra.</p>
</x-layout>
```

### ¿Qué es `<x-layout>`?
Al colocar el prefijo `x-` seguido del nombre de nuestro archivo (en este caso, `layout`), Laravel sabe que debe envolver el contenido de esta vista dentro del archivo `components/layout.blade.php`. Todo lo que esté entre `<x-layout>` y `</x-layout>` viajará y se insertará donde pusimos el `{{ $slot }}`.

## 🎛️ Pasando datos al Layout (Atributos)

¿Y si queremos que el `<title>` de la pestaña del navegador cambie dependiendo de la página? Podemos pasarle variables (atributos) a nuestro componente.

Modifica la etiqueta principal en tu vista de perfil:
```html
<x-layout titulo="Mi Perfil de Usuario">
    <h1>Bienvenido, {{ $nombre }}</h1>
    </x-layout>
```

Y en tu `layout.blade.php`, lo recibes así:
```html
<head>
    <title>{{ $titulo ?? 'Mi Aplicación en Laravel' }}</title>
</head>
```

---

### 🚀 Siguiente paso
Ahora que tienes tu plantilla maestra lista y tu HTML bien estructurado para no repetir código, es hora de solucionar un problema muy común: ¿qué pasa si el día de mañana decides cambiar la URL de una página? ¡Se romperían todos tus enlaces! 

En el siguiente tema, aprenderemos una de las mejores prácticas en Laravel: las **Rutas con Nombre**, que nos permitirán crear enlaces dinámicos e indestructibles en nuestras vistas.