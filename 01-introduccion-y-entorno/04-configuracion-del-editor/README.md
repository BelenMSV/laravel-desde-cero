# Tema 4: Configuración del Editor y Extensiones

Programar en Laravel es una experiencia genial, pero puede ser frustrante si tu editor no te ayuda con el autocompletado o no entiende la sintaxis especial de Laravel. 

Aunque puedes usar cualquier editor, la industria utiliza mayoritariamente **Visual Studio Code (VS Code)**. Aquí te enseñaré cómo convertirlo en una "máquina de guerra" para Laravel.

## 🔌 Extensiones Imprescindibles

Ve a la sección de extensiones en VS Code (`Ctrl+Shift+X`) e instala las siguientes:

1.  **PHP Intelephense:** (De Ben Mewburn). Es la más importante. Te da autocompletado real, encuentra errores antes de ejecutar el código y te permite navegar entre archivos rápidamente.
2.  **Laravel Blade Snippets:** Laravel usa archivos `.blade.php`. Sin esta extensión, el código se verá en blanco y negro. Esta herramienta le da color y atajos de teclado.
3.  **Laravel Extra Intellisense:** Te ayuda a autocompletar nombres de rutas, vistas y configuraciones dentro de tu código PHP.
4.  **Laravel Artisan:** Te permite ejecutar comandos de Artisan directamente desde la paleta de comandos de VS Code (`Ctrl+Shift+P`) sin tener que ir a la terminal.

## ⚙️ Consejos Pro: Auto-formateo

Para que tu código siempre esté limpio y ordenado, te recomiendo activar el "Format on Save":

1.  Ve a **File > Preferences > Settings**.
2.  Busca `format on save`.
3.  Marca la casilla. 

Ahora, cada vez que guardes un archivo, VS Code acomodará los espacios y paréntesis por ti. ¡Magia!

---

### 🚀 Siguiente paso
Con tu editor listo, solo nos queda conocer a nuestro "asistente personal" en la línea de comandos: **Artisan**. ¡Vamos al último tema de este módulo!