# Instalación de Laravel 8.x
Laravel se instalará parcialmente en el cliente cuando sea hecho el primer GIT Commit, pero para que esté instalado por completo habrá que cambiar al directio `laravel` y ejecutar los siguientes comandos.
```bash
$ composer install
```
El grupo de Apache suele ser `www-data` pero en caso de desconocer o para asegurarse de saber el nombre del grupo, puede ser ejecutado el siguiente comando
```bash
$ ps aux | egrep '(apache|httpd)'
```
Ahora, establecer los permision y propiedad de todo lo contenido en el directorio. `$USER` es el actual usuario conectado al terminal del servidor pero en caso contrario que el propietario del directorio de la aplicación sea otro, usar el nombre correspondiente.
```bash
$ sudo find . -type f -exec chmod 644 {} \;
$ sudo find . -type d -exec chmod 755 {} \;
$ sudo chown -R $USER:www-data .
$ sudo chgrp -R www-data storage bootstrap/cache
$ sudo chmod -R ug+rwx storage bootstrap/cache
```
Seguido, se deberá establecer la clave que Laravel requiere para la aplicación y vaciar la misma (puesta a cero) de cualquier fichero no deseado en la instalación 
```bash
$ php artisan key:generate
$ php artisan cache:clear
$ php artisan config:clear
```
En cuanto a Composer, se requiere que se actualice el índice de las clases
```bash
$ composer dump-autoload
```
En cuanto a la configuración con la base de datos en Laravel, los datos de la conexión del cliente se suele establecer en el fichero `.env` ubicado en la raíz del directorio `laravel`.
```bash
$ sudo nano .env 
```
Fichero:
```bash
APP_NAME=Laravel
APP_ENV=local
APP_KEY=base64:bFQsdwjwgdqiD9s9LEZ8MWa5CEvNG1GndzAc8HrDcF8=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE={database_name}
DB_USERNAME={database_username}
DB_PASSWORD={database_password}

BROADCAST_DRIVER=log
CACHE_DRIVER=file
SESSION_DRIVER=file
QUEUE_DRIVER=sync
```
Pero en el caso de el corriente proyecto, se ha modificado el fichero `laravel/config/database.php` en las siguientes líneas para que se utilice los parámetros de conexión establecidos en el fichero `/config_BBDD.php`:
```php
<?php
use Illuminate\Support\Str;

require_once __DIR__ . '/../../' . 'config_BBDD.php';

return [
  
  #... lines
  'connections' => [

        #... lines

        'mysql' => [
            'driver' => 'mysql',
            'url' => env('DATABASE_URL'),
            'host' => BBDD_server, //env('DB_HOST', '127.0.0.1'),
            'port' => env('DB_PORT', '3306'),
            'database' => BBDD_name, //env('DB_DATABASE', 'forge'),
            'username' => BBDD_user, //env('DB_USERNAME', 'forge'),
            'password' => BBDD_pass, //env('DB_PASSWORD', ''),
            'unix_socket' => env('DB_SOCKET', ''),
            'charset' => 'utf8mb4',
            'collation' => 'utf8mb4_unicode_ci',
            'prefix' => '',
            'prefix_indexes' => true,
            'strict' => true,
            'engine' => null,
            'options' => extension_loaded('pdo_mysql') ? array_filter([
                PDO::MYSQL_ATTR_SSL_CA => env('MYSQL_ATTR_SSL_CA'),
            ]) : [],
        ],
  #... lines
  ],

];
```

#### Cabe destacar que toda esta configuración será ejecutada solo la primera vez. Luego, cuando sea actualizada la aplicación a través de los commits, se ejecutará solamente los comandos correspondientes a la actualización de la base de datos tanto para migraciones o seeders.
