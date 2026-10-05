# 8 Práctica: Desarrollo CRUD con Laravel 13.x, PHP 8.5.x y MySQL 8.4.x

## Metadatos

| Atributo | Detalle |
| :--- | :--- |
| **Duración** | 180 minutos |
| **Complejidad** | Media |
| **Nivel de Bloom** | Crear (Nivel 6) |

## Descripción General

En este laboratorio práctico, configurarás un entorno de desarrollo web moderno utilizando el framework backend Laravel, conectándolo con el servidor de bases de datos relacionales MySQL 8.4.0 LTS. Diseñarás e implementarás una estructura de base de datos robusta para gestionar un flujo de tareas utilizando migraciones de Laravel. Además, construirás un modelo Eloquent estructurado con reglas estrictas de validación de negocio y desarrollarás un módulo CRUD completo (Crear, Leer, Actualizar, Eliminar) basado en vistas Blade tradicionales y estilizado con Tailwind CSS 4.0.0. Este módulo incorporará paginación optimizada desde el servidor y un motor dinámico de ordenamiento de columnas para garantizar una experiencia fluida y escalable.

## Objetivos de Aprendizaje

Al finalizar este laboratorio, serás capaz de:
- [ ] **Configurar y validar** la conexión física entre el framework Laravel y un motor de base de datos MySQL 8.4.0 LTS empleando variables de entorno seguras.
- [ ] **Orquestar** la estructura lógica de la base de datos mediante migraciones declarativas y robustas para una tabla de tareas (`tasks`).
- [ ] **Implementar** un modelo de datos Eloquent con protecciones contra asignación masiva, conversión estricta de tipos de datos (*casting*) y lógica de negocio básica.
- [ ] **Construir** un controlador CRUD con validaciones en el lado del servidor mediante *Form Requests* y consultas optimizadas para paginación y ordenamiento dinámico.
- [ ] **Validar** la robustez de la API y el backend mediante la ejecución de pruebas automatizadas con Pest, incluyendo casos de prueba adversos y de inyección de código.

## Prerrequisitos

Para completar con éxito este laboratorio, requieres:
1. **Conocimientos teóricos y prácticos previos:**
   - Dominio básico de la sintaxis y comandos SQL estándar (DDL y DML).
   - Comprensión del patrón de arquitectura Modelo-Vista-Controlador (MVC).
   - Familiaridad con el uso de la interfaz de línea de comandos (CLI/Terminal).
   - Finalización del laboratorio previo de inicialización del proyecto (Lab 01-00-01).

2. **Acceso e infraestructura:**
   - Una terminal con acceso interactivo a un entorno local de desarrollo basado en Linux, macOS (x86_64 o ARM64) o WSL2 sobre Windows 11.
   - Acceso administrativo a una instancia de base de datos MySQL Server (versión 8.4.x) para aprovisionar esquemas y asignar privilegios.

## Entorno de Laboratorio

El laboratorio se ejecutará de forma exclusiva bajo las siguientes especificaciones técnicas de hardware y software de código abierto:

### Especificaciones de Hardware
- **Procesador (CPU):** Arquitectura x86_64 o ARM64 (mínimo de 4 núcleos físicos).
- **Memoria RAM:** Mínimo 8 GB (Recomendado 16 GB).
- **Almacenamiento:** Unidad de Estado Sólido (SSD) con un mínimo de 10 GB de espacio libre dedicado.

### Versiones de Software y Licencias

| Herramienta / Tecnología | Versión de Referencia | Licencia | Enlace de Descarga / Fuente Oficial |
| :--- | :--- | :--- | :--- |
| **PHP (CLI / FPM)** | 8.5.0 | PHP License | [PHP Downloads](https://www.php.net/downloads.php) |
| **Composer (Gestor de Dependencias)** | 2.10.3 | MIT | [Composer Download](https://getcomposer.org/download/) |
| **Laravel Framework** | 13.x [VERSIÓN POR VALIDAR] / 11.8.0 | MIT | [Laravel GitHub](https://github.com/laravel/framework) |
| **MySQL Community Server** | 8.4.0 LTS (ARM64/x86_64) | GPLv2 | [MySQL Community Downloads](https://dev.mysql.com/downloads/mysql/) |
| **Node.js** | 24.21.0 (LTS) | MIT / Node | [Node.js Downloads](https://nodejs.org/en/download/) |
| **Tailwind CSS** | 4.0.0-alpha.15 | MIT | [Tailwind CSS installation](https://tailwindcss.com) |
| **Pest (Testing Framework)** | 2.34.0 | MIT | [Pest Official Docs](https://pestphp.com/) |

### Variables de Entorno del Laboratorio
El directorio de trabajo principal asignado será: `~/labs/taskflow-app`.
Las credenciales de acceso preestablecidas para la base de datos de desarrollo son:
- **Database Name:** `taskflow_db`
- **Database User:** `taskflow_user`
- **Database Password:** `TaskFlowSecure2026!`
- **Database Port:** `3306`

---

## Instrucciones Paso a Paso

### Paso 1: Configurar el entorno de base de datos MySQL (.env)

**Objetivo:** Crear el esquema lógico en MySQL y configurar las variables de entorno de la aplicación Laravel para interactuar con la base de datos utilizando el controlador PDO seguro.

1. Abre tu terminal de comandos local e ingresa a tu consola interactiva de MySQL como usuario administrador (`root`):
   ```bash
   mysql -u root -p
   ```
   *(Ingresa la contraseña de administrador cuando se te solicite).*

2. Ejecuta las siguientes sentencias SQL estructuradas para crear la base de datos del proyecto, asignar un nuevo usuario con permisos explícitos y asegurar la conexión:
   ```sql
   CREATE DATABASE taskflow_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

   CREATE USER 'taskflow_user'@'127.0.0.1' IDENTIFIED BY 'TaskFlowSecure2026!';

   GRANT ALL PRIVILEGES ON taskflow_db.* TO 'taskflow_user'@'127.0.0.1';

   FLUSH PRIVILEGES;
   EXIT;
   ```

3. Navega al directorio de trabajo asignado del proyecto Laravel:
   ```bash
   cd ~/labs/taskflow-app
   ```

4. Abre el archivo `.env` ubicado en la raíz del proyecto y localiza las propiedades con prefijo `DB_`. Configura sus valores con las credenciales creadas:
   ```env
   DB_CONNECTION=mysql
   DB_HOST=127.0.0.1
   DB_PORT=3306
   DB_DATABASE=taskflow_db
   DB_USERNAME=taskflow_user
   DB_PASSWORD="TaskFlowSecure2026!"
   ```
   *(Nota: Asegúrate de envolver la contraseña entre comillas dobles si contiene caracteres especiales como `!`).*

5. Para cerciorarte de que la configuración se lea en tiempo de ejecución sin interferencias de configuraciones previas en caché, limpia el almacenamiento de configuración interna:
   ```bash
   php artisan config:clear
   ```

6. Inicia una sesión rápida de interacción interactiva de Laravel Tinker para validar el enlace físico con la base de datos:
   ```bash
   php artisan tinker
   ```
   Dentro de la consola interactiva Tinker, ejecuta la siguiente instrucción:
   ```php
   DB::connection()->getPdo();
   ```
   Sal de la consola escribiendo `exit`.

*Resultado esperado:* La salida en Tinker debe devolver una instancia estructurada del objeto PDO con detalles de la conexión establecida con el servidor MySQL. Si devuelve un error, comprueba que el servicio MySQL se encuentre activo e iniciado en el puerto 3306.

*Verificación:* Ejecuta la herramienta de diagnóstico de Laravel en consola para asegurar el estado general de la base de datos:
```bash
php artisan db:show
```
Este comando debe renderizar el tipo de motor (`MySQL`), la versión (`8.4.0`) y el conteo actual de tablas existentes en el esquema `taskflow_db` (vacío temporalmente).

---

### Paso 2: Crear y ejecutar la migración de la tabla 'tasks'

**Objetivo:** Modelar de forma declarativa e implementar la estructura relacional de la tabla `tasks` en el motor MySQL utilizando el sistema de migraciones nativo de Laravel.

1. Genera una nueva migración estructurada desde la terminal de Artisan:
   ```bash
   php artisan make:migration create_tasks_table --table=tasks
   ```

2. Abre la migración recién generada ubicada en el directorio `database/migrations/*_create_tasks_table.php` utilizando tu editor de texto preferido. Reemplaza el contenido por la siguiente estructura:
   ```php
   <?php

   use Illuminate\Database\Migrations\Migration;
   use Illuminate\Database\Schema\Blueprint;
   use Illuminate\Support\Facades\Schema;

   return new class extends Migration
   {
       /**
        * Run the migrations.
        */
       public function up(): void
       {
           Schema::create('tasks', function (Blueprint $table) {
               $table->id();
               $table->string('title', 150);
               $table->text('description')->nullable();
               $table->enum('status', ['pending', 'in_progress', 'completed'])->default('pending');
               $table->date('due_date')->nullable();
               $table->timestamps();
           });
       }

       /**
        * Reverse the migrations.
        */
       public function down(): void
       {
           Schema::dropIfExists('tasks');
       }
   };
   ```

3. Guarda los cambios en el archivo y ejecuta el proceso de migración de la base de datos en la consola:
   ```bash
   php artisan migrate
   ```

*Resultado esperado:* Verás en la salida de consola una lista confirmando que las tablas internas del sistema (sessions, cache, etc.) y la tabla `tasks` han sido creadas con éxito dentro de la base de datos `taskflow_db`.
```text
Running migrations...
  202X_XX_XX_XXXXXX_create_tasks_table ....................................... 12ms DONE
```

*Verificación:* Conéctate directamente por terminal al servidor de base de datos para validar la consistencia estructural de las tablas:
```bash
mysql -u taskflow_user -pTaskFlowSecure2026! -h 127.0.0.1 taskflow_db -e "DESCRIBE tasks;"
```
Debes obtener una representación tabular con los campos definidos (`title` de longitud `varchar(150)`, `status` como un tipo `enum` de tres estados permitidos, etc.).

---

### Paso 3: Desarrollar el Modelo Eloquent 'Task' con validaciones

**Objetivo:** Crear el modelo de persistencia `Task` e implementar los mecanismos de protección contra asignación masiva, tipos de datos tipados en Laravel (Casts) y las reglas semánticas de validación para las peticiones entrantes.

1. Genera el modelo correspondiente a la entidad `Task` mediante Artisan:
   ```bash
   php artisan make:model Task
   ```

2. Abre el archivo de clase generado en `app/Models/Task.php` y escribe el siguiente bloque de código fuente con tipado estricto de propiedades y conversión automática de tipos de datos:
   ```php
   <?php

   namespace App\Models;

   use Illuminate\Database\Eloquent\Factories\HasFactory;
   use Illuminate\Database\Eloquent\Model;

   class Task extends Model
   {
       use HasFactory;

       /**
        * Los atributos que son asignables masivamente de forma segura.
        *
        * @var array<int, string>
        */
       protected $fillable = [
           'title',
           'description',
           'status',
           'due_date',
       ];

       /**
        * Los atributos que deben ser convertidos a tipos de datos específicos.
        *
        * @var array<string, string>
        */
       protected $casts = [
           'due_date' => 'date',
       ];
   }
   ```

3. Crea los archivos de validación de peticiones HTTP (*Form Requests*) dedicados a aislar la lógica de validación de negocio tanto para la creación como para la actualización de tareas:
   ```bash
   php artisan make:request StoreTaskRequest
   php artisan make:request UpdateTaskRequest
   ```

4. Abre el archivo `app/Http/Requests/StoreTaskRequest.php` y configura las reglas de validación en el servidor:
   ```php
   <?php

   namespace App\Http\Requests;

   use Illuminate\Foundation\Http\FormRequest;

   class StoreTaskRequest extends FormRequest
   {
       /**
        * Determina si el usuario está autorizado a realizar esta petición.
        */
       public function authorize(): bool
       {
           return true;
       }

       /**
        * Obtiene las reglas de validación que se aplican a la petición.
        *
        * @return array<string, \Illuminate\Contracts\Validation\ValidationRule|array<mixed>|string>
        */
       public function rules(): array
       {
           return [
               'title' => 'required|string|min:3|max:150',
               'description' => 'nullable|string|max:1000',
               'status' => 'required|in:pending,in_progress,completed',
               'due_date' => 'nullable|date|after_or_equal:today',
           ];
       }

       /**
        * Personaliza los mensajes de error devueltos en la interfaz de usuario.
        */
       public function messages(): array
       {
           return [
               'title.required' => 'El título de la tarea es un campo de carácter obligatorio.',
               'title.min' => 'El título de la tarea debe poseer al menos 3 caracteres.',
               'title.max' => 'El título no puede exceder la cantidad de 150 caracteres.',
               'status.required' => 'Debes definir un estado inicial válido para la tarea.',
               'status.in' => 'El estado provisto no coincide con ninguna opción admisible.',
               'due_date.date' => 'El formato proporcionado para la fecha de vencimiento es inválido.',
               'due_date.after_or_equal' => 'La fecha de vencimiento no puede ser anterior al día de hoy.',
           ];
       }
   }
   ```

5. Modifica ahora `app/Http/Requests/UpdateTaskRequest.php`. En este caso, reutilizaremos la misma lógica rigurosa pero permitiendo actualizaciones parciales:
   ```php
   <?php

   namespace App\Http\Requests;

   use Illuminate\Foundation\Http\FormRequest;

   class UpdateTaskRequest extends FormRequest
   {
       public function authorize(): bool
       {
           return true;
       }

       public function rules(): array
       {
           return [
               'title' => 'required|string|min:3|max:150',
               'description' => 'nullable|string|max:1000',
               'status' => 'required|in:pending,in_progress,completed',
               'due_date' => 'nullable|date', // Se permite mantener una fecha histórica previa en actualizaciones
           ];
       }

       public function messages(): array
       {
           return [
               'title.required' => 'El título es un campo obligatorio.',
               'title.min' => 'El título debe tener al menos 3 caracteres.',
               'status.required' => 'El estado es obligatorio.',
               'status.in' => 'El estado no es válido.',
           ];
       }
   }
   ```

*Resultado esperado:* El modelo y las clases de validación estarán listos para desacoplar el procesamiento de entrada del controlador, garantizando integridad y seguridad contra manipulaciones de datos extraños.

*Verificación:* Compila las clases PHP para descartar errores sintácticos mediante el comando integrado del intérprete:
```bash
php -l app/Models/Task.php app/Http/Requests/StoreTaskRequest.php app/Http/Requests/UpdateTaskRequest.php
```
La salida debe confirmar: `No syntax errors detected in [archivo]`.

---

### Paso 4: Implementar el TaskController con CRUD completo, paginación y ordenamiento dinámico

**Objetivo:** Desarrollar el controlador backend `TaskController` que procese las solicitudes HTTP, aplique ordenamiento dinámico y paginación en base de datos para la visualización, y controle el ciclo de vida completo de los registros de tareas de forma limpia y segura.

1. Genera el controlador con la estructura completa de métodos RESTful vacíos:
   ```bash
   php artisan make:controller TaskController --resource
   ```

2. Abre `app/Http/Controllers/TaskController.php` y reescribe la clase para incorporar el filtrado seguro de columnas de ordenamiento y paginación controlada:
   ```php
   <?php

   namespace App\Http\Controllers;

   use App\Models\Task;
   use App\Http\Requests\StoreTaskRequest;
   use App\Http\Requests\UpdateTaskRequest;
   use Illuminate\Http\Request;

   class TaskController extends Controller
   {
       /**
        * Display a listing of the resource.
        */
       public function index(Request $request)
       {
           // Lista de columnas permitidas para evitar la inyección de parámetros SQL maliciosos
           $allowedSortColumns = ['title', 'status', 'due_date', 'created_at'];
           $sortBy = $request->input('sort_by', 'created_at');
           $order = $request->input('order', 'desc');

           // Validar rigurosamente que los parámetros de ordenamiento sean válidos
           if (!in_array($sortBy, $allowedSortColumns)) {
               $sortBy = 'created_at';
           }

           if (!in_array(strtolower($order), ['asc', 'desc'])) {
               $order = 'desc';
           }

           // Consultar de forma paginada y adjuntar dinámicamente los parámetros actuales en los links de paginación
           $tasks = Task::orderBy($sortBy, $order)
               ->paginate(5)
               ->withQueryString();

           return view('tasks.index', compact('tasks', 'sortBy', 'order'));
       }

       /**
        * Show the form for creating a new resource.
        */
       public function create()
       {
           return view('tasks.create');
       }

       /**
        * Store a newly created resource in storage.
        */
       public function store(StoreTaskRequest $request)
       {
           // El método validated() asegura que solo se procesen datos autorizados
           Task::create($request->validated());

           return redirect()
               ->route('tasks.index')
               ->with('success', '¡La tarea se ha registrado exitosamente en el sistema!');
       }

       /**
        * Display the specified resource.
        */
       public function show(Task $task)
       {
           return view('tasks.show', compact('task'));
       }

       /**
        * Show the form for editing the specified resource.
        */
       public function edit(Task $task)
       {
           return view('tasks.edit', compact('task'));
       }

       /**
        * Update the specified resource in storage.
        */
       public function update(UpdateTaskRequest $request, Task $task)
       {
           $task->update($request->validated());

           return redirect()
               ->route('tasks.index')
               ->with('success', '¡La tarea se ha actualizado de forma exitosa!');
       }

       /**
        * Remove the specified resource from storage.
        */
       public function destroy(Task $task)
       {
           $task->delete();

           return redirect()
               ->route('tasks.index')
               ->with('success', 'La tarea ha sido eliminada del servidor permanentemente.');
       }
   }
   ```

3. Modifica tu archivo de rutas web en `routes/web.php`. Configura una redirección por defecto y asocia todas las rutas CRUD al controlador recién implementado:
   ```php
   <?php

   use App\Http\Controllers\TaskController;
   use Illuminate\Support\Facades\Route;

   Route::get('/', function () {
       return redirect()->route('tasks.index');
   });

   Route::resource('tasks', TaskController::class);
   ```

*Resultado esperado:* El controlador estará listo y las rutas enrutadas correctamente. Cualquier acceso a la raíz de la aplicación redirigirá al módulo de visualización de tareas.

*Verificación:* Ejecuta en terminal el inspector de rutas para validar el mapeo completo de endpoints HTTP y su correcto destino en el controlador:
```bash
php artisan route:list --path=tasks
```
La salida debe confirmar la existencia de al menos 7 endpoints (GET, POST, PUT/PATCH, DELETE) vinculados inequívocamente a los métodos del `TaskController`.

---

### Paso 5: Diseñar las Vistas Blade responsivas con Tailwind CSS 4.0.0

**Objetivo:** Desarrollar una interfaz de usuario limpia, intuitiva y moderna utilizando plantillas Blade, aplicando componentes visuales de Tailwind CSS 4.0.0, protecciones contra ataques CSRF y la prevención de XSS automatizada a través de escapes de salida de Blade (`{{ }}`).

1. Diseña una plantilla base compartida. Crea la carpeta `resources/views/layouts` y dentro de ella el archivo `app.blade.php`:
   ```bash
   mkdir -p resources/views/layouts
   touch resources/views/layouts/app.blade.php
   ```

2. Abre `resources/views/layouts/app.blade.php` e introduce el siguiente HTML estructural responsivo con integración nativa del empaquetador de assets Vite:
   ```html
   <!DOCTYPE html>
   <html lang="es" class="h-full bg-slate-50">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>TaskFlow Application - @yield('title', 'Inicio')</title>
       @vite(['resources/css/app.css', 'resources/js/app.js'])
   </head>
   <body class="h-full font-sans antialiased text-slate-800">
       <div class="min-h-full">
           <!-- Navbar superior -->
           <nav class="bg-indigo-600 border-b border-indigo-700 shadow-sm">
               <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
                   <div class="flex items-center justify-between h-16">
                       <div class="flex items-center">
                           <div class="flex-shrink-0">
                               <span class="text-white font-extrabold text-xl tracking-tight">TaskFlow v4.0</span>
                           </div>
                           <div class="hidden md:block">
                               <div class="ml-10 flex items-baseline space-x-4">
                                   <a href="{{ route('tasks.index') }}" class="text-white hover:bg-indigo-500 px-3 py-2 rounded-md text-sm font-medium transition duration-150">Gestor de Tareas</a>
                               </div>
                           </div>
                       </div>
                   </div>
               </div>
           </nav>

           <main class="py-10">
               <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
                   <!-- Bloques de Mensajes de Notificación de Sesión -->
                   @if (session('success'))
                       <div class="mb-6 p-4 bg-emerald-50 border-l-4 border-emerald-500 text-emerald-800 rounded-r-md flex items-center justify-between shadow-xs">
                           <div class="flex items-center">
                               <svg class="h-5 w-5 text-emerald-500 mr-3" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                   <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
                               </svg>
                               <span class="font-medium">{{ session('success') }}</span>
                           </div>
                       </div>
                   @endif

                   @yield('content')
               </div>
           </main>
       </div>
   </body>
   </html>
   ```

3. Crea el directorio de vistas específicas de la entidad de tareas:
   ```bash
   mkdir -p resources/views/tasks
   ```

4. Diseña el listado principal con ordenamiento y paginación. Crea el archivo `resources/views/tasks/index.blade.php`:
   ```html
   @extends('layouts.app')

   @section('title', 'Listado de Tareas')

   @section('content')
   <div class="sm:flex sm:items-center sm:justify-between mb-8">
       <div>
           <h1 class="text-3xl font-extrabold text-slate-900 tracking-tight">Mis Tareas</h1>
           <p class="mt-2 text-sm text-slate-600">Administra y organiza tus flujos de trabajo en un entorno unificado.</p>
       </div>
       <div class="mt-4 sm:mt-0">
           <a href="{{ route('tasks.create') }}" class="inline-flex items-center justify-center px-4 py-2 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 shadow-sm focus:outline-hidden focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 transition duration-150">
               <svg class="h-5 w-5 mr-2 -ml-1 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                   <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4" />
               </svg>
               Crear Nueva Tarea
           </a>
       </div>
   </div>

   <!-- Panel de Datos -->
   <div class="bg-white shadow-xs rounded-lg overflow-hidden border border-slate-200">
       <div class="overflow-x-auto">
           <table class="min-w-full divide-y divide-slate-200">
               <thead class="bg-slate-50">
                   <tr>
                       <th class="px-6 py-3 text-left text-xs font-semibold text-slate-500 uppercase tracking-wider">
                           <a href="{{ route('tasks.index', ['sort_by' => 'title', 'order' => ($sortBy === 'title' && $order === 'asc') ? 'desc' : 'asc']) }}" class="group inline-flex items-center hover:text-indigo-600">
                               Título de la Tarea
                               <span class="ml-2 flex-none rounded text-slate-400 group-hover:bg-slate-100">
                                   @if($sortBy === 'title')
                                       {!! $order === 'asc' ? '&#9650;' : '&#9660;' !!}
                                   @else
                                       &#8597;
                                   @endif
                               </span>
                           </a>
                       </th>
                       <th class="px-6 py-3 text-left text-xs font-semibold text-slate-500 uppercase tracking-wider">
                           <a href="{{ route('tasks.index', ['sort_by' => 'status', 'order' => ($sortBy === 'status' && $order === 'asc') ? 'desc' : 'asc']) }}" class="group inline-flex items-center hover:text-indigo-600">
                               Estado actual
                               <span class="ml-2 flex-none rounded text-slate-400 group-hover:bg-slate-100">
                                   @if($sortBy === 'status')
                                       {!! $order === 'asc' ? '&#9650;' : '&#9660;' !!}
                                   @else
                                       &#8597;
                                   @endif
                               </span>
                           </a>
                       </th>
                       <th class="px-6 py-3 text-left text-xs font-semibold text-slate-500 uppercase tracking-wider">
                           <a href="{{ route('tasks.index', ['sort_by' => 'due_date', 'order' => ($sortBy === 'due_date' && $order === 'asc') ? 'desc' : 'asc']) }}" class="group inline-flex items-center hover:text-indigo-600">
                               Fecha Vencimiento
                               <span class="ml-2 flex-none rounded text-slate-400 group-hover:bg-slate-100">
                                   @if($sortBy === 'due_date')
                                       {!! $order === 'asc' ? '&#9650;' : '&#9660;' !!}
                                   @else
                                       &#8597;
                                   @endif
                               </span>
                           </a>
                       </th>
                       <th class="px-6 py-3 text-right text-xs font-semibold text-slate-500 uppercase tracking-wider">Acciones</th>
                   </tr>
               </thead>
               <tbody class="bg-white divide-y divide-slate-200">
                   @forelse ($tasks as $task)
                       <tr class="hover:bg-slate-50 transition duration-150">
                           <td class="px-6 py-4 whitespace-nowrap">
                               <div class="text-sm font-semibold text-slate-900">{{ $task->title }}</div>
                               <div class="text-xs text-slate-500 max-w-xs truncate">{{ $task->description ?? 'Sin descripción adjunta.' }}</div>
                           </td>
                           <td class="px-6 py-4 whitespace-nowrap">
                               @switch($task->status)
                                   @case('pending')
                                       <span class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium bg-amber-100 text-amber-800 border border-amber-200">Pendiente</span>
                                       @break
                                   @case('in_progress')
                                       <span class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium bg-indigo-100 text-indigo-800 border border-indigo-200">En Progreso</span>
                                       @break
                                   @case('completed')
                                       <span class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium bg-emerald-100 text-emerald-800 border border-emerald-200">Completada</span>
                                       @break
                               @endswitch
                           </td>
                           <td class="px-6 py-4 whitespace-nowrap text-sm text-slate-600">
                               {{ $task->due_date ? $task->due_date->format('d/m/Y') : 'Sin fecha límite' }}
                           </td>
                           <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium space-x-2">
                               <a href="{{ route('tasks.show', $task) }}" class="inline-flex items-center px-3 py-1.5 border border-slate-300 rounded-md text-xs font-semibold text-slate-700 bg-white hover:bg-slate-50 focus:outline-hidden transition duration-150">Ver</a>
                               <a href="{{ route('tasks.edit', $task) }}" class="inline-flex items-center px-3 py-1.5 border border-transparent rounded-md text-xs font-semibold text-white bg-indigo-600 hover:bg-indigo-500 focus:outline-hidden transition duration-150">Editar</a>
                               <form action="{{ route('tasks.destroy', $task) }}" method="POST" class="inline-block" onsubmit="return confirm('¿Confirma que desea eliminar este registro?');">
                                   @csrf
                                   @method('DELETE')
                                   <button type="submit" class="inline-flex items-center px-3 py-1.5 border border-transparent rounded-md text-xs font-semibold text-white bg-rose-600 hover:bg-rose-500 focus:outline-hidden transition duration-150">Eliminar</button>
                               </form>
                           </td>
                       </tr>
                   @empty
                       <tr>
                           <td colspan="4" class="px-6 py-12 text-center text-sm text-slate-500">
                               <svg class="mx-auto h-12 w-12 text-slate-400 mb-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                   <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" />
                               </svg>
                               <p class="text-base font-semibold text-slate-900 mb-1">No hay tareas registradas</p>
                               <p>Comienza creando tu primera tarea presionando el botón superior.</p>
                           </td>
                       </tr>
                   @endforelse
               </tbody>
           </table>
       </div>

       <!-- Paginación con Tailwind -->
       @if($tasks->hasPages())
           <div class="px-6 py-4 bg-slate-50 border-t border-slate-200">
               {{ $tasks->links() }}
           </div>
       @endif
   </div>
   @endsection
   ```

5. Diseña el formulario de creación. Crea el archivo `resources/views/tasks/create.blade.php`:
   ```html
   @extends('layouts.app')

   @section('title', 'Crear Tarea')

   @section('content')
   <div class="max-w-2xl mx-auto">
       <div class="mb-8">
           <a href="{{ route('tasks.index') }}" class="inline-flex items-center text-sm font-semibold text-indigo-600 hover:text-indigo-500 transition duration-150">
               &larr; Volver al listado principal
           </a>
           <h1 class="text-3xl font-extrabold text-slate-900 mt-4">Crear Nueva Tarea</h1>
       </div>

       <div class="bg-white shadow-xs border border-slate-200 rounded-lg p-6 sm:p-8">
           <form action="{{ route('tasks.store') }}" method="POST">
               @csrf <!-- Protección estricta CSRF requerida por el kernel de seguridad -->
               
               <div class="space-y-6">
                   <div>
                       <label for="title" class="block text-sm font-semibold text-slate-700">Título de la Tarea <span class="text-rose-500">*</span></label>
                       <input type="text" name="title" id="title" value="{{ old('title') }}" class="mt-1 block w-full rounded-md border-slate-300 shadow-xs focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm @error('title') border-rose-300 ring-1 ring-rose-300 @enderror" placeholder="Ej. Rediseñar menú de navegación">
                       @error('title')
                           <p class="mt-2 text-sm text-rose-600">{{ $message }}</p>
                       @enderror
                   </div>

                   <div>
                       <label for="description" class="block text-sm font-semibold text-slate-700">Descripción detallada</label>
                       <textarea name="description" id="description" rows="4" class="mt-1 block w-full rounded-md border-slate-300 shadow-xs focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm @error('description') border-rose-300 ring-1 ring-rose-300 @enderror" placeholder="Opcional: Describe los objetivos y requerimientos de la tarea...">{{ old('description') }}</textarea>
                       @error('description')
                           <p class="mt-2 text-sm text-rose-600">{{ $message }}</p>
                       @enderror
                   </div>

                   <div class="grid grid-cols-1 sm:grid-cols-2 gap-6">
                       <div>
                           <label for="status" class="block text-sm font-semibold text-slate-700">Estado inicial <span class="text-rose-500">*</span></label>
                           <select name="status" id="status" class="mt-1 block w-full rounded-md border-slate-300 shadow-xs focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm">
                               <option value="pending" {{ old('status') === 'pending' ? 'selected' : '' }}>Pendiente</option>
                               <option value="in_progress" {{ old('status') === 'in_progress' ? 'selected' : '' }}>En Progreso</option>
                               <option value="completed" {{ old('status') === 'completed' ? 'selected' : '' }}>Completada</option>
                           </select>
                           @error('status')
                               <p class="mt-2 text-sm text-rose-600">{{ $message }}</p>
                           @enderror
                       </div>

                       <div>
                           <label for="due_date" class="block text-sm font-semibold text-slate-700">Fecha de vencimiento</label>
                           <input type="date" name="due_date" id="due_date" value="{{ old('due_date') }}" class="mt-1 block w-full rounded-md border-slate-300 shadow-xs focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm @error('due_date') border-rose-300 ring-1 ring-rose-300 @enderror">
                           @error('due_date')
                               <p class="mt-2 text-sm text-rose-600">{{ $message }}</p>
                           @enderror
                       </div>
                   </div>
               </div>

               <div class="mt-8 pt-6 border-t border-slate-200 flex items-center justify-end space-x-3">
                   <a href="{{ route('tasks.index') }}" class="px-4 py-2 border border-slate-300 text-sm font-semibold rounded-md text-slate-700 bg-white hover:bg-slate-50 focus:outline-hidden focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 transition duration-150">Cancelar</a>
                   <button type="submit" class="px-4 py-2 border border-transparent text-sm font-semibold rounded-md text-white bg-indigo-600 hover:bg-indigo-700 shadow-xs focus:outline-hidden focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 transition duration-150">Guardar Tarea</button>
               </div>
           </form>
       </div>
   </div>
   @endsection
   ```

6. Diseña el formulario de edición. Crea el archivo `resources/views/tasks/edit.blade.php`:
   ```html
   @extends('layouts.app')

   @section('title', 'Editar Tarea')

   @section('content')
   <div class="max-w-2xl mx-auto">
       <div class="mb-8">
           <a href="{{ route('tasks.index') }}" class="inline-flex items-center text-sm font-semibold text-indigo-600 hover:text-indigo-500 transition duration-150">
               &larr; Volver al listado principal
           </a>
           <h1 class="text-3xl font-extrabold text-slate-900 mt-4">Editar Tarea: {{ $task->title }}</h1>
       </div>

       <div class="bg-white shadow-xs border border-slate-200 rounded-lg p-6 sm:p-8">
           <form action="{{ route('tasks.update', $task) }}" method="POST">
               @csrf
               @method('PUT') <!-- Directiva Blade requerida para mapear un método PUT en un formulario HTML estándar -->
               
               <div class="space-y-6">
                   <div>
                       <label for="title" class="block text-sm font-semibold text-slate-700">Título de la Tarea <span class="text-rose-500">*</span></label>
                       <input type="text" name="title" id="title" value="{{ old('title', $task->title) }}" class="mt-1 block w-full rounded-md border-slate-300 shadow-xs focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm @error('title') border-rose-300 ring-1 ring-rose-300 @enderror">
                       @error('title')
                           <p class="mt-2 text-sm text-rose-600">{{ $message }}</p>
                       @enderror
                   </div>

                   <div>
                       <label for="description" class="block text-sm font-semibold text-slate-700">Descripción detallada</label>
                       <textarea name="description" id="description" rows="4" class="mt-1 block w-full rounded-md border-slate-300 shadow-xs focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm @error('description') border-rose-300 ring-1 ring-rose-300 @enderror">{{ old('description', $task->description) }}</textarea>
                       @error('description')
                           <p class="mt-2 text-sm text-rose-600">{{ $message }}</p>
                       @enderror
                   </div>

                   <div class="grid grid-cols-1 sm:grid-cols-2 gap-6">
                       <div>
                           <label for="status" class="block text-sm font-semibold text-slate-700">Estado actual <span class="text-rose-500">*</span></label>
                           <select name="status" id="status" class="mt-1 block w-full rounded-md border-slate-300 shadow-xs focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm">
                               <option value="pending" {{ old('status', $task->status) === 'pending' ? 'selected' : '' }}>Pendiente</option>
                               <option value="in_progress" {{ old('status', $task->status) === 'in_progress' ? 'selected' : '' }}>En Progreso</option>
                               <option value="completed" {{ old('status', $task->status) === 'completed' ? 'selected' : '' }}>Completada</option>
                           </select>
                           @error('status')
                               <p class="mt-2 text-sm text-rose-600">{{ $message }}</p>
                           @enderror
                       </div>

                       <div>
                           <label for="due_date" class="block text-sm font-semibold text-slate-700">Fecha de vencimiento</label>
                           <input type="date" name="due_date" id="due_date" value="{{ old('due_date', $task->due_date ? $task->due_date->format('Y-m-d') : '') }}" class="mt-1 block w-full rounded-md border-slate-300 shadow-xs focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm @error('due_date') border-rose-300 ring-1 ring-rose-300 @enderror">
                           @error('due_date')
                               <p class="mt-2 text-sm text-rose-600">{{ $message }}</p>
                           @enderror
                       </div>
                   </div>
               </div>

               <div class="mt-8 pt-6 border-t border-slate-200 flex items-center justify-end space-x-3">
                   <a href="{{ route('tasks.index') }}" class="px-4 py-2 border border-slate-300 text-sm font-semibold rounded-md text-slate-700 bg-white hover:bg-slate-50 focus:outline-hidden transition duration-150">Cancelar</a>
                   <button type="submit" class="px-4 py-2 border border-transparent text-sm font-semibold rounded-md text-white bg-indigo-600 hover:bg-indigo-700 shadow-xs focus:outline-hidden transition duration-150">Actualizar Tarea</button>
               </div>
           </form>
       </div>
   </div>
   @endsection
   ```

7. Diseña el detalle individual. Crea el archivo `resources/views/tasks/show.blade.php`:
   ```html
   @extends('layouts.app')

   @section('title', 'Detalle de Tarea')

   @section('content')
   <div class="max-w-2xl mx-auto">
       <div class="mb-8">
           <a href="{{ route('tasks.index') }}" class="inline-flex items-center text-sm font-semibold text-indigo-600 hover:text-indigo-500 transition duration-150">
               &larr; Volver al listado principal
           </a>
           <h1 class="text-3xl font-extrabold text-slate-900 mt-4">Detalle de la Tarea</h1>
       </div>

       <div class="bg-white shadow-xs border border-slate-200 rounded-lg overflow-hidden">
           <div class="px-6 py-5 border-b border-slate-200 bg-slate-50 sm:px-8 flex items-center justify-between">
               <span class="text-sm font-semibold text-slate-500 uppercase tracking-wider">Identificador: #{{ $task->id }}</span>
               
               @switch($task->status)
                   @case('pending')
                       <span class="inline-flex items-center px-3 py-1 rounded-full text-xs font-semibold bg-amber-100 text-amber-800 border border-amber-200">Pendiente</span>
                       @break
                   @case('in_progress')
                       <span class="inline-flex items-center px-3 py-1 rounded-full text-xs font-semibold bg-indigo-100 text-indigo-800 border border-indigo-200">En Progreso</span>
                       @break
                   @case('completed')
                       <span class="inline-flex items-center px-3 py-1 rounded-full text-xs font-semibold bg-emerald-100 text-emerald-800 border border-emerald-200">Completada</span>
                       @break
               @endswitch
           </div>

           <div class="p-6 sm:p-8 space-y-6">
               <div>
                   <h3 class="text-xs font-semibold text-slate-400 uppercase tracking-wider">Título de la Tarea</h3>
                   <p class="mt-2 text-xl font-bold text-slate-900">{{ $task->title }}</p>
               </div>

               <div>
                   <h3 class="text-xs font-semibold text-slate-400 uppercase tracking-wider">Descripción detallada</h3>
                   <p class="mt-2 text-base text-slate-700 whitespace-pre-line leading-relaxed">
                       {{ $task->description ?? 'No se aportaron detalles de descripción para esta tarea.' }}
                   </p>
               </div>

               <div class="grid grid-cols-1 sm:grid-cols-2 gap-6 pt-4 border-t border-slate-100">
                   <div>
                       <h3 class="text-xs font-semibold text-slate-400 uppercase tracking-wider">Fecha límite de entrega</h3>
                       <p class="mt-2 text-sm font-medium text-slate-900">
                           {{ $task->due_date ? $task->due_date->format('l, d \d\e F \d\e Y') : 'Sin fecha límite establecida.' }}
                       </p>
                   </div>
                   <div>
                       <h3 class="text-xs font-semibold text-slate-400 uppercase tracking-wider">Última actualización</h3>
                       <p class="mt-2 text-sm font-medium text-slate-900">
                           {{ $task->updated_at->format('d/m/Y H:i') }} hrs.
                       </p>
                   </div>
               </div>
           </div>

           <div class="px-6 py-4 bg-slate-50 border-t border-slate-200 sm:px-8 flex justify-end space-x-3">
               <a href="{{ route('tasks.edit', $task) }}" class="inline-flex items-center px-4 py-2 border border-transparent text-sm font-semibold rounded-md text-white bg-indigo-600 hover:bg-indigo-700 transition duration-150 shadow-xs">Editar Tarea</a>
           </div>
       </div>
   </div>
   @endsection
   ```

*Resultado esperado:* La UI estará implementada y estilizada. Conectará de forma inmediata las rutas con la representación del servidor.

*Verificación:* Compila las clases CSS en tiempo de ejecución de desarrollo levantando el servidor de Vite:
```bash
npm run build
```
El bundler debe completar exitosamente y colocar los ficheros finales en el directorio público de Laravel.

---

### Paso 6: Crear pruebas automatizadas con Pest PHP para validar el flujo completo

**Objetivo:** Desarrollar casos de prueba funcionales e integrados empleando el motor Pest para verificar que los mecanismos de creación de datos, filtros de ordenamiento y validaciones de inyección SQL se encuentren en perfecto funcionamiento.

1. Inicializa el conjunto de utilidades e integración de Pest en el proyecto:
   ```bash
   composer require pestphp/pest-plugin-laravel --dev
   php artisan pest:install
   ```
   *(Elige las opciones por defecto que ofrece el instalador interactivo).*

2. Asegura que el archivo de configuración de pruebas de Pest `tests/Pest.php` tenga activa la recarga y refresco de base de datos para no contaminar la base de datos MySQL local utilizando laTrait de RefreshDatabase:
   ```php
   <?php

   uses(
       Tests\TestCase::class,
       Illuminate\Foundation\Testing\RefreshDatabase::class,
   )->in('Feature');
   ```

3. Crea el archivo de especificación de pruebas para la gestión de tareas:
   ```bash
   mkdir -p tests/Feature
   touch tests/Feature/TaskCrudTest.php
   ```

4. Abre `tests/Feature/TaskCrudTest.php` y escribe los siguientes escenarios de control para validar el correcto comportamiento del CRUD:
   ```php
   <?php

   use App\Models\Task;

   test('la página de inicio redirige al listado de tareas', function () {
       $response = $this->get('/');

       $response->assertRedirect(route('tasks.index'));
   });

   test('se pueden listar las tareas de forma ordenada de manera descendente por defecto', function () {
       $taskAntigua = Task::create([
           'title' => 'Tarea Antigua',
           'status' => 'pending',
           'created_at' => now()->subDays(5)
       ]);

       $taskNueva = Task::create([
           'title' => 'Tarea Reciente',
           'status' => 'in_progress',
           'created_at' => now()
       ]);

       $response = $this->get(route('tasks.index'));

       $response->assertStatus(200);
       $response->assertSeeInOrder(['Tarea Reciente', 'Tarea Antigua']);
   });

   test('no se puede registrar una tarea con título menor a tres caracteres', function () {
       $response = $this->post(route('tasks.store'), [
           'title' => 'Ir',
           'status' => 'pending'
       ]);

       $response->assertSessionHasErrors(['title']);
       $this->assertDatabaseEmpty('tasks');
   });

   test('no se puede registrar una tarea con fecha de vencimiento anterior al día de hoy', function () {
       $response = $this->post(route('tasks.store'), [
           'title' => 'Presentar examen final',
           'status' => 'pending',
           'due_date' => now()->subDay()->format('Y-m-d')
       ]);

       $response->assertSessionHasErrors(['due_date']);
       $this->assertDatabaseEmpty('tasks');
   });

   test('se puede eliminar una tarea registrada con éxito', function () {
       $task = Task::create([
           'title' => 'Tarea para ser borrada',
           'status' => 'completed'
       ]);

       $response = $this->delete(route('tasks.destroy', $task));

       $response->assertRedirect(route('tasks.index'));
       $this->assertDatabaseMissing('tasks', [
           'id' => $task->id
       ]);
   });
   ```

5. Ejecuta el suite completo de pruebas desde la terminal del proyecto:
   ```bash
   ./vendor/bin/pest
   ```

*Resultado esperado:* La salida de Pest debe confirmar el paso exitoso de la totalidad de las aserciones declaradas en la suite de integración.
```text
  PASS  Tests\Feature\TaskCrudTest
  ✓ la página de inicio redirige al listado de tareas
  ✓ se pueden listar las tareas de forma ordenada de manera descendente por defecto
  ✓ no se puede registrar una tarea con título menor a tres caracteres
  ✓ no se puede registrar una tarea con fecha de vencimiento anterior al día de hoy
  ✓ se puede eliminar una tarea registrada con éxito

  Tests:    5 passed (5 assertions)
  Duration: 0.28s
```

*Verificación:* Modifica deliberadamente una de las reglas de validación de caracteres del título en `StoreTaskRequest.php` (por ejemplo, cambia `'min:3'` a `'min:1'`) y vuelve a ejecutar Pest para confirmar que la suite detecta de inmediato el cambio de comportamiento y falla bajo las condiciones correctas. Revierte el cambio después de la prueba.

---

## Validación y Pruebas

Para asegurar la robustez de la lógica construida en este laboratorio, llevaremos a cabo una ronda de pruebas de aseguramiento de calidad utilizando escenarios de carácter adverso en la línea de comandos y en el navegador.

### Escenario 1: Pruebas de Resistencia contra Inyección de Parámetros en Ordenamiento Dinámico
El motor de ordenamiento desarrollado en el `TaskController` mapea los query parameters `sort_by` y `order` directamente sobre la consulta SQL. Si el controlador no estuviera protegido por una lista blanca de valores permitidos (*whitelisting*), un atacante podría inyectar consultas no deseadas.

Para simular este comportamiento adversario de inyección y validar la robustez, ejecuta la siguiente petición con un parámetro malicioso en el query string de ordenamiento:
```bash
curl -I "http://localhost:8000/tasks?sort_by=id;DROP+TABLE+tasks;--&order=desc"
```
*Verificación:* La aplicación no debe fallar con un error del motor PDO. El controlador debe ignorar la cadena inyectada `id;DROP TABLE tasks;--` y forzar el comportamiento por defecto (`created_at`). 
Puedes comprobarlo visualizando los logs del sistema para asegurar que no ocurrieron errores de consulta SQL:
```bash
tail -n 20 storage/logs/laravel.log
```

### Escenario 2: Prueba Adversaria de Carga sin Datos (Payload Vacío)
Envía una petición POST directa simulando una omisión completa de campos para corroborar el correcto control de validación:
```bash
curl -X POST http://localhost:8000/tasks \
     -H "Content-Type: application/json" \
     -H "Accept: application/json" \
     -d '{}'
```
*Resultado esperado:* El servidor debe responder de forma inmediata con un código de estado de respuesta HTTP `422 Unprocessable Entity` y detallar en un formato JSON estructurado los campos obligatorios ausentes en la petición.

---

## Solución de Problemas

Durante el transcurso del desarrollo de este laboratorio práctico, es posible que te enfrentes a alguno de los siguientes contratiempos comunes de integración de infraestructura:

### Problema 1: PDOException - Connection refused / Access Denied
* **Síntomas:** Al ejecutar `php artisan db:show` o intentar ingresar a Laravel Tinker se arroja por consola el siguiente error de PHP: `SQLSTATE[HY000] [2002] Connection refused` o `Access denied for user 'taskflow_user'@'localhost'`.
* **Causa raíz:** Este fallo se origina por discrepancias entre el puerto de red/host definidos en el archivo `.env` y el socket donde realmente se encuentra escuchando el servicio local de MySQL. También puede deberse a que la contraseña provista posee caracteres especiales de sistema y no fue encerrada entre comillas dobles, rompiendo la lectura por parte del parseador dotenv.
* **Solución correctiva:**
  1. Verifica que el servidor de MySQL se encuentre iniciado activamente:
     ```bash
     sudo systemctl status mysql     # En distribuciones Linux Debian/Ubuntu
     # O bien en macOS Homebrew:
     brew services list
     ```
  2. Confirma la existencia del usuario y sus credenciales abriendo la consola nativa de MySQL:
     ```sql
     SELECT user, host FROM mysql.user WHERE user = 'taskflow_user';
     ```
  3. Asegura que el archivo `.env` defina de forma correcta la dirección de loopback `127.0.0.1` en vez de `localhost` para obligar al controlador de PHP a conectarse mediante protocolo TCP en lugar de usar sockets Unix Unix `/var/run/mysqld/mysqld.sock`.

### Problema 2: Error "The POST method is not supported for this route. Supported methods: GET, HEAD."
* **Síntomas:** Al intentar guardar el formulario de creación o actualización de tareas en la interfaz de usuario, Laravel arroja una pantalla de error crítica con estado HTTP 405 Method Not Allowed.
* **Causa raíz:** En el formulario de creación, este error ocurre típicamente por omitir el token `@csrf` o por no enviar el payload a la ruta de acción correcta (`route('tasks.store')`). En el formulario de edición, ocurre porque los formularios HTML estándar no admiten de manera nativa los verbos HTTP `PUT` o `PATCH`.
* **Solución correctiva:**
  1. Abre el archivo de vista `resources/views/tasks/edit.blade.php`.
  2. Verifica que inmediatamente debajo de la etiqueta `<form>` se encuentren declaradas las siguientes dos directivas Blade obligatorias:
     ```html
     <form action="{{ route('tasks.update', $task) }}" method="POST">
         @csrf
         @method('PUT')
     ```
  3. Ejecuta una recarga de la caché de rutas del framework para garantizar que los cambios surtan efecto en el despachador central de Laravel:
     ```bash
     php artisan route:clear
     ```

---

## Limpieza

Una vez validados todos los escenarios del laboratorio y completada la entrega de manera exitosa, es fundamental restablecer y sanear el estado de la estación de trabajo para evitar conflictos posteriores:

1. Detén cualquier servidor local de desarrollo de Laravel o Vite que se encuentre actualmente en ejecución en tu terminal presionando la combinación de teclas `Ctrl + C`.

2. En el directorio raíz de la aplicación, revierte o descarta cambios de desarrollo temporales y consolida tus avances en la rama correspondiente de Git alineada al ID de este laboratorio:
   ```bash
   git add .
   git commit -m "feat: implementa de forma completa el flujo CRUD de tareas con validaciones, paginación y ordenamiento dinámico"
   git checkout -b lab-02-crud
   ```

3. Limpia los archivos temporales autogenerados de caché de configuración, vistas, optimizaciones del sistema y dependencias de testing:
   ```bash
   php artisan optimize:clear
   ```

---

## Resumen

En este laboratorio práctico has diseñado e implementado una solución completa para persistir y administrar datos utilizando Laravel 13.x y MySQL 8.4.0 LTS. A lo largo del ejercicio completaste los siguientes hitos técnicos:

* **Conexión de Entornos:** Estableciste una comunicación bidireccional segura aislando credenciales mediante variables de entorno en el archivo `.env` y confirmaste su correcto funcionamiento mediante la consola interactiva Tinker y el uso del conector PDO.
* **Migraciones Relacionales:** Declaraste e implementaste la tabla `tasks` en MySQL asegurando la integridad estructural de los tipos de datos mediante la herramienta de migraciones integrada.
* **Modelo y Seguridad:** Creaste el modelo Eloquent aplicando protecciones nativas contra ataques de asignación masiva, implementaste conversiones estrictas de fechas y aislaste la lógica de validación semántica en clases *Form Request*.
* **Controlador RESTful & Blade v4:** Construiste la interfaz de usuario responsiva integrada con Tailwind CSS 4.0.0 utilizando plantillas Blade, soportando flujos CRUD completos, paginación de servidores y un motor dinámico contra inyecciones SQL para el ordenamiento de columnas.
* **Aseguramiento de Calidad:** Verificaste la integridad de tu código implementando pruebas automatizadas con Pest PHP para controlar tanto los flujos de éxito como los límites y casos adversos de seguridad.

### Recursos Adicionales e Información Oficial
* [Documentación oficial de Laravel sobre Migraciones de Base de Datos](https://laravel.com/docs/11.x/migrations)
* [Guía de referencia de Laravel Eloquent ORM](https://laravel.com/docs/11.x/eloquent)
* [Directiva de diseño y manual de referencia rápido de Tailwind CSS v4](https://tailwindcss.com/docs)
* [Pest PHP - Primeros Pasos y Aserciones de Base de Datos](https://pestphp.com/docs/assertions)
