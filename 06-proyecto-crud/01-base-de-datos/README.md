# Tema 1: Planificación y Base de Datos

Todo buen proyecto de software comienza lejos del código: planificando la estructura de los datos. Si la base de datos está mal diseñada, toda la aplicación sufrirá.

Para nuestro Catálogo, necesitamos organizar los productos de forma lógica. Por lo tanto, tendremos **dos tablas**:
1. **Categorías** (ej: Electrónica, Ropa, Hogar).
2. **Productos** (ej: Laptop, Camiseta, Lámpara).

*Regla de negocio:* Una Categoría tiene muchos Productos (Relación 1 a N).



## 🛠️ 1. Creando las Migraciones

Abre tu terminal y vamos a pedirle a Artisan que nos cree los archivos de migración. 

*Nota: Siempre es mejor crear la tabla independiente primero (Categorías) y luego la tabla dependiente (Productos).*

```bash
# Creamos la migración para categorías
php artisan make:migration create_categorias_table

# Creamos la migración para productos
php artisan make:migration create_productos_table
```

## 📝 2. Definiendo la Tabla Categorías

Ve a `database/migrations/` y abre el archivo que termina en `_create_categorias_table.php`. Vamos a añadirle un nombre y una descripción opcional:

```php
public function up(): void
{
    Schema::create('categorias', function (Blueprint $table) {
        $table->id(); // Llave primaria automática
        
        $table->string('nombre')->unique(); // El nombre no puede repetirse
        $table->text('descripcion')->nullable(); // nullable() significa que este campo puede quedar vacío
        
        $table->timestamps();
    });
}
```

## 📦 3. Definiendo la Tabla Productos

Ahora abre el archivo `_create_productos_table.php`. Aquí es donde la cosa se pone interesante, porque necesitamos guardar diferentes tipos de datos (texto, decimales, enteros y la llave foránea).

```php
public function up(): void
{
    Schema::create('productos', function (Blueprint $table) {
        $table->id();
        
        // 1. La Llave Foránea (Conexión con la tabla categorias)
        // cascadeOnDelete asegura que si borramos la categoría "Laptops",
        // se borren todas las laptops para evitar errores.
        $table->foreignId('categoria_id')->constrained('categorias')->cascadeOnDelete();
        
        // 2. Información básica
        $table->string('nombre');
        $table->text('descripcion');
        
        // 3. Precio y Stock
        // decimal('precio', 8, 2) significa: 8 dígitos en total, 2 de ellos decimales (ej: 999999.99)
        $table->decimal('precio', 8, 2); 
        // integer() para números enteros (no puedes tener 1.5 camisetas en stock)
        $table->integer('stock')->default(0); 
        
        // 4. Imagen (Guardaremos la ruta del archivo, no el archivo físico)
        $table->string('imagen_url')->nullable(); 
        
        $table->timestamps();
    });
}
```

## 🚀 4. Construyendo la Base de Datos

Una vez que tus archivos estén guardados, es hora de materializar estas tablas en tu base de datos SQLite o MySQL.

Ejecuta en tu terminal:
```bash
php artisan migrate
```

Si todo ha ido bien, verás el mensaje verde de "Done". ¡Nuestros cimientos están listos!

---

### 🚀 Siguiente paso
Las tablas ya existen, pero están completamente vacías y nuestro código PHP aún no sabe interactuar con ellas. En el siguiente tema, crearemos los **Modelos, Relaciones y usaremos Factories/Seeders** para llenar nuestra tienda de productos de prueba en segundos.