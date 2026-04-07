# Tema 2: Migraciones (El Control de Versiones de la BD)

En el pasado, si querías crear una tabla en tu base de datos, tenías que abrir un programa visual (como phpMyAdmin), hacer clic en "Nueva Tabla", añadir las columnas a mano y luego exportar un archivo `.sql` para pasárselo a tus compañeros de equipo. 

Si alguien olvidaba un campo, la aplicación se rompía.

Laravel soluciona esto con las **Migraciones**. Son como un sistema de "control de versiones" (un Git) pero para tu base de datos. Te permiten definir la estructura de tus tablas escribiendo código PHP.



## 🛠️ 1. Creando una Migración

Vamos a crear una tabla para guardar los "Posts" de un blog. Abre tu terminal y ejecuta:

```bash
php artisan make:migration create_posts_table
```

*Nota: La convención en Laravel es usar inglés, minúsculas, en plural y separar con guiones bajos (snake_case).*

## 🔍 2. Entendiendo el archivo de Migración

Artisan acaba de crear un archivo en la carpeta `database/migrations/`. El nombre del archivo incluye una marca de tiempo (ej. `2024_10_25_000000_create_posts_table.php`) para que Laravel sepa en qué orden exacto debe ejecutarlas.

Si abres el archivo, verás dos métodos principales: `up()` y `down()`.

### El método `up()` (Para crear/modificar)
Aquí le decimos a Laravel qué queremos hacer cuando ejecutemos la migración.

```php
public function up(): void
{
    Schema::create('posts', function (Blueprint $table) {
        $table->id(); // Crea una columna 'id', autoincremental y llave primaria
        
        // ¡Aquí añadimos nuestras columnas!
        $table->string('titulo'); // VARCHAR(255)
        $table->text('contenido'); // TEXT
        $table->boolean('esta_publicado')->default(false); // BOOLEAN
        
        $table->timestamps(); // Crea mágicamente las columnas 'created_at' y 'updated_at'
    });
}
```

### El método `down()` (El botón de pánico)
Aquí le decimos a Laravel cómo **deshacer** lo que hicimos en el método `up()`. Si en `up()` creamos una tabla, en `down()` debemos borrarla.

```php
public function down(): void
{
    Schema::dropIfExists('posts');
}
```

## 🚀 3. Ejecutando las Migraciones

Una vez que has definido tus columnas, tienes que decirle a Laravel que envíe esas instrucciones a tu base de datos (SQLite o MySQL).

En tu terminal ejecuta:
```bash
php artisan migrate
```

¡Listo! Si revisas tu base de datos, verás que la tabla `posts` ha sido creada exactamente con las columnas que definiste.

## ⏪ 4. Deshaciendo cambios (Rollback)

¿Te equivocaste y olvidaste una columna justo después de ejecutar el comando? No vayas a borrar la tabla a mano. Usa el comando de rollback para ejecutar el método `down()` de tu última migración:

```bash
php artisan migrate:rollback
```
Luego puedes modificar tu archivo y volver a ejecutar `php artisan migrate`.

---

### 🚀 Siguiente paso
Ahora nuestra base de datos ya tiene la tabla `posts` lista para recibir información. Pero nuestro código aún no sabe cómo hablar con esa tabla. En el siguiente tema, aprenderemos a crear **Modelos**, que serán los "representantes legales" de nuestras tablas dentro de Laravel.