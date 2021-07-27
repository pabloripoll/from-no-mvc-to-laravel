# Session

Ejemplo de uso de `$_SESSION` en controlador
```bash
<?php

namespace App\Http\Controllers;

use App\Session\Session;

class ExampleController
{    
    public function ListPage()
    {
        $session = new Session;
        
        $user_id = ($session()->has('user.id') ? $session()->get('user.id') : false;
    
        return $user_id;
    }
}
```
Ejemplo de uso de `$_SESSION` a través de `Global Functions` como `session()`
```php
<?php

$user_id = (session()->has('user.id') ? session()->get('user.id') : false);

echo $user_id;

```

### Método ALL 
Para obtener todas las variables de sesión como el siguiente ejemplo
```php
$session = $_SESSION;
```
sólo bastará con llamar a la función session()
```php
$session = session()->all();
```
Y consecuentemente también se podría llamar a una clave de la siguiente manera:
```php
echo session()->all()['user']['id'];
```
Pero para llamar a claves dentro de la sesión, convenientemente se podrá llamar con el método GET.

### Método GET 
Ejemplo de sesión:
```bash
$_SESSION
├── [bands]
│   ├── [0] pantera
│   └── [1] metallica
│   └── [2] megadeth
└── [other]
```
Para obtener todas las claves de `bands`:
```php
$bands = session()->get('bands');
```
Para obtener una clave ordinal:
```php
$metallica = session()->get('bands.metal')[1];
```
Salida:
```bash
metallica
```

## PUT
// example: $_SESSION['bands']['metal'] = 'pantera';
echo '<pre>';
// print_r( session()->put('bands.metal', 'metallica') );                   // update with string value
// print_r( session()->put('bands.metal', ['metallica', 'pantera']) );      // update with array value
// print_r( session()->put('bands.pop', 'michael jackson') );               // create new string/array value on parent key
// print_r( session()->put('bands.pop.classic', 'michael jackson') );       // exception error on 2rd key "pop"
echo '</pre>';

// PUSH session()->push('{existing.arrayPath}', '{array}'); //
echo 'PUSH';
// example: $_SESSION['bands']['metal'] = 'pantera';
echo '<pre>';
// print_r( session()->push('bands.metal', 'megadeth') );               // update update key with [1] => metallica
// print_r( session()->push('bands', 'pop') );                          // update key with [0] => pop (key one level depth sencond mehod attribute is a must)
// print_r( session()->push('bands.pop') );                             // update key with [pop] => ''
// print_r( session()->push('bands.pop', 'michael jackson') );          // update key with [pop] => '' (second attribute is overlap with empty value)
// print_r( session()->push('bands.pop.classic', 'michael jackson') );  // exception error on 2rd key "pop"
echo '</pre>';

// FLUSH session()->flush('{existing.arrayPath}'); //
echo 'FLUSH';
echo '<pre>';
// print_r( session()->flush('bands.metal') ); // update
echo '</pre>';

// FORGET session()->forget('{existing.arrayPath}'); //
echo 'FORGET';
echo '<pre>';
// print_r( session()->forget('bands') ); // update
// print_r( session()->forget('metal') ); // update
echo '</pre>';


