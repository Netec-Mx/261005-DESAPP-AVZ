# 7 Práctica: Autenticación y gestión básica de roles con Laravel 11.x y MySQL 8.4.x

## Metadatos

| Atributo | Detalle |
| :--- | :--- |
| **Duración** | 180 minutos |
| **Complejidad** | Media |
| **Nivel de Bloom** | Crear |
| **Objetivos** | - Integrar el andamiaje de autenticación segura utilizando Laravel Breeze 2.2.0.<br>- Implementar un control de acceso basado en roles simples añadiendo atributos específicos en la tabla de usuarios.<br>- Desarrollar un middleware personalizado para restringir el acceso a módulos críticos de la aplicación según el rol asignado. |

---

## Descripción General

En este laboratorio práctico, el estudiante configurará un sistema de autenticación de nivel profesional utilizando **Laravel Breeze**, modificando su comportamiento nativo para admitir un control de acceso basado en roles (RBAC ligero). Se añadirá una columna `role` a la tabla de usuarios para diferenciar entre administradores (`admin`) y usuarios estándar (`user`). 

Además, se diseñará e implementará un *Middleware* personalizado que interceptará las peticiones HTTP dirigidas a los endpoints críticos de gestión de tareas (edición y eliminación), bloqueando el acceso no autorizado con un código de estado `403 Forbidden`. Por último, se adaptarán las interfaces Blade generadas por Breeze para integrarse de forma armoniosa con el diseño visual del proyecto y se sembrará la base de datos con cuentas de prueba listas para la validación funcional.

---

## Objetivos de Aprendizaje

Al finalizar este laboratorio, serás capaz de:
- [ ] Instalar y configurar el paquete de autenticación **Laravel Breeze** sobre una pila de desarrollo basada en Blade, Vite y Tailwind CSS.
- [ ] Alterar la estructura de la base de datos mediante migraciones de Laravel para soportar roles de usuario predefinidos.
- [ ] Crear y registrar un *Middleware* personalizado en Laravel para filtrar peticiones HTTP según roles de seguridad.
- [ ] Desarrollar cargadores de datos estructurados (*Seeders*) para inicializar el entorno con perfiles de prueba de administración y operación.
- [ ] Personalizar interfaces Blade condicionales utilizando directivas de autorización (`@can`, `@if`) para mejorar la UX del usuario final en función de sus privilegios.

---

## Prerrequisitos

### Requisitos de Conocimiento
- Comprensión sólida del ciclo de vida de una petición en Laravel y arquitectura MVC.
- Familiaridad con el uso de migraciones de bases de datos relacionales en Laravel.
- Manejo básico de Git (creación de ramas, commits y resolución de conflictos).
- Conceptos fundamentales de seguridad web: Autenticación de sesiones, cookies seguras y protección contra CSRF (*Cross-Site Request Forgery*).

### Accesos y Licencias
- Conectividad a Internet sin restricciones para la descarga de paquetes a través de Composer y NPM.
- Servidor de base de datos MySQL configurado y accesible con las credenciales indicadas.
- Opcional: En caso de utilizar asistentes de IA para el desarrollo del código (como **GitHub Copilot** o **Microsoft 365 Copilot Chat**), se requiere disponer de una licencia activa (por ejemplo, *GitHub Copilot Enterprise* o *Individual*), asegurando que la herramienta esté integrada y configurada correctamente en el entorno de desarrollo (por ejemplo, en VS Code u Cursor).

---

## Entorno de Laboratorio

Para garantizar la reproducibilidad de esta práctica, el entorno de desarrollo debe ajustarse a las especificaciones y herramientas listadas a continuación.

### Especificaciones de Hardware (Mínimo Recomendado)
- **Procesador**: Arquitectura x86_64 o ARM64 (Apple Silicon) con un mínimo de 4 núcleos físicos.
- **Memoria RAM**: 8 GB (Recomendado 16 GB).
- **Almacenamiento**: Disco de estado sólido (SSD) con al menos 20 GB de espacio libre.

### Especificaciones de Software y Herramientas

| Herramienta / Tecnología | Versión Requerida | URL de Descarga Oficial |
| :--- | :--- | :--- |
| **PHP** | v8.2.x o v8.3.x / v8.4.0 (con extensiones pdo, openssl, mbstring, dom) | [php.net/downloads](https://www.php.net/downloads) |
| **Composer** | v2.10.3 | [getcomposer.org/download](https://getcomposer.org) |
| **Node.js / npm** | v24.21.0 / v11.1.0 | [nodejs.org/en/download](https://nodejs.org) |
| **MySQL Community Server**| v8.4.0 (ejecutándose en puerto TCP 3306) | [dev.mysql.com/downloads](https://dev.mysql.com/downloads/mysql/) |
| **Laravel Framework** | v11.8.0 | [laravel.com](https://laravel.com) |
| **Laravel Breeze** | v2.2.0 | [laravel.com/docs/11.x/starter-kits](https://laravel.com/docs/11.x/starter-kits) |
| **Tailwind CSS** | v4.0.0-alpha.15 (o compatible v4.0.0) | [tailwindcss.com](https://tailwindcss.com) |
| **Bruno** | v1.38.0 | [usebruno.com](https://usebruno.com) |

### Parámetros de Configuración del Entorno

Asegure el uso de las siguientes variables globales y rutas del sistema durante toda la práctica:
- **Directorio de Trabajo Principal**: `~/labs/taskflow-app`
- **Base de Datos MySQL**: `taskflow_db`
- **Usuario de la Base de Datos**: `taskflow_user`
- **Contraseña de la Base de Datos**: `TaskFlowSecure2026!`
- **Puerto de Laravel**: `http://localhost:8000`
- **Puerto de Vite**: `http://localhost:5173`

---

## Instrucciones Paso a Paso

### Paso 1: Configurar el Repositorio y Entorno de Trabajo

**Objetivo**: Preparar la rama de control de versiones y validar la conectividad de la base de datos antes de inyectar dependencias externas.

**Instrucciones**:

1. Navegue al directorio de trabajo asignado en su terminal:
   ```bash
   cd ~/labs/taskflow-app
   ```

2. Cree y cámbiese a la rama de desarrollo dedicada a este laboratorio:
   ```bash
   git checkout -b lab-03-autenticacion-roles
   ```

3. Abra el archivo `.env` ubicado en la raíz del proyecto y configure los parámetros de conexión para la base de datos MySQL corporativa:
   ```env
   DB_CONNECTION=mysql
   DB_HOST=127.0.0.1
   DB_PORT=3306
   DB_DATABASE=taskflow_db
   DB_USERNAME=taskflow_user
   DB_PASSWORD=TaskFlowSecure2026!
   ```

4. Ejecute una prueba de verificación de conexión ejecutando las migraciones base actuales para certificar la conexión con MySQL:
   ```bash
   php artisan db:show
   ```

**Resultado Esperado**:
La terminal mostrará información detallada del servidor de base de datos MySQL (versión 8.4.x, esquemas existentes y tablas actuales), confirmando que la conexión TCP es exitosa.

**Verificación**:
```bash
## Debería retornar un resumen de la base de datos libre de errores de conexión.
php artisan db:show
```

---

### Paso 2: Instalar y Configurar Laravel Breeze

**Objetivo**: Instalar el paquete de autenticación Laravel Breeze y desplegar el scaffolding para la pila clásica basada en plantillas Blade de Laravel.

**Instrucciones**:

1. Descargue el paquete de Laravel Breeze en el entorno de desarrollo utilizando Composer:
   ```bash
   composer require laravel/breeze:^2.2 --dev
   ```

2. Ejecute el comando interactivo de instalación de Breeze seleccionando la pila tecnológica adecuada:
   ```bash
   php artisan breeze:install blade
   ```
   *Durante las preguntas interactivas, seleccione las siguientes opciones:*
   - **¿Soporte para modo oscuro?**: `no` (o `yes` si desea la implementación visual oscura nativa).
   - **¿Framework de pruebas preferido?**: `Pest` (o `PHPUnit` si está predeterminado en su flujo).

3. Instale las dependencias de frontend necesarias para Tailwind CSS v4 y comience el proceso de compilación del servidor de desarrollo con Vite:
   ```bash
   npm install
   npm run dev
   ```

**Resultado Esperado**:
Vite levantará su servidor de desarrollo local de manera exitosa en `http://localhost:5173` y se generará una estructura física de archivos dentro de `app/Http/Controllers/Auth/`, `routes/auth.php`, y `resources/views/auth/`.

**Verificación**:
Inspeccione que el archivo `routes/web.php` haya sido actualizado automáticamente incluyendo el archivo de rutas de Breeze:
```php
require __DIR__.'/auth.php';
```

---

### Paso 3: Modificar la Estructura de la Base de Datos para Control de Roles

**Objetivo**: Alterar la migración original de usuarios para integrar una columna de rol e implementar un valor predeterminado seguro de privilegios mínimos.

**Instrucciones**:

1. Abra el archivo de migración principal para la creación de usuarios localizado en `database/migrations/0001_01_01_000000_create_users_table.php` (o la migración equivalente del sistema).

2. Añada la columna `role` a la definición del esquema de la tabla de usuarios. Asegúrese de que el campo sea un `string`, con un valor predeterminado `user` y que esté restringido para evitar valores nulos:
   ```php
   Schema::create('users', function (Blueprint $table) {
       $table->id();
       $table->string('name');
       $table->string('email')->unique();
       $table->timestamp('email_verified_at')->nullable();
       $table->string('password');
       $table->string('role')->default('user'); // Nueva columna de rol por defecto
       $table->rememberToken();
       $table->timestamps();
   });
   ```

3. Modifique el modelo `User` en `app/Models/User.php` para asegurar que el atributo `role` esté protegido contra asignaciones masivas no controladas adicionándolo a la propiedad `$fillable`:
   ```php
   protected $fillable = [
       'name',
       'email',
       'password',
       'role', // Añadir rol a la asignación masiva segura
   ];
   ```

**Resultado Esperado**:
La migración de base de datos definirá con precisión el campo `role` como un parámetro estructurado de la tabla `users` con restricción de integridad por defecto.

**Verificación**:
Ejecute la reconstrucción limpia de la base de datos mediante:
```bash
php artisan migrate:fresh
```
Si la operación finaliza sin errores, el esquema se habrá reestructurado correctamente.

---

### Paso 4: Implementar Sembradores de Base de Datos para Cuentas de Prueba

**Objetivo**: Generar registros controlados en la base de datos para simular escenarios reales de administración y perfiles limitados de forma determinista.

**Instrucciones**:

1. Abra el archivo sembrador de base de datos general de su proyecto `database/seeders/DatabaseSeeder.php`.

2. Reemplace el método `run()` para forzar la creación de dos cuentas con contraseñas seguras predecibles basadas en el estándar del entorno. Utilice la fachada `Hash` para garantizar el cifrado nativo:
   ```php
   <?php

   namespace Database\Seeders;

   use App\Models\User;
   use Illuminate\Database\Seeder;
   use Illuminate\Support\Facades\Hash;

   class DatabaseSeeder extends Seeder
   {
       /**
        * Seed the application's database.
        */
       public function run(): void
       {
           // Crear un usuario con privilegios de Administrador
           User::factory()->create([
               'name' => 'Administrador TaskFlow',
               'email' => 'admin@taskflow.com',
               'password' => Hash::make('TaskFlowSecure2026!'),
               'role' => 'admin',
           ]);

           // Crear un usuario estándar básico
           User::factory()->create([
               'name' => 'Usuario Operador',
               'email' => 'user@taskflow.com',
               'password' => Hash::make('TaskFlowSecure2026!'),
               'role' => 'user',
           ]);
       }
   }
   ```

3. Ejecute la migración completa junto con el cargador de datos iniciales:
   ```bash
   php artisan migrate:fresh --seed
   ```

**Resultado Esperado**:
La consola informará que las tablas se borraron, se crearon de nuevo y se ejecutó exitosamente el `DatabaseSeeder`.

**Verificación**:
Consulte directamente la base de datos a través de la interfaz de consola de Laravel Artisan Tinker para verificar el guardado correcto:
```bash
php artisan tinker
```
Dentro de Tinker, ejecute el siguiente comando:
```php
App\Models\User::pluck('role', 'email')->toArray();
```
*Salida esperada de Tinker:*
```php
=> [
     "admin@taskflow.com" => "admin",
     "user@taskflow.com" => "user",
   ]
```
Salga de Tinker escribiendo `exit`.

---

### Paso 5: Construir y Registrar el Middleware Personalizado `CheckAdminRole`

**Objetivo**: Diseñar un middleware a nivel de enrutamiento que inspeccione la sesión del usuario autenticado y evalúe si posee privilegios de administrador.

**Instrucciones**:

1. Genere una clase de middleware personalizada utilizando el generador automático de código CLI de Laravel:
   ```bash
   php artisan make:middleware CheckAdminRole
   ```

2. Ubique y abra el archivo recién creado en `app/Http/Middleware/CheckAdminRole.php`.

3. Implemente la lógica de control de flujo. Si la petición no proviene de un usuario autenticado o su atributo `role` es diferente de `admin`, interrumpa inmediatamente el ciclo de vida de la petición arrojando una excepción HTTP con código `403` (Prohibido):
   ```php
   <?php

   namespace App\Http\Middleware;

   use Closure;
   use Illuminate\Http\Request;
   use Symfony\Component\HttpFoundation\Response;

   class CheckAdminRole
   {
       /**
        * Handle an incoming request.
        *
        * @param  \Illuminate\Http\Request  $request
        * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
        */
       public function handle(Request $request, Closure $next): Response
       {
           // Evaluar si el usuario está autenticado y si su rol corresponde a 'admin'
           if (!$request->user() || $request->user()->role !== 'admin') {
               abort(Response::HTTP_FORBIDDEN, 'Acceso denegado: Se requieren privilegios de administrador para realizar esta operación.');
           }

           return $next($request);
       }
   }
   ```

4. Registre el middleware dentro de la infraestructura moderna de Laravel 11. Abra el archivo de configuración bootstrap del framework `bootstrap/app.php` y configure el alias de middleware seguro para uso en enrutamientos web:
   ```php
   <?php

   use Illuminate\Foundation\Application;
   use Illuminate\Foundation\Configuration\Exceptions;
   use Illuminate\Foundation\Configuration\Middleware;

   return Application::configure(basePath: dirname(__DIR__))
       ->withRouting(
           web: __DIR__.'/../routes/web.php',
           commands: __DIR__.'/../routes/console.php',
           health: '/up',
       )
       ->withMiddleware(function (Middleware $middleware) {
           // Registro del alias del Middleware de Roles
           $middleware->alias([
               'role.admin' => \App\Http\Middleware\CheckAdminRole::class,
           ]);
       })
       ->withExceptions(function (Exceptions $exceptions) {
           //
       })->create();
   ```

**Resultado Esperado**:
El alias `role.admin` quedará registrado globalmente en la pila del cargador del núcleo de Laravel para enrutamientos dinámicos.

**Verificación**:
```bash
## Validar que no existan errores de sintaxis en el bootstrap compilando las rutas
php artisan route:list
```

---

### Paso 6: Aplicar Protección de Rutas con el Middleware de Roles

**Objetivo**: Restringir rutas de negocio específicas del sistema para garantizar que operaciones críticas de edición, guardado de modificaciones y borrado de tareas sean privativas del rol de administración.

**Instrucciones**:

1. Para propósitos de este laboratorio, emularemos un controlador básico de tareas. Cree un controlador rápido para simular las rutas administrativas:
   ```bash
   php artisan make:controller TaskController
   ```

2. Abra `app/Http/Controllers/TaskController.php` e inserte la lógica para renderizar la lista y las acciones críticas:
   ```php
   <?php

   namespace App\Http\Controllers;

   use Illuminate\Http\Request;

   class TaskController extends Controller
   {
       public function index()
       {
           return view('dashboard');
       }

       public function edit($id)
       {
           return "Formulario de edición para la tarea #" . e($id);
       }

       public function destroy($id)
       {
           return "La tarea #" . e($id) . " ha sido eliminada con éxito del sistema.";
       }
   }
   ```

3. Abra su archivo de enrutamiento web `routes/web.php` y estructure la protección de accesos:
   ```php
   <?php

   use App\Http\Controllers\ProfileController;
   use App\Http\Controllers\TaskController;
   use Illuminate\Support\Facades\Route;

   Route::get('/', function () {
       return view('welcome');
   });

   // Rutas generales para usuarios autenticados
   Route::middleware(['auth', 'verified'])->group(function () {
       Route::get('/dashboard', [TaskController::class, 'index'])->name('dashboard');
       Route::get('/profile', [ProfileController::class, 'edit'])->name('profile.edit');
       Route::patch('/profile', [ProfileController::class, 'update'])->name('profile.update');
       Route::delete('/profile', [ProfileController::class, 'destroy'])->name('profile.destroy');
       
       // Rutas Administrativas de Tareas: Protegidas por rol
       Route::middleware('role.admin')->group(function () {
           Route::get('/tasks/{id}/edit', [TaskController::class, 'edit'])->name('tasks.edit');
           Route::delete('/tasks/{id}', [TaskController::class, 'destroy'])->name('tasks.destroy');
       });
   });

   require __DIR__.'/auth.php';
   ```

**Resultado Esperado**:
Las rutas `/tasks/{id}/edit` y `/tasks/{id}` (DELETE) estarán bloqueadas por dos capas de seguridad: autenticación de usuario activa y validación de rol de administración.

**Verificación**:
```bash
## Deberá verificar que la estructura de filtros de seguridad esté bien mapeada
php artisan route:list --path=tasks
```
*Salida de la terminal esperada:*
```text
  GET|HEAD   tasks/{id}/edit ........... tasks.edit › TaskController@edit | web, auth, verified, role.admin
  DELETE     tasks/{id} ............. tasks.destroy › TaskController@destroy | web, auth, verified, role.admin
```

---

### Paso 7: Personalizar la Interfaz de Usuario Condicional según Roles

**Objetivo**: Modificar las plantillas Blade para ocultar o mostrar acciones críticas en el Panel de Control en base al perfil del usuario autenticado.

**Instrucciones**:

1. Abra la plantilla principal del panel de control ubicada en `resources/views/dashboard.blade.php`.

2. Modifique el bloque central del contenedor para pintar dinámicamente un listado de tareas ficticio y añadir los botones condicionales correspondientes:
   ```html
   <x-app-layout>
       <x-slot name="header">
           <h2 class="font-semibold text-xl text-gray-800 leading-tight">
               {{ __('Dashboard - Flujo de Tareas') }}
           </h2>
       </x-slot>

       <div class="py-12">
           <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
               <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
                   <div class="p-6 text-gray-900">
                       <h3 class="text-lg font-bold mb-4">Listado General de Tareas</h3>
                       
                       <!-- Bloque informativo de perfil actual -->
                       <div class="mb-6 p-4 rounded-md bg-blue-50 text-blue-800 border border-blue-200">
                           <span class="font-semibold">Perfil Autenticado:</span> 
                           {{ Auth::user()->name }} (Rol asignado: <strong class="underline">{{ strtoupper(Auth::user()->role) }}</strong>)
                       </div>

                       <div class="border-t border-gray-200 pt-4">
                           <div class="flex items-center justify-between p-3 bg-gray-50 rounded-lg mb-2">
                               <div>
                                   <p class="font-semibold text-gray-800">Tarea #101: Configurar Certificados SSL en el entorno local</p>
                                   <span class="text-xs text-red-500 font-medium">Prioridad Alta</span>
                               </div>
                               <div class="flex space-x-2">
                                   <!-- Renderizado Condicional de Acciones Basado en Roles -->
                                   @if(Auth::user()->role === 'admin')
                                       <a href="{{ route('tasks.edit', 101) }}" class="px-3 py-1 bg-amber-500 hover:bg-amber-600 text-white rounded text-sm transition-all">
                                           Editar (Admin)
                                       </a>
                                       <form action="{{ route('tasks.destroy', 101) }}" method="POST" class="inline">
                                           @csrf
                                           @method('DELETE')
                                           <button type="submit" class="px-3 py-1 bg-red-600 hover:bg-red-700 text-white rounded text-sm transition-all" onclick="return confirm('¿Seguro que desea eliminar esta tarea?')">
                                               Eliminar (Admin)
                                           </button>
                                       </form>
                                   @else
                                       <span class="text-sm text-gray-400 italic">Acciones restringidas para su rol</span>
                                   @endif
                               </div>
                           </div>
                       </div>
                   </div>
               </div>
           </div>
       </div>
   </x-app-layout>
   ```

**Resultado Esperado**:
El usuario con rol `user` visualizará la tarea pero sin controles activos para editar o borrar. El usuario `admin` verá de forma explícita las llamadas a la acción (*Call-to-Action*).

**Verificación**:
Levante el servidor de Laravel local:
```bash
php artisan serve
```
Acceda con su navegador e inicie sesión con `user@taskflow.com`. Valide que el panel muestre únicamente el texto de "Acciones restringidas para su rol". Cierre sesión e ingrese con `admin@taskflow.com` para comprobar que los botones se renderizan y operan con normalidad.

---

## Validación y Pruebas

Para garantizar que el sistema de autenticación y los middlewares de control de accesos operan bajo estándares de calidad elevados y seguros frente a ataques de omisión, se ejecutarán pruebas manuales exhaustivas.

### Escenario 1: Pruebas de Flujo Positivas (Usuarios Legítimos)

#### Prueba A: Autenticación de Usuario Regular
1. Abra un navegador web en modo incógnito.
2. Navegue a la dirección: `http://localhost:8000/login`
3. Introduzca las credenciales:
   - **Email**: `user@taskflow.com`
   - **Password**: `TaskFlowSecure2026!`
4. Haga clic en el botón de envío del formulario.

**Resultado Esperado en Navegador**:
El navegador debe redireccionar automáticamente a `http://localhost:8000/dashboard`, mostrando un banner azul con el mensaje: "Perfil Autenticado: Usuario Operador (Rol asignado: USER)" y la leyenda "Acciones restringidas para su rol".

#### Prueba B: Autenticación de Administrador
1. Cierre la sesión activa del usuario regular o elimine las cookies de sesión del navegador.
2. Navegue nuevamente a: `http://localhost:8000/login`
3. Introduzca las credenciales de administración:
   - **Email**: `admin@taskflow.com`
   - **Password**: `TaskFlowSecure2026!`
4. Presione Enter o haga clic en enviar.

**Resultado Esperado en Navegador**:
El sistema redireccionará a `http://localhost:8000/dashboard` desplegando el banner correspondiente con los botones **Editar (Admin)** y **Eliminar (Admin)** completamente visibles y funcionales.

---

### Escenario 2: Pruebas de Flujo Negativas y Adversarias (Intento de Evasión)

#### Prueba C: Bypass Directo de URL por Rol No Autorizado (Inyección de Navegación)
1. Autentíquese en el navegador utilizando las credenciales del usuario regular (`user@taskflow.com`).
2. Una vez situado en el panel de control, intente violar la seguridad escribiendo de forma manual y forzada la URL de edición restringida en la barra de direcciones del navegador:
   `http://localhost:8000/tasks/101/edit`
3. Presione Enter para solicitar el recurso.

**Resultado Esperado en Navegador**:
El framework debe interceptar la petición mediante el middleware `CheckAdminRole` antes de llegar al controlador, interrumpiendo el flujo de inmediato y pintando la pantalla de error nativa con código **HTTP 403 Forbidden** y el texto:
`Acceso denegado: Se requieren privilegios de administrador para realizar esta operación.`

---

### Escenario 3: Pruebas de Integración Automatizadas con Bruno

Utilice el cliente HTTP **Bruno** para asegurar que los endpoints devuelven los códigos de estado adecuados según la cabecera de sesión y que no se expone información sensible.

1. Abra la herramienta **Bruno v1.38.0**.
2. Cree una colección temporal con el nombre: `TaskFlow-Auth-Validation`.
3. Registre una solicitud HTTP de tipo `GET` con la dirección de prueba:
   `http://localhost:8000/tasks/101/edit`
4. Asegúrese de que no existan credenciales o cabeceras de autorización en la petición (Simulando un cliente anónimo externo).
5. Envíe la petición.

**Resultado Esperado en Bruno**:
Bruno debe capturar una respuesta con código **302 Found** (Redirección nativa de Laravel hacia la vista de login `/login`), garantizando que la ruta está blindada contra accesos de usuarios no autenticados en el sistema.

---

## Solución de Problemas

A continuación, se describen los dos incidentes más comunes que ocurren durante la implementación de esta arquitectura, sus causas raíz y los procedimientos técnicos para solucionarlos.

### Problema 1: Error "Class 'CheckAdminRole' not found" o Falla al Compilar bootstrap/app.php
- **Sintoma Visual**: Al intentar acceder a cualquier ruta del servidor web, la pantalla devuelve un error crítico de compilación de PHP (Symphony Debugger) que indica que el middleware de control de accesos `CheckAdminRole` no se encuentra disponible o no puede ser cargado por el enrutador.
- **Causa Raíz**: El espacio de nombres (*namespace*) dentro del archivo `app/Http/Middleware/CheckAdminRole.php` no coincide exactamente con la declaración de la ruta importada en el archivo `bootstrap/app.php`, o se omitió el guardado de alguno de los archivos.
- **Solución del Problema**:
  1. Abra el archivo `app/Http/Middleware/CheckAdminRole.php` y valide que la cabecera declare exactamente el namespace:
     ```php
     namespace App\Http\Middleware;
     ```
  2. En `bootstrap/app.php`, verifique que el mapeo use el namespace cualificado completo de la clase, utilizando el operador de resolución de alcance `::class` para evitar interpretaciones erróneas de tipo string:
     ```php
     $middleware->alias([
         'role.admin' => \App\Http\Middleware\CheckAdminRole::class,
     ]);
     ```
  3. Ejecute una limpieza interna de la caché de optimización de rutas del framework en su terminal:
     ```bash
     php artisan route:clear
     ```

### Problema 2: El usuario regular puede seguir accediendo a las rutas de edición del Administrador
- **Sintoma Visual**: Al iniciar sesión con un usuario que tiene registrado el rol `user` e introducir de forma manual la dirección `/tasks/101/edit`, se despliega la pantalla o el mensaje de edición de tareas sin gatillar el código 403.
- **Causa Raíz**: El middleware se construyó de manera adecuada, pero no se asoció de forma correcta a las rutas críticas dentro del archivo `routes/web.php`, provocando que las peticiones evadan el filtro de seguridad de roles.
- **Solución del Problema**:
  1. Abra el archivo `routes/web.php`.
  2. Verifique que las rutas administrativas estén rodeadas por el método de agrupación de middleware adecuado. Asegúrese de que el alias del middleware coincida exactamente en sintaxis:
     ```php
     Route::middleware('role.admin')->group(function () {
         Route::get('/tasks/{id}/edit', [TaskController::class, 'edit'])->name('tasks.edit');
         Route::delete('/tasks/{id}', [TaskController::class, 'destroy'])->name('tasks.destroy');
     });
     ```
  3. Ejecute el comando de listado en su terminal y verifique visualmente que la columna de middleware muestre la etiqueta `role.admin`:
     ```bash
     php artisan route:list | grep tasks
     ```

---

## Limpieza

Para restaurar el entorno y asegurar un flujo de trabajo limpio para la próxima práctica de laboratorio, proceda de la siguiente manera:

1. Detenga el servidor de desarrollo local de Laravel y el empaquetador Vite presionando la combinación de teclas `CTRL + C` en cada una de las consolas activas.
2. Revise el estado del control de versiones para asegurarse de no incluir archivos basura o directorios de prueba transitorios:
   ```bash
   git status
   ```
3. Agregue todos los cambios del laboratorio al sistema de control de versiones y confirme mediante un commit estructurado:
   ```bash
   git add .
   git commit -m "feat: implementar autenticacion breeze, migracion de roles, seeders y middleware de seguridad"
   ```
4. Suba la rama de desarrollo al repositorio remoto corporativo (sustituya `origin` si su servidor utiliza otro nombre):
   ```bash
   git push origin lab-03-autenticacion-roles
   ```
5. Si no requiere continuar utilizando el esquema actual, puede realizar un vaciado de las sesiones locales y limpiar la caché de las vistas:
   ```bash
   php artisan cache:clear
   ```

---

## Resumen

### Puntos Clave Cubiertos
- **Laravel Breeze** permite acelerar drásticamente el desarrollo seguro de flujos de registro, autenticación de sesiones e inicio seguro empleando el estándar de la industria sobre Blade y Tailwind CSS.
- La extensión del esquema de base de datos a través de campos como `role` con un valor seguro por defecto (`user`) constituye la base del control de accesos basado en roles (*RBAC*).
- Los **Middlewares de Laravel** actúan como filtros intermedios en el flujo HTTP, permitiendo denegar peticiones anómalas mediante códigos de estado web estándar antes de consumir recursos de procesamiento de lógica del negocio o del backend de base de datos.
- El archivo `bootstrap/app.php` representa el nuevo estándar de configuración unificado de middleware y servicios para el framework desde la versión 11.x, sustituyendo los archivos tradicionales de `Kernel.php`.

### Recursos Adicionales e Investigación Complementaria
- [Documentación Oficial de Laravel Middleware](https://laravel.com/docs/11.x/middleware)
- [Control de Accesos Basado en Roles (RBAC) - Conceptos de Seguridad Web OWASP](https://owasp.org/www-community/Access_Control)
- [Configuración de Estilos y Compilación Frontend con Laravel y Vite](https://laravel.com/docs/11.x/vite)
