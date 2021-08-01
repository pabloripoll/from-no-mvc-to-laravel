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
Por último, es fundamental establecer los datos de la conexión a la base de datos del cliente
```bash
$ sudo nano .env 
```
.env file with minimum params required
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
#### Cabe destacar que toda esta configuración será ejecutada solo la primera vez. Luego, como menciona al principio, en el workflow diario, se ejecutará solamente los comandos correspondientes a la actualización de la base de datos:
