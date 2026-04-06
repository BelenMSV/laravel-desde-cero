# Tema 5: Archivos Estáticos (CSS, JS e Imágenes)

Una página web sin estilos ni imágenes es bastante aburrida. Ahora que sabes usar vistas y layouts, seguramente querrás vincular tu propia hoja de estilos (`.css`), algún archivo de JavaScript (`.js`) o mostrar el logotipo de tu aplicación.

## 📁 ¿Dónde se guardan estos archivos?

En Laravel, por razones de seguridad, la única carpeta a la que los usuarios pueden acceder desde internet es la carpeta **`public/`**. 

Todo lo que quieras que el navegador pueda cargar directamente (imágenes, fuentes, css, js) **debe ir dentro de esta carpeta**.

1. Ve a la carpeta `public/`.
2. Crea una carpeta llamada `css`.
3. Dentro de `css`, crea un archivo llamado `style.css` y ponle algún color básico:

```css
/* public/css/style.css */
body {
    background-color: #f3f4f6;
    font-family: Arial, sans-serif;
}
```

## 🪄 La función `asset()`

Para cargar este archivo en tu HTML, no debes intentar adivinar la ruta relativa (como `../../public/css/style.css`). Laravel tiene una función *helper* llamada **`asset()`** que genera la ruta completa y correcta hacia la carpeta pública.

Abre tu archivo `components/layout.blade.php` (o cualquier vista) y añade tu CSS así:

```html
<head>
    <title>Mi Aplicación</title>
    <link rel="stylesheet" href="{{ asset('css/style.css') }}">
</head>
```

### Usando imágenes

El proceso para las imágenes es exactamente el mismo. 
1. Crea una carpeta `public/img/`.
2. Guarda una imagen allí (por ejemplo, `logo.png`).
3. Muéstrala en tu vista de Blade:

```html
<img src="{{ asset('img/logo.png') }}" alt="Logotipo de mi web">
```

---

### 🎉 ¡Módulo 2 Completado Definitivamente!

Ahora sí, tienes el cinturón negro en diseño frontend con Laravel. Sabes manejar URLs, crear vistas, estructurar layouts con componentes, usar nombres de rutas para no romper enlaces y agregar estilos e imágenes. 

En el **Módulo 3**, daremos el gran salto a la arquitectura profesional: dejaremos de poner código en el archivo de rutas y aprenderemos a usar los **Controladores** y el **Patrón MVC**.