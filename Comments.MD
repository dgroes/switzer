## CXX: Consideraciones entre Windows y Linux
La creación del proyecto se realizará entre Windows 10 y Ubuntu. Trabajar entre dichos OS es completamente posible y compatible, para que esto sea posible es importante tener esto en cuenta.

### 1. Case sensitivity (mayúsculas/minúsculas)
Ubuntu es sensible a mayúsculas/minúsculas, pero en Windows no.
```php
require 'App/Models/User.php';
```
⤷ En Windows funcionará igual si es 'app/Models/User.php', pero en Linux fallará.
- Será importante usar rutas, nombres de ficheros, y namespaces con el mismo casing exacto
- En Git, se puede habilitar la detección de cambios en mayúsculas/minúsculas con:
```bash
git config core.ignorecase false
```
⤷ Esto hace que Git actúe más parecido a como lo haría en Ubuntu, lo cual es más seguro si vas a trabajar en ambos entornos. (**ESTE COMANDO DEBERÍA EJECUTARSE EN WINDOWS**)

### 2. Líneas de comandos y dependencias
Obviamente se deberá tener lo mismo instalado en ambos sistemas
- PHP
- Composer
- Node.js NPM
- Laravel CLI (opcional)
- SQLite/Mysql
- Git

### 3. .env y configuración local
El fichero `.env` puede tener diferencias según el entorno
```js
DB_CONNECTION=sqlite
DB_DATABASE=/ruta/a/base.sqlite
```
⤷ En Ubuntu y Windows la ruta cambia, por eso:
- Usar `.env` por entorno  (`.env.local`, `.env.windows`) y cambiarlo cuando se desarrolle en un OS en concreto.
- Usar rutas relativas si es posible:
```js
DB_DATABASE=database/database.sqlite
```

### 4. Permisos de archivos
Ubuntu usa permisos de archivos, Windows no mucho, por eso en Ubuntu se debe:
```bash
chmod -R 775 storage bootstrap/cache
```
Después de clonar o mover el proyecto a Ubuntu, se deberá asegurar de dar permisos a las carpetas storage y bootstrap/cache.

### 5. Vite / Laravel Mix / Node
Las rutas de Vite o Laravel Mix pueden tener problemas si hay diferencias en los archivos JS/CSS
- Asegurarse que el `node_modules` no esté versionado (.gitignore lo incluye por defecto).
- Ejecutar `npm install` al cambiar de sistema operativo.

### 6. Git y línea de fin de archivo (LF/CRLF)
Windows usa CRLF, Linux usa LF. Laravel recomienda:
```bash
git config --global core.autocrlf input
```
Esto fuerza Git a convertir los CRLF a LF al guardar en el repo.

### 7. Usar WSL (opcional pero recomendado en Windows)
Si se desea una buena compatibilidad con Linux desde Windows, se puede usar **WSL (Windows Subsystem for Linux)** con Ubuntu instalado. Esto da un entorno Ubuntu dentro de Windows. Esto da las siguientes ventajas:
- Mismos comandos de terminal
- Mismo estructura de permisos y casing
- Mayor compatibilidad con Laravel

Sugerencia de ChatGPT
| Ítem                       | Acción sugerida                                  |
| -------------------------- | ------------------------------------------------ |
| `.env`                     | Ajustar rutas a la base de datos, archivos, etc. |
| `chmod`                    | No aplica en Windows, pero necesario en Linux    |
| `composer install`         | Ejecutar para reinstalar dependencias PHP        |
| `npm install`              | Ejecutar para dependencias JS                    |
| `php artisan key:generate` | Solo si no tienes `.env` listo                   |
| `php artisan migrate`      | Asegúrate de tener la BD bien conectada          |
| Servidor local             | Usar `php artisan serve`                         |


## C00: Instalación y Configuración de entorno
*Comentario no vinculado con un fichero*

Herramientas a utilizar
- Laravel 11
- Filament
- Tailwind
- TypeScript (aun no decidido)
- SQLite

### Instalación
A día de hoy está Laravel 12, pero se optará por la versión 11, debido a preferencias de desarrollo, como el utilizar Breeze, entre otras configuraciones.

`composer create-project laravel/laravel:^11.0 nombre-del-proyecto`
Con este comando se creará el proyecto con la versión de Laravel **especificada**

Luego dentro de la carpeta al ejecutar `php artisan serve` se podrá comprobar si la instalación fue completada y correcta accediendo a http://127.0.0.1:8000.

Será importante instalar Breeze, con el comando: `composer require laravel/breeze --dev` estará listo para segur con Livewire.

Ahora para instalar Livewire se deberá ejecutar: `php artisan breeze:install livewire`

Instalación de dependencias de Node con `npm install && npm run dev`, con este comando se realizan 2 cosas importante:
- Instalar todas las dependencias JS/Node.js/ definidas en el package.json (incluyendo Tailwind CSS, Alpine.js, Vite, etc.)
- Estas dependencias son necesarias para:
	- Compilar los estilos CSS (Tailwind)
	- Procesar el JavaScript (Alpine.js, Livewire)
	- Manejar los assets con Vite

Luego de esta instalación con `npm run deb` se compila los assets en modo desarrollo (más rápido, sin optimizadores finales ). Generando en `/public/build`
- Los archivos CSS compilados
- Los archivos JavaScript procesados
- El manifiesto que Vite usa para cargar los assets

Esto último es importante ya que Breeze usa
- **Tailwind CSS** para estilos
- **Vite** como bundler (reemplazo de Laravel Mix)
- **Alpine.js** (en el stack Livewire)

Sin la ejecución de estos comando:
- Los estilos no aparecerán
- Alpine.js no funcionará
- La autenticación no tendrá estilos adecuados

Si se hacen cambios en:  `resources/js/app.js` o `resources/css/app.css` se deberá volver ejecutar `npm run dev` o `npm run watch` para compilación automática al detectar cambios

#### Qué es LiveWire
Laravel Breeze también ofrece un scaffolding de [Livewire](https://livewire.laravel.com/). Livewire es una forma poderosa de construir interfaces de usuario dinámicas y reactivas en el frontend utilizando solo PHP. Livewire es una excelente opción para equipos que utilizan principalmente plantillas Blade y buscan una alternativa más simple a los frameworks SPA impulsados por JavaScript como Vue y React. Para utilizar la pila de Livewire, puedes seleccionar la pila frontend de Livewire al ejecutar el comando Artisan `breeze:install`.

#### Qué es Breeze?
[Laravel Breeze](https://github.com/laravel/breeze) es una implementación mínima y simple de todas las [funciones de autenticación](https://documentacionlaravel.com/docs/11.x/authentication) de Laravel, incluyendo inicio de sesión, registro, restablecimiento de contraseña, verificación de correo electrónico y confirmación de contraseña. Además, Breeze incluye una simple página de "perfil" donde el usuario puede actualizar su nombre, dirección de correo electrónico y contraseña. La capa de vista predeterminada de Laravel Breeze está compuesta por simples [plantillas Blade](https://documentacionlaravel.com/docs/11.x/blade) estilizadas con [Tailwind CSS](https://tailwindcss.com/). Además, Breeze ofrece opciones de scaffolding basadas en [Livewire](https://livewire.laravel.com/) o [Inertia](https://inertiajs.com/), con la opción de usar Vue o React para el scaffolding basado en Inertia.


## 01: Creación de la BD
Enlace a las migraciones: [Migraciones L11](https://documentacionlaravel.com/docs/11.x/migrations)

## C02: Creación de Usuario por defecto (Con Seeder)
Para poder crear un usuario por defecto y que esté anclado con las seeds será de la siguiente forma:x

### Creación de un Seeder
Al ejectuar este comando se creará un seeder especifico:
`php artisan make:seeder UserSeeder`


##

##

##

##
