# Migraciones

Es recomendable que el mantenimiento de la base de datos sea administrado por el paquete Doctrine y sea ejecutado por alguno de los frameworks Laravel ó Symfony.
Luego de varios exámenes de ambos frameworks, se considera a Symfony con exacelente prestación para la manipulación de las altas y bajas de migraciones y es de menor peso en bytes, ya que requiere menores cantidades de dependencias de otros paquetes de Composer.
Sin embargo, con respecto con la experiencia de usuario para la administración y mantenimiento, Laravel porta una mejor interfaz por lo que la recomendación de la elección de Laravel sobre Symfony, es enfocado en la practicidad.

La aplicación para el mantenimiento de la base de datos estará desacoplada de la aplicación principal ya que la ejecución de los comandos para la migración de las diferentes versionas de migraciones, será exclusivamente a través de la entreda por terminal en el servidor.

Como la base de datos ya está existe y a su vez contiene datos de uso por el cliente, solo bastará que cada commit de una rama en particular, dentro del servidor del cliente se ejecute dentro del directorio donde reside Laravel, el comando `php artisan migrate`. Este punto se desarrollará luego de la description de mantenimiento *workflow*

## Estructura
La estructura donde estará montada ésta aplicación será la siguiente:
```bash
root
├── app
|   └── mvc
│       ├── app
│       └── bootstrap
│       └── ...
└── *laravel*
```

## Workflow

### # Documentación

Antes que nada, los siguientes enlaces serán la documentación a consultar para el mantenimiento.

[Documentación para la composición de las migraciones](https://laravel.com/docs/8.x/migrations)

[Documentación de los esquemas para las migraciones](https://laravel.com/docs/5.0/schema)

### # Crear nueva tabla

Se debe cambiar a directorio `laravel` para ejecutar los comandos a través de `php artisan`.
```bash
$ cd /var/www/{cloud-gestion-dir-path}/laravel/
```
A continuación se tomará de ejemplo la rama Entrega de Material que necesitará una nueva tabla en la base de datos: `entrega_material`
```bash
$ php artisan make:migration create_entrega_material_table
```
Salida:
```bash
Created Migration: 2021_07_29_115502_create_entrega_material_table
```
Fichero php a modificar ubicado en `/laravel/database/migrations` será en se establece el esquema de la tabla.
```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateEntregaMaterialTable extends Migration
{
    /**
     * Table name property.
     *
     * @return string
     */
    private $table = 'entrega_material';

    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create($this->table, function (Blueprint $table) {
            $table->increments('IdEntregaMaterial');
            $table->integer('IdSerie');
            $table->integer('IdAlmacen');
            $table->integer('IdTrabajador');
            $table->integer('Numero');
            $table->date('Fecha');
            $table->boolean('Estado');
            $table->decimal('Total', 10, 2);
            $table->text('Contenido');
            $table->text('Notas');
            $table->string('URLFirma', 256);
            $table->timestamps();

            $table->index('IdSerie');
            $table->index('IdAlmacen');
            $table->index('IdTrabajador');
            $table->index('Numero');
            $table->index('Fecha');
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists($this->table);
    }
}
```
Una vez establecido las columnas, índices y claves foráneas de la table, se procede a crear la tabla en la base de datos con el siguiente comando
```bash
$ php artisan migrate
```
Salida:
```bash
Migrating: 2021_07_29_115502_create_entrega_material_table
Migrated:  2021_07_29_115502_create_entrega_material_table (560.53ms)
```
En caso de retroceder en ésta última migración, se deberá realizar a través de un rollback
```bash
$ php artisan migrate:rollback
```
*Si se ha producido un error al ejecutar la migración, el comando migrate:rollaback no eliminará la tabla si ya fué creada puesto que puede ocurrir que un error se haya producido al crear una columna y como la grabación de la migración sucede al finalizar cada creación de tabla completa, se deberá proceder eliminar (DROP) la tabla manualmente.*

### # Actualizar columnas de tabla existente
A continuación se tomará de ejemplo la rama Entrega de Material que necesitará una nueva tabla en la base de datos: `entrega_material`
```bash
$ php artisan make:migration add_various_to_entrega_material_table
```
Fichero php:
```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class AddVariousToEntregaMaterialTable extends Migration
{
    /**
     * Table name property.
     *
     * @return string
     */
    private $table = 'entrega_material';

    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table($this->table, function (Blueprint $table) {
            $table->boolean('URLFirmaEstado')->after('URLFirma');
            $table->string('Observaciones', 128)->after('URLFirmaEstado');
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::table($this->table, function (Blueprint $table) {
            $table->dropColumn('Observaciones');
            $table->dropColumn('URLFirmaEstado');
        });
    }
}
```
Una vez completado el esquema de actualización, ejecutar la migración
```bash
$ php artisan migrate
```

### # Renombrar columnas de una tabla existente
Para actualizar el nombre de una ó varias columnas de una tabla existente, se puede utilizar el siguiente ejemplo
```bash
$ php artisan make:migration rename_column_from_entrega_material_table
```
Fichero php:
```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class RenameColumnFromEntregaMaterialTable extends Migration
{
    /**
     * Table name property.
     *
     * @return string
     */
    private $table = 'entrega_material';

    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table($this->table, function (Blueprint $table) {
            $table->renameColumn('URLFirmaEstado', 'FirmaEstado');
        });
    }
  
    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::table($this->table, function (Blueprint $table) {
            $table->renameColumn('FirmaEstado', 'URLFirmaEstado'); // TESTEAR!!!
        });
    }
}
```
Una vez completado el esquema de actualización, ejecutar la migración
```bash
$ php artisan migrate
```

### # Modificar nombre de tabla existente
*(Tener en cuenta si la tabla posee claves foráneas e índices dependientes [StackOverflow](https://stackoverflow.com/a/54453430) )*

Para actualizar el nombre de una tabla existente en la base de datos, utilizar el siguiente ejemplo
```bash
$ php artisan make:migration rename_entrega_material_to_entregas_de_materiales_table
```
Fichero php:
```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class RenameColumnFromEntregaMaterialTable extends Migration
{
    Schema::rename('entrega_material', 'entregas_de_materiales');   
}
```
Una vez completado el esquema de actualización, ejecutar la migración
```bash
$ php artisan migrate
```

### # Eliminar tabla existente
*(Tener en cuenta si la tabla posee claves foráneas e índices dependientes [StackOverflow](https://stackoverflow.com/a/54453430) )*

Para eliminar una tabla existente en la base de datos, utilizar el siguiente ejemplo
```bash
$ php artisan make:migration drop_entregas_de_materiales_table
```
Fichero php:
```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class RenameColumnFromEntregaMaterialTable extends Migration
{
    Schema::dropIfExists('entregas_de_materiales');   
}
```
Una vez completado el esquema de actualización, ejecutar la migración
```bash
$ php artisan migrate
```

### # Migración en producción
Cabe destacar que toda esta configuración será ejecutada solo la primera vez.
Luego, como menciona al principio, en el workflow de las fuciones de la rama de desarrollo con la rama maestra, se ejecutará solamente los comandos correspondientes a la actualización de la base de datos (y los seeds de las tablas, si los posee) dentro del directorio de Laravel:
```bash
$ cd /var/www/{cloud-gestion-dir-path}/laravel/
$ php artisan migrate
```
