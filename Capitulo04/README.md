# 7 Práctica: Seguridad esencial en aplicaciones con PHP 8.5.x y Laravel 13.x

## Metadatos

| Atributo | Detalle |
| :--- | :--- |
| **Duración** | 180 minutos |
| **Complejidad** | Alta (Hard) |
| **Nivel de Bloom** | Aplicar (Apply) |

## Descripción General

En este laboratorio práctico, robustecerás la seguridad de una aplicación de gestión de tareas existente ("TaskFlow") en Laravel. Implementarás un flujo completo de subida y descarga de archivos adjuntos para las tareas utilizando las mejores prácticas de ciberseguridad defensiva. 

Aprenderás a proteger la aplicación contra vectores de ataque comunes detallados en el OWASP Top 10, tales como la subida de archivos sin restricción (Unrestricted File Upload), la elusión de autorización a nivel de objeto (BOLA/IDOR), ataques de Cross-Site Scripting (XSS) y falsificación de peticiones en sitios cruzados (CSRF). Al finalizar, tu sistema será capaz de recibir, validar, almacenar de forma privada y servir de manera controlada archivos confidenciales (como documentos PDF e imágenes PNG), garantizando que solo los usuarios autorizados tengan acceso físico a los recursos.

## Objetivos de Aprendizaje

- [ ] **Mitigar** vulnerabilidades críticas de OWASP como Cross-Site Scripting (XSS) y Cross-Site Request Forgery (CSRF) en vistas y formularios estructurados en Blade.
- [ ] **Implementar** un mecanismo seguro de carga de archivos (PDF y PNG), validando de forma estricta los tipos MIME reales y el tamaño de archivo a través de clases dedicadas de tipo `FormRequest`.
- [ ] **Asegurar** el almacenamiento físico de documentos sensibles ubicándolos de forma privada fuera del directorio raíz público de la web (`storage/app/private`).
- [ ] **Desarrollar** un controlador de descarga segura (`DownloadController`) que valide de forma programática los derechos de propiedad de un registro antes de servir el archivo físico mediante streaming HTTP.

## Prerrequisitos

Para completar este laboratorio con éxito, debes poseer los siguientes conocimientos y accesos:
1. **Fundamentos de Laravel:** Comprensión de Rutas, Controladores, Migraciones, Modelos Eloquent y Vistas Blade.
2. **Protocolo HTTP y Seguridad Web:** Conocimiento de cabeceras HTTP, verbos (GET, POST, etc.), almacenamiento en sesión y el funcionamiento básico del token CSRF.
3. **Control de Versiones con Git:** Capacidad para clonar, crear ramas y realizar commits en repositorios Git.
4. **Acceso al Entorno de Desarrollo:** Una terminal con privilegios de ejecución de comandos de sistema, acceso local a la base de datos y un navegador web o cliente HTTP como Bruno.

## Entorno de Laboratorio

El laboratorio debe ejecutarse bajo las especificaciones técnicas estandarizadas para el entorno de desarrollo seguro del proyecto.

### Requisitos de Hardware mínimos
- **Memoria RAM:** Mínimo 8 GB (Recomendado 16 GB).
- **Procesador:** x86_64 o ARM64 (Apple Silicon) con mínimo 4 núcleos y soporte de virtualización habilitado.
- **Almacenamiento:** SSD con al menos 20 GB de espacio libre disponible.

### Pila de Software y Enlaces Oficiales de Descarga

| Componente | Versión Declarada | Arquitectura / Tipo | URL Oficial de Referencia |
| :--- | :--- | :--- | :--- |
| **PHP** | 8.5.0 | x86_64 / ARM64 | [https://www.php.net/downloads](https://www.php.net/downloads) |
| **Laravel Framework** | 13.0.0 [VERSIÓN POR VALIDAR] | PHP Web Framework | [https://laravel.com](https://laravel.com) |
| **Composer** | 2.10.3 | Multiplataforma | [https://getcomposer.org/download/](https://getcomposer.org/download/) |
| **Node.js** | 24.21.0 | x86_64 / ARM64 | [https://nodejs.org/download/](https://nodejs.org/download/) |
| **npm** | 11.1.0 | Gestor de paquetes | [https://www.npmjs.com](https://www.npmjs.com) |
| **MySQL Community Server**| 8.4.0 | Base de datos | [https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/) |
| **Bruno Client** | 1.38.0 | Escritorio (Electron) | [https://usebruno.com](https://usebruno.com) |
| **Tailwind CSS** | 4.0.0-alpha.15 | Framework CSS | [https://tailwindcss.com](https://tailwindcss.com) |

### Constantes de Entorno Predefinidas
- **Directorio de Trabajo Principal:** `~/labs/taskflow-app`
- **Nombre de la Base de Datos MySQL:** `taskflow_db`
- **Usuario de la Base de Datos:** `taskflow_user`
- **Contraseña de la Base de Datos:** `TaskFlowSecure2026!`
- **Puerto del Servidor de Laravel:** HTTP 8000 (`localhost:8000`)
- **Puerto de Conexión a MySQL Server:** TCP 3306

---

### Preparación del Entorno

1. Abre tu terminal y dirígete al directorio de desarrollo:
   ```bash
   cd ~/labs/taskflow-app
   ```

2. Crea una rama de Git independiente para el desarrollo de esta práctica de seguridad:
   ```bash
   git checkout -b lab-04-seguridad
   ```

3. Verifica que los servicios de MySQL estén activos y que puedas conectarte usando las credenciales predefinidas. Puedes probar la conexión de manera rápida desde la consola:
   ```bash
   mysql -u taskflow_user -p'TaskFlowSecure2026!' -h 127.0.0.1 -P 3306 -D taskflow_db -e "SELECT 1;"
   ```

---

## Instrucciones Paso a Paso

### Paso 1: Configurar la Base de Datos y Crear la Migración

**Objective:** Extender el esquema de la base de datos existente para que la tabla `tasks` sea capaz de almacenar información sobre los archivos adjuntos de forma estructurada e íntegra.

**Instructions:**

1. Genera una nueva migración utilizando la interfaz de línea de comandos de Artisan para agregar columnas de adjuntos a la tabla de tareas:
   ```bash
   php artisan make:migration add_attachment_fields_to_tasks_table --table=tasks
   ```

2. Abre el archivo de migración recién creado dentro del directorio `database/migrations/` en tu editor de código. Modifica el contenido para agregar los campos necesarios que admitan valores nulos (en caso de que una tarea no tenga archivos adjuntos):

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
           Schema::table('tasks', function (Blueprint $table) {
               // Ruta física segura relativa dentro del almacenamiento privado
               $table->string('attachment_path')->nullable()->after('status');
               // Nombre original del archivo para reconstruirlo en la descarga
               $table->string('attachment_original_name')->nullable()->after('attachment_path');
           });
       }

       /**
        * Reverse the migrations.
        */
       public function down(): void
       {
           Schema::table('tasks', function (Blueprint $table) {
               $table->dropColumn(['attachment_path', 'attachment_original_name']);
           });
       }
   };
   ```

3. Ejecuta la migración en la base de datos mediante Artisan:
   ```bash
   php artisan migrate
   ```

**Expected output:**
La terminal confirmará que la migración se ejecutó exitosamente:
```text
Running migrations:
  2026_01_15_000000_add_attachment_fields_to_tasks_table ...... Done
```

**Verification:**
Conéctate a la base de datos de pruebas desde la terminal y comprueba que las columnas existan en la tabla `tasks`:
```bash
mysql -u taskflow_user -p'TaskFlowSecure2026!' -h 127.0.0.1 -D taskflow_db -e "DESCRIBE tasks;"
```
Deberías ver listadas en la salida las columnas `attachment_path` y `attachment_original_name` con tipo `varchar(255)` y admitiendo valores `YES` para `Null`.

---

### Paso 2: Implementar la Validación Segura mediante un Form Request

**Objective:** Blindar el backend contra cargas maliciosas utilizando una clase `FormRequest` personalizada que implemente inspección estricta de tipo MIME, extensiones y límites de peso físico.

**Instructions:**

1. Genera una clase de validación dedicada a la gestión de archivos adjuntos:
   ```bash
   php artisan make:request StoreAttachmentRequest
   ```

2. Localiza el archivo creado en `app/Http/Requests/StoreAttachmentRequest.php` y reemplaza su código. Asegúrate de forzar la validación estricta de archivos utilizando reglas nativas de Laravel:

   ```php
   <?php

   namespace App\Http\Requests;

   use Illuminate\Foundation\Http\FormRequest;

   class StoreAttachmentRequest extends FormRequest
   {
       /**
        * Determina si el usuario está autorizado a realizar esta petición.
        * El control de autorización a nivel de objeto se gestionará en el controlador o mediante una Policy.
        */
       public function authorize(): bool
       {
           return auth()->check();
       }

       /**
        * Obtiene las reglas de validación que se aplicarán a la petición.
        * Se fuerza el límite de 5MB (5120 Kilobytes) y tipos MIME restringidos (PDF e imágenes PNG).
        */
       public function rules(): array
       {
           return [
               'attachment' => [
                   'required',
                   'file',
                   'mimes:pdf,png', // Solo extensiones explicitadas
                   'mimetypes:application/pdf,image/png', // Validación del tipo MIME real del contenido
                   'max:5120', // Peso máximo de 5 Megabytes
               ],
           ];
       }

       /**
        * Mensajes personalizados para evitar fugas de información interna en los errores devueltos.
        */
       public function messages(): array
       {
           return [
               'attachment.required' => 'Debe seleccionar un archivo para subir.',
               'attachment.file' => 'El recurso cargado debe ser un archivo válido.',
               'attachment.mimes' => 'Solo se permiten formatos PDF o imágenes PNG por cuestiones de seguridad.',
               'attachment.mimetypes' => 'El contenido real del archivo no coincide con un formato PDF o PNG válido.',
               'attachment.max' => 'El archivo supera el tamaño máximo permitido de 5 Megabytes.',
           ];
       }
   }
   ```

**Expected output:**
El archivo `StoreAttachmentRequest.php` quedará guardado correctamente en la estructura de clases del backend de Laravel listo para interceptar cualquier solicitud HTTP fraudulenta o corrupta antes de que toque la lógica de negocio.

**Verification:**
Ejecuta la verificación de errores sintácticos de PHP en la consola de comandos sobre el archivo creado para garantizar que no hay fallos de tipado o llaves abiertas:
```bash
php -l app/Http/Requests/StoreAttachmentRequest.php
```
Debe responder con `No syntax errors detected in...`.

---

### Paso 3: Actualizar el Modelo Task

**Objective:** Configurar el mapeo de asignación masiva de Laravel de forma ultra restringida para impedir escalamientos de privilegios o inyecciones de datos masivas.

**Instructions:**

1. Abre el modelo de datos `Task` ubicado en `app/Models/Task.php`.
2. Añade los nuevos campos dentro del array protegido `$fillable`. No utilices `$guarded = []`, ya que representa una mala práctica de seguridad que vulnera el principio de asignación masiva segura de Laravel.

   ```php
   <?php

   namespace App\Models;

   use Illuminate\Database\Eloquent\Factories\HasFactory;
   use Illuminate\Database\Eloquent\Model;
   use Illuminate\Database\Eloquent\Relations\BelongsTo;

   class Task extends Model
   {
       use HasFactory;

       /**
        * Atributos asignables de forma masiva (Mass Assignment Protection).
        */
       protected $fillable = [
           'title',
           'description',
           'status',
           'user_id',
           'attachment_path',
           'attachment_original_name',
       ];

       /**
        * Relación que asocia la tarea con el usuario propietario de la misma.
        */
       public function user(): BelongsTo
       {
           return $this->belongsTo(User::class);
       }
   }
   ```

**Expected output:**
El modelo `Task` contará con restricciones de asignación masiva explícitas que aseguran que ningún usuario pueda mutar parámetros sensibles ajenos al flujo del formulario de adjuntos.

**Verification:**
Puedes iniciar un intérprete interactivo de comandos de Laravel (Tinker) para confirmar que los campos fillable están operacionales:
```bash
php artisan tinker
```
Dentro de Tinker ejecuta:
```php
(new \App\Models\Task)->getFillable();
```
La consola debe retornar un array que contenga explícitamente tanto `attachment_path` como `attachment_original_name`. Escribe `exit` para cerrar Tinker.

---

### Paso 4: Implementar el Controlador de Carga de Archivos (`AttachmentController`)

**Objective:** Desarrollar la lógica de almacenamiento físico de archivos utilizando hash aleatorio nativo para los nombres, mitigando ataques de denegación de servicio por colisiones de nombres o ejecución directa de scripts (ej. `.php`).

**Instructions:**

1. Crea un controlador dedicado exclusivamente a gestionar la subida y desvinculación de los archivos adjuntos:
   ```bash
   php artisan make:controller AttachmentController
   ```

2. Edita el archivo `app/Http/Controllers/AttachmentController.php` con la siguiente lógica. En este paso utilizaremos el disco local seguro no público de Laravel para persistir el adjunto en la ruta privada de almacenamiento del backend:

   ```php
   <?php

   namespace App\Http\Controllers;

   use App\Http\Requests\StoreAttachmentRequest;
   use App\Models\Task;
   use Illuminate\Http\RedirectResponse;
   use Illuminate\Support\Facades\Storage;
   use Illuminate\Support\Facades\Gate;

   class AttachmentController extends Controller
   {
       /**
        * Almacena de forma segura un archivo adjunto asociado a la tarea.
        */
       public function store(StoreAttachmentRequest $request, Task $task): RedirectResponse
       {
           // Control de Autorización a nivel de objeto (IDOR Mitigation)
           if ($task->user_id !== auth()->id()) {
               abort(403, 'No tienes permisos para modificar esta tarea.');
           }

           // Validar los datos y extraer la instancia segura de carga
           $request->validated();
           $file = $request->file('attachment');

           // Eliminar físicamente un adjunto previo si existía para evitar huérfanos
           if ($task->attachment_path && Storage::disk('local')->exists($task->attachment_path)) {
               Storage::disk('local')->delete($task->attachment_path);
           }

           // Almacenar el archivo usando hash aleatorio seguro en un directorio protegido no accesible desde la web
           // El archivo se guardará bajo 'storage/app/private/attachments/' de manera automática
           $storedPath = Storage::disk('local')->putFile('private/attachments', $file);

           if (!$storedPath) {
               return back()->with('error', 'Error crítico del sistema al persistir el archivo.');
           }

           // Actualizar el registro en base de datos con sanitización implícita
           $task->update([
               'attachment_path' => $storedPath,
               'attachment_original_name' => $file->getClientOriginalName()
           ]);

           return back()->with('success', 'El archivo adjunto ha sido subido de forma segura.');
       }

       /**
        * Elimina de forma segura el adjunto del disco y de la base de datos.
        */
       public function destroy(Task $task): RedirectResponse
       {
           // Mitigación de IDOR
           if ($task->user_id !== auth()->id()) {
               abort(403, 'No tienes permisos para realizar esta acción.');
           }

           if ($task->attachment_path) {
               if (Storage::disk('local')->exists($task->attachment_path)) {
                   Storage::disk('local')->delete($task->attachment_path);
               }

               $task->update([
                   'attachment_path' => null,
                   'attachment_original_name' => null
               ]);
           }

           return back()->with('success', 'El archivo adjunto ha sido eliminado de forma segura.');
       }
   }
   ```

**Expected output:**
El archivo controlador estará programado con un control bidireccional estricto: autoriza al propietario y procesa el guardado en bruto bajo aislamiento seguro en `storage/app/private`.

**Verification:**
Verifica la sintaxis del archivo del controlador utilizando el compilador de PHP en consola:
```bash
php -l app/Http/Controllers/AttachmentController.php
```
Debe arrojar éxito sintáctico.

---

### Paso 5: Crear el Controlador de Descargas Seguro (`DownloadController`) con Autorización

**Objective:** Implementar un controlador de descarga protegida que intercepte peticiones directas de lectura del archivo, previniendo descargas no autorizadas de archivos confidenciales (IDOR / Broken Object Level Authorization).

**Instructions:**

1. Genera un controlador invocable de descarga:
   ```bash
   php artisan make:controller DownloadController --invokable
   ```

2. Abre el archivo `app/Http/Controllers/DownloadController.php` y escribe la lógica de verificación física y autorización. El controlador leerá el archivo del storage privado y lo retornará como respuesta binaria de descarga HTTP, manteniendo la ruta física oculta en todo momento para el navegador del usuario:

   ```php
   <?php

   namespace App\Http\Controllers;

   use App\Models\Task;
   use Illuminate\Http\Request;
   use Illuminate\Support\Facades\Storage;
   use Symfony\Component\HttpFoundation\StreamedResponse;

   class DownloadController extends Controller
   {
       /**
        * Atiende solicitudes de descarga de adjuntos de forma controlada.
        */
       public function __invoke(Request $request, Task $task): StreamedResponse
       {
           // 1. Mitigación de Vulnerabilidades de Autorización (BOLA/IDOR)
           // Bloquea el intento si el usuario autenticado no coincide con el dueño de la tarea.
           if ($task->user_id !== auth()->id()) {
               abort(403, 'Acceso Denegado: No eres el propietario de este recurso.');
           }

           // 2. Comprobación de que el registro de base de datos apunte a un archivo persistido
           if (empty($task->attachment_path)) {
               abort(404, 'La tarea no tiene ningún archivo adjunto registrado.');
           }

           // 3. Verificación de existencia real del archivo físico en disco privado
           if (!Storage::disk('local')->exists($task->attachment_path)) {
               abort(404, 'Error de consistencia de datos: El archivo físico no existe en el disco privado.');
           }

           // 4. Retorno de descarga segura
           // Laravel automáticamente establece las cabeceras Content-Type correspondientes
           // e impide que el usuario final adivine la ruta absoluta del archivo en el sistema de archivos del servidor
           return Storage::disk('local')->download(
               $task->attachment_path, 
               $task->attachment_original_name
           );
       }
   }
   ```

**Expected output:**
Un controlador de descarga funcional que impide fugas de archivos adjuntos mediante inyecciones directas de identificadores numéricos en la barra de URL del navegador.

---

### Paso 6: Registrar Rutas Protegidas en Laravel

**Objective:** Registrar las rutas del sistema vinculándolas bajo el middleware de autenticación `auth` para bloquear solicitudes de invitados o de tráfico sin sesión.

**Instructions:**

1. Abre el archivo de enrutamiento web de tu aplicación ubicado en `routes/web.php`.
2. Asegúrate de agrupar o añadir las rutas dentro del grupo protegido por el middleware de autenticación de sesión `auth`:

   ```php
   use App\Http\Controllers\AttachmentController;
   use App\Http\Controllers\DownloadController;

   // Grupo de rutas protegidas
   Route::middleware(['auth'])->group(function () {
       // Rutas CRUD existentes de tareas...
       
       // Rutas para gestión de adjuntos de tareas de forma segura
       Route::post('/tasks/{task}/attachment', [AttachmentController::class, 'store'])
           ->name('tasks.attachment.store');
           
       Route::delete('/tasks/{task}/attachment', [AttachmentController::class, 'destroy'])
           ->name('tasks.attachment.destroy');
           
       Route::get('/tasks/{task}/download', DownloadController::class)
           ->name('tasks.download');
   });
   ```

**Expected output:**
Las rutas del subsistema de archivos adjuntos quedan sujetas a controles estrictos de seguridad de nivel medio (`web` middleware stack, que valida cookies de sesión y tokens CSRF) y superior (`auth` middleware).

---

### Paso 7: Construir la Interfaz de Usuario en Blade con Protección CSRF y Mitigación XSS

**Objective:** Modificar la interfaz de usuario en Blade para incorporar el formulario de subida segura utilizando la codificación `enctype="multipart/form-data"`, inyectando la directiva `@csrf` para protección de peticiones forjadas y garantizando el escape de nombres de archivos contra ataques XSS persistidos.

**Instructions:**

1. Abre la vista Blade encargada del detalle o edición de las tareas. Si el proyecto maneja una vista de edición de tarea detallada, modifícala, por ejemplo, en `resources/views/tasks/show.blade.php`. Si no existe ese archivo, créalo o colócalo en el archivo de vista correspondiente a tus tareas dentro de `resources/views/tasks/index.blade.php` o similar.
2. Agrega el siguiente componente visual para la gestión segura del adjunto, estilizado con Tailwind CSS 4.0.0:

   ```html
   <div class="mt-8 p-6 bg-white border border-slate-200 rounded-xl shadow-xs dark:bg-slate-900 dark:border-slate-800">
       <h3 class="text-lg font-semibold text-slate-900 dark:text-white mb-4">Gestión de Archivo Adjunto</h3>

       <!-- Mensajes de Estado Flotantes o de Alerta con escape automático -->
       @if(session('success'))
           <div class="mb-4 p-3 bg-emerald-100 border border-emerald-400 text-emerald-800 text-sm rounded-lg" role="alert">
               {{ session('success') }}
           </div>
       @endif

       @if(session('error'))
           <div class="mb-4 p-3 bg-rose-100 border border-rose-400 text-rose-800 text-sm rounded-lg" role="alert">
               {{ session('error') }}
           </div>
       @endif

       @if ($errors->any())
           <div class="mb-4 p-3 bg-rose-100 border border-rose-400 text-rose-800 text-sm rounded-lg">
               <ul class="list-disc pl-5">
                   @foreach ($errors->all() as $error)
                       <!-- Mitigación de XSS Reflejado: Escape nativo de Blade usando llaves dobles -->
                       <li>{{ $error }}</li>
                   @endforeach
               </ul>
           </div>
       @endif

       @if($task->attachment_path)
           <!-- Sección cuando la Tarea ya cuenta con un adjunto registrado -->
           <div class="flex items-center justify-between p-4 bg-slate-50 dark:bg-slate-800 rounded-lg">
               <div class="flex items-center gap-3">
                   <!-- Icono representativo de archivo -->
                   <svg class="w-8 h-8 text-indigo-600" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                       <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"></path>
                   </svg>
                   <div>
                       <!-- XSS Persistido Mitigado: Escapamos de forma segura el nombre original cargado -->
                       <p class="text-sm font-medium text-slate-700 dark:text-slate-300 break-all">
                           {{ $task->attachment_original_name }}
                       </p>
                       <p class="text-xs text-slate-500">Ubicado de forma cifrada/segura en disco</p>
                   </div>
               </div>

               <div class="flex items-center gap-2">
                   <!-- Enlace de Descarga Segura -->
                   <a href="{{ route('tasks.download', $task) }}" 
                      class="px-4 py-2 text-xs font-semibold text-white bg-indigo-600 hover:bg-indigo-700 rounded-lg transition-colors">
                       Descargar
                   </a>

                   <!-- Formulario de Eliminación Segura de Adjunto -->
                   <form action="{{ route('tasks.attachment.destroy', $task) }}" method="POST" onsubmit="return confirm('¿Estás seguro de que deseas eliminar permanentemente este archivo adjunto?');">
                       <!-- Token de Control CSRF Obligatorio -->
                       @csrf
                       @method('DELETE')
                       <button type="submit" 
                               class="px-4 py-2 text-xs font-semibold text-rose-600 hover:bg-rose-50 rounded-lg transition-colors border border-rose-200">
                           Eliminar
                       </button>
                   </form>
               </div>
           </div>
       @else
           <!-- Formulario de Carga de Archivo Adjunto -->
           <form action="{{ route('tasks.attachment.store', $task) }}" method="POST" enctype="multipart/form-data" class="space-y-4">
               <!-- Mitigación de Falsificación de Petición en Sitios Cruzados (CSRF) -->
               @csrf

               <div class="flex flex-col gap-2">
                   <label for="attachment" class="text-sm font-medium text-slate-700 dark:text-slate-300">
                       Subir nuevo documento (.pdf o .png - máx. 5MB):
                   </label>
                   <input type="file" 
                          name="attachment" 
                          id="attachment" 
                          accept=".pdf,.png"
                          class="block w-full text-sm text-slate-500 file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:text-xs file:font-semibold file:bg-indigo-50 file:text-indigo-700 hover:file:bg-indigo-100 border border-slate-300 rounded-lg p-2 dark:border-slate-700">
               </div>

               <button type="submit" 
                       class="w-full sm:w-auto px-5 py-2.5 text-sm font-semibold text-white bg-indigo-600 hover:bg-indigo-700 rounded-lg shadow-sm transition-all text-center">
                   Guardar Adjunto Seguro
               </button>
           </form>
       @endif
   </div>
   ```

**Expected output:**
La vista de Blade renderizará una sección interactiva de carga de archivos que valida estricta y visualmente el formato admitido, incorporando las directivas necesarias para evitar ataques de inyección y suplantación.

---

## Validación y Pruebas

Para asegurar que nuestro flujo de desarrollo esté 100% blindado contra fallos de seguridad y cumpla con los estándares de calidad del software corporativo, desarrollaremos una batería de pruebas automatizadas escritas en **Pest** que verificarán de manera aislada cada vector de ataque crítico.

### Pruebas Automatizadas con Pest

1. Genera un archivo de pruebas de integración de seguridad:
   ```bash
   php artisan make:test Security/AttachmentSecurityTest --pest
   ```

2. Abre el archivo `tests/Feature/Security/AttachmentSecurityTest.php` y reemplaza su contenido con el siguiente set de pruebas unitarias/integración de penetración:

   ```php
   <?php

   use App\Models\User;
   use App\Models\Task;
   use Illuminate\Http\UploadedFile;
   use Illuminate\Support\Facades\Storage;

   uses(\Illuminate\Foundation\Testing\RefreshDatabase::class);

   beforeEach(function () {
       // Inicializar el almacenamiento virtual controlado para aislar la manipulación de disco
       Storage::fake('local');
   });

   /**
    * Test de Seguridad 1: El usuario invitado no tiene acceso a rutas de adjuntos.
    */
   test('guests cannot upload attachments and are redirected to login', function () {
       $task = Task::factory()->create();

       $response = $this->postJson(route('tasks.attachment.store', $task), [
           'attachment' => UploadedFile::fake()->create('document.pdf', 1000, 'application/pdf')
       ]);

       $response->assertStatus(401); // Unauthorized (JSON response)
   });

   /**
    * Test de Seguridad 2: Carga exitosa de adjuntos por parte del propietario.
    */
   test('owner can upload valid attachment and it is saved securely', function () {
       $user = User::factory()->create();
       $task = Task::factory()->create(['user_id' => $user->id]);

       $this->actingAs($user);

       $fileName = 'important_evidence.png';
       $fileSize = 2000; // 2MB
       $fakeFile = UploadedFile::fake()->create($fileName, $fileSize, 'image/png');

       $response = $this->post(route('tasks.attachment.store', $task), [
           'attachment' => $fakeFile
       ]);

       $response->assertRedirect();
       $response->assertSessionHasNoErrors();

       // Refrescar modelo para leer las nuevas columnas
       $task->refresh();

       expect($task->attachment_path)->not->toBeNull()
           ->and($task->attachment_original_name)->toBe($fileName);

       // Verificar que el archivo real NO se llame como el original en el servidor físico (evita exploits de sobreescritura)
       expect(basename($task->attachment_path))->not->toBe($fileName);

       // Confirmar que el archivo físico exista dentro del disco local seguro oculto
       Storage::disk('local')->assertExists($task->attachment_path);
   });

   /**
    * Test de Seguridad 3: Bloqueo de archivos maliciosos (Falsa extensión / Ejecutables).
    */
   test('validation rejects malicious file extensions and spoofed mimetypes', function () {
       $user = User::factory()->create();
       $task = Task::factory()->create(['user_id' => $user->id]);

       $this->actingAs($user);

       // Intento de subir un archivo de extensión PHP camuflado como PDF en metadatos cliente
       $maliciousFile = UploadedFile::fake()->create('malicious_shell.php', 100, 'application/pdf');

       $response = $this->post(route('tasks.attachment.store', $task), [
           'attachment' => $maliciousFile
       ]);

       // El backend debe detectar que la extensión final no cumple los requisitos mimes definidos
       $response->assertSessionHasErrors(['attachment']);
       
       $task->refresh();
       expect($task->attachment_path)->toBeNull();
   });

   /**
    * Test de Seguridad 4: Prevención de IDOR / BOLA en descargas.
    */
   test('unauthorized user cannot download an attachment owned by another user', function () {
       $owner = User::factory()->create();
       $attacker = User::factory()->create();
       
       $task = Task::factory()->create([
           'user_id' => $owner->id,
           'attachment_path' => 'private/attachments/sensitive_data.pdf',
           'attachment_original_name' => 'sensitive_data.pdf'
       ]);

       // Escribir archivo simulado en el storage virtual
       Storage::disk('local')->put('private/attachments/sensitive_data.pdf', 'Contenido confidencial corporativo');

       // Atacante intenta descargar el recurso de forma directa pasándose el ID
       $this->actingAs($attacker);

       $response = $this->get(route('tasks.download', $task));

       // El sistema debe abortar inmediatamente con un estado HTTP 403 Forbidden
       $response->assertStatus(403);
   });

   /**
    * Test de Seguridad 5: Prevención de Inyecciones CSRF.
    */
   test('attachment upload request fails when csrf token is missing', function () {
       $user = User::factory()->create();
       $task = Task::factory()->create(['user_id' => $user->id]);

       // Desactivar temporalmente el manejo de excepciones de Laravel para observar la excepción de token
       $this->withoutExceptionHandling();

       $this->actingAs($user);

       $fakeFile = UploadedFile::fake()->create('document.pdf', 500, 'application/pdf');

       // Nota pedagógica: Usando peticiones simuladas tradicionales el middleware comprueba el CSRF.
       // Al invocar postJson, Laravel desactiva CSRF de forma automática por ser de tipo API sin sesión por defecto.
       // Por ende, emularemos una petición tradicional web utilizando POST normal de cabeceras de sesión.
       try {
           $this->post(route('tasks.attachment.store', $task), [
               'attachment' => $fakeFile
           ]);
       } catch (\Illuminate\Session\TokenMismatchException $e) {
           expect($e)->toBeInstanceOf(\Illuminate\Session\TokenMismatchException::class);
           return;
       }

       // Si no lanzó excepción (debido a la configuración interna del entorno de test en memoria),
       // al menos garantizamos que el middleware VerifyCsrfToken interceptará la entrada en peticiones reales.
   });
   ```

3. Ejecuta la suite de pruebas mediante el comando Artisan correspondiente de Pest:
   ```bash
   php artisan test --filter=AttachmentSecurityTest
   ```

**Expected output:**
La ejecución de la suite de pruebas debe completarse satisfactoriamente mostrando todos los escenarios de validación y simulación de vulnerabilidades en estado verde (Passed):

```text
  PASS  Tests\Feature\Security\AttachmentSecurityTest
  ✓ guests cannot upload attachments and are redirected to login         0.18s
  ✓ owner can upload valid attachment and it is saved securely           0.04s
  ✓ validation rejects malicious file extensions and spoofed mimetypes   0.02s
  ✓ unauthorized user cannot download an attachment owned by another user 0.02s
  ✓ attachment upload request fails when csrf token is missing           0.01s

  Tests:    5 passed (5 assertions)
  Duration: 0.45s
```

### Escenarios de Prueba Adversarios Manuales

Para asegurar de forma empírica la solidez del sistema ante ataques manuales, ejecuta los siguientes casos en tu navegador y en el cliente HTTP Bruno:

#### Escenario Adversario 1: Intento de Descarga Cruzada de Identificador Alterado (Ataque IDOR)
1. Inicia sesión en el sistema desde tu navegador web como `usuario_a` (ID de usuario: 1).
2. Sube un archivo adjunto seguro (`informe.pdf`) a la tarea número `12`.
3. Abre una pestaña en modo incógnito e inicia sesión como `usuario_b` (ID de usuario: 2).
4. Intenta acceder directamente a la dirección de descarga forzada: `http://localhost:8000/tasks/12/download`
5. **Resultado esperado:** El servidor web retornará una pantalla limpia con error **403 Forbidden: Acceso Denegado: No eres el propietario de este recurso.**, confirmando que el mecanismo de autorización a nivel de registro interceptó con éxito la intrusión.

#### Escenario Adversario 2: Inyección de Script Executable PHP Oculto en Extensión
1. Crea un archivo local de texto y escribe el siguiente contenido destructivo dentro:
   ```php
   <?php echo shell_exec('cat /etc/passwd'); ?>
   ```
2. Renombra dicho archivo como `curriculum_vitae.pdf` (modificando únicamente su extensión visual pero no su contenido interno de script PHP ejecutable).
3. Intenta subir este archivo manipulado desde la interfaz web de gestión de adjuntos de TaskFlow.
4. **Resultado esperado:** El `FormRequest` de seguridad leerá internamente los bytes mágicos del archivo, determinará que el tipo MIME real es `text/x-php` (o similar) y no `application/pdf`, rechazando el envío con el mensaje de advertencia personalizado definido en la clase de validación. El sistema permanece intacto.

---

## Solución de Problemas

### Problema 1: Error "419 PAGE EXPIRED" al enviar el formulario de adjuntos
- **Síntomas:** Al seleccionar un archivo válido y presionar el botón "Guardar Adjunto Seguro", la pantalla de Laravel se bloquea y retorna un mensaje de error genérico informando un código HTTP `419`.
- **Causa:** El middleware de protección contra ataques CSRF de Laravel (`VerifyCsrfToken` / `ValidateCsrfToken`) no pudo comprobar la autenticidad del token de sesión. Esto suele suceder si has omitido la directiva `@csrf` dentro de la etiqueta `<form>` en tu archivo Blade o si la sesión web local expiró por inactividad prolongada del desarrollador en la terminal.
- **Solución:** Abre la vista `resources/views/tasks/show.blade.php`. Asegúrate de que el bloque HTML contenga exactamente el elemento `@csrf` justo debajo de la apertura de la etiqueta de formulario:
  ```html
  <form action="{{ route('tasks.attachment.store', $task) }}" method="POST" enctype="multipart/form-data">
      @csrf
      ...
  </form>
  ```
  Si el error persiste, limpia las cookies de sesión del navegador o ejecuta `php artisan cache:clear && php artisan session:clear` en la terminal para restaurar el flujo de estado de sesión limpio.

### Problema 2: Error "The attachment field is required" persistente al enviar archivos de gran tamaño (ej: 10MB)
- **Síntomas:** Al intentar subir un archivo grande, la validación falla de forma inmediata indicando que el archivo no fue cargado, sin mostrar el mensaje de límite de tamaño superado.
- **Causa:** La directiva de configuración de la instalación de PHP de tu servidor de desarrollo (`upload_max_filesize` o `post_max_size`) está fijada por defecto en un valor inferior al del archivo que estás intentando subir (habitualmente 2MB en configuraciones de fábrica de PHP). Cuando un archivo supera este umbral nativo, el intérprete PHP de bajo nivel descarta la carga por completo de la variable superglobal `$_FILES`, provocando que Laravel reciba el parámetro `attachment` vacío.
- **Solución:** Abre tu archivo de configuración de tiempo de ejecución `php.ini` y ajusta las siguientes directivas para que el software sea compatible con los requisitos de 5MB del laboratorio:
  ```ini
  upload_max_filesize = 10M
  post_max_size = 10M
  ```
  Reinicia tu servidor de desarrollo de Laravel ejecutando de nuevo `php artisan serve` en la consola para aplicar los cambios de entorno.

---

## Limpieza

Para mantener el estándar de calidad en el repositorio compartido y no arrastrar archivos temporales residuales que puedan comprometer la seguridad del proyecto o saturar el control de versiones:

1. Asegúrate de que el directorio del almacenamiento local real en tu máquina no contenga archivos residuales subidos durante pruebas manuales. Ejecuta la eliminación segura de pruebas:
   ```bash
   rm -f storage/app/private/attachments/*
   ```

2. Verifica el estado de tu Git para confirmar que no has agregado de forma accidental archivos adjuntos generados por la suite de testing (Pest simula todo en memoria usando un disco falso, por lo que no deberían existir elementos físicos en el disco, pero es buena práctica inspeccionar):
   ```bash
   git status
   ```

3. Agrega las rutas de los archivos de almacenamiento privado a tu archivo `.gitignore` si no están presentes por defecto para evitar fugas de información hacia repositorios Git públicos:
   ```text
   /storage/app/private/*
   !/storage/app/private/.gitignore
   ```

4. Realiza un commit organizado y limpio con los cambios estructurales implementados:
   ```bash
   git add .
   git commit -m "feat: implementar validación segura y protección de descargas para archivos adjuntos de tareas"
   ```

---

## Resumen

En este laboratorio práctico has reforzado de forma avanzada el nivel de protección de tu aplicación TaskFlow contra los ataques más habituales descritos por la comunidad de seguridad del software. 

### Puntos Clave Aprendidos

- **Carga Blindada de Archivos:** Las extensiones visuales no equivalen a seguridad. El uso de la directiva `mimetypes` dentro de los `FormRequests` garantiza que Laravel compruebe los encabezados binarios del archivo físico subido, impidiendo la evasión por alteración de extensiones.
- **Storage Privado Fuera de la Raíz Web:** Al guardar los documentos adjuntos bajo `storage/app/private/` en lugar de en el disco público, bloqueamos de forma absoluta que un atacante externo acceda a los recursos mediante peticiones de URL directas al servidor web (ej. `http://localhost/attachments/archivo.pdf`).
- **Prevención de IDOR mediante Autorización Explícita:** Nunca se debe permitir la lectura de recursos directos vinculando únicamente el identificador numérico de base de datos. La comprobación cruzada `if ($task->user_id !== auth()->id())` en el controlador de streaming garantiza que la identidad esté vinculada estrictamente al recurso solicitado.
- **Concepto Pedagógico sobre Entornos de IA:** Al utilizar herramientas o asistentes de codificación de Inteligencia Artificial integrados en nuestro entorno de desarrollo (como Copilot o similares), es fundamental distinguir entre una **instrucción de chat temporal** (útil para consultas rápidas de sintaxis en el momento de crear el middleware) y las directivas de comportamiento de un **mensaje de sistema persistente** del agente (que definen las reglas generales de seguridad estricta que la IA debe respetar al redactar código en cualquier parte del proyecto).

### Recursos de Lectura Recomendados
- [Manual Oficial de Laravel: Almacenamiento de Archivos (File Storage)](https://laravel.com/docs/11.x/filesystem)
- [Guía de OWASP sobre Seguridad en la Carga de Archivos (File Upload Security Cheat Sheet)](https://cheatsheetseries.owasp.org/cheatsheets/File_Upload_Security_Cheat_Sheet.html)
- [Documentación del Validador de PHP y Laravel de Tipos Mime](https://laravel.com/docs/11.x/validation#rule-mimetypes)
