# 8 Práctica: Desarrollo y consumo de APIs con Laravel 13.x, Laravel Sanctum y Bruno o Postman

## Metadatos

| Atributo | Detalle |
| :--- | :--- |
| **Duración** | 210 minutos |
| **Complejidad** | Alta (Hard) |
| **Nivel de Bloom** | Crear (Create) |
| **Objetivo** | Diseñar, proteger y consumir una API RESTful estructurada con Laravel Sanctum, integrando interfaces interactivas y validaciones de seguridad rigurosas. |

## Descripción General

En este laboratorio práctico de nivel avanzado, configurarás e implementarás una infraestructura completa de API RESTful utilizando **Laravel Sanctum 5.0.0** integrado sobre **Laravel Framework 11.8.0** (compatible con entornos 13.x). Diseñarás controladores de API con respuestas JSON estructuradas bajo estándares HTTP estrictos para la gestión de tareas (`Task`). 

Posteriormente, construirás una interfaz híbrida utilizando una vista de Blade potenciada por **Vite 6.0.5** y **Tailwind CSS 4.0.0-alpha.15**, que consumirá de forma asíncrona estos endpoints usando la API Fetch nativa de JavaScript, simulando el comportamiento de una Single Page Application (SPA). Por último, documentarás, simularás y validarás el flujo completo de autenticación y transacciones mediante colecciones avanzadas en el cliente de código abierto **Bruno 1.38.0**, y ejecutarás pruebas automatizadas de integración utilizando el framework de pruebas **Pest/PHPUnit**.

## Objetivos de Aprendizaje

Al finalizar este laboratorio, serás capaz de:
- [ ] Instalar, configurar y extender **Laravel Sanctum 5.0.0** para la emisión y validación estricta de tokens de acceso rápido (*Personal Access Tokens*).
- [ ] Crear controladores de API estructurados que sigan las convenciones RESTful y devuelvan códigos de estado HTTP correctos junto con payloads JSON consistentes.
- [ ] Consumir de forma asíncrona endpoints protegidos mediante la API Fetch de JavaScript, gestionando estados de autenticación (Bearer Token) en el cliente de manera segura.
- [ ] Configurar entornos de pruebas funcionales en el cliente API **Bruno 1.38.0**, incluyendo la automatización de flujos de autenticación mediante variables de entorno.
- [ ] Escribir y ejecutar pruebas unitarias y de integración con **Pest** para asegurar la calidad de las respuestas de la API, previniendo regresiones de seguridad y lógica de negocio.

## Prerrequisitos

### Conocimientos Teóricos y Técnicos
- Comprensión profunda del protocolo HTTP (métodos GET, POST, PUT/PATCH, DELETE, cabeceras y códigos de estado).
- Familiaridad con el estilo arquitectónico REST y el formato de datos JSON.
- Dominio de la arquitectura MVC en Laravel, migraciones de bases de datos y Eloquent ORM.
- Conocimiento intermedio de JavaScript asíncrono (Promises, async/await, Fetch API) y manipulación del DOM.

### Accesos y Software Requerido
- Acceso a una terminal con privilegios de ejecución de comandos de sistema.
- Entorno de desarrollo local configurado con las tecnologías listadas en la sección "Entorno de Laboratorio".
- Cuenta de usuario configurada para el motor de base de datos MySQL local.
- Conexión a Internet activa para la descarga de paquetes adicionales (Composer y npm).

---

## Entorno de Laboratorio

### Requisitos de Hardware
- **Procesador:** Arquitectura x86_64 o ARM64 (Apple Silicon) de mínimo 4 núcleos físicos.
- **Memoria RAM:** Mínimo de 8 GB (Recomendado 16 GB).
- **Almacenamiento:** Unidad de Estado Sólido (SSD) con al menos 20 GB de espacio libre en disco.

### Requisitos de Software y Herramientas Auxiliares

A continuación se detallan las versiones específicas utilizadas y validadas para este laboratorio:

| Componente | Versión / Edición | Fuente Oficial |
| :--- | :--- | :--- |
| **PHP** | 8.5.0 (CLI / x64) | [PHP Official Website](https://www.php.net/) |
| **Composer** | 2.10.3 (Multiplataforma) | [Composer Official Website](https://getcomposer.org/) |
| **Node.js** | 24.21.0 (LTS) | [NodeJS Official Website](https://nodejs.org/) |
| **npm** | 11.1.0 | [npm Registry](https://www.npmjs.com/) |
| **MySQL Community Server** | 8.4.0 (LTS / TCP 3306) | [MySQL Downloads](https://dev.mysql.com/downloads/mysql/) |
| **SQLite (Fallback)** | 3.45.3 | [SQLite Official](https://www.sqlite.org/) |
| **Laravel Framework** | 11.8.0 / 13.x (Compatible) | [Laravel Docs](https://laravel.com/) |
| **Laravel Sanctum** | 5.0.0 | [Sanctum GitHub](https://github.com/laravel/sanctum) |
| **Bruno API Client** | 1.38.0 | [UseBruno Official](https://www.usebruno.com/) |
| **Tailwind CSS** | 4.0.0-alpha.15 | [Tailwind CSS Docs](https://tailwindcss.com/) |
| **React** | 19.0.0-rc-f9947a08-20240521 | [React Dev](https://react.dev/) |
| **TypeScript** | 5.4.5 | [TypeScript Lang](https://www.typescriptlang.org/) |
| **Inertia.js React Adapter** | 1.2.0 | [Inertia.js Website](https://inertiajs.com/) |

### Configuración del Entorno de Inteligencia Artificial (Copilot)
Si utilizas asistentes de codificación como **Microsoft 365 Copilot** (Licencia Enterprise para Desarrolladores) o la extensión **Copilot Chat v1.254.0** en VS Code, ten en cuenta las siguientes definiciones para interactuar de forma correcta:
- **Instrucción / Prompt:** La instrucción temporal que tú, como desarrollador, ingresas en la consola de chat para solicitar un fragmento de código o depurar un error.
- **Mensaje de Sistema (System Message):** La directiva persistente de nivel profundo configurada en la IA para definir su rol, restricciones de comportamiento y reglas de estilo.

### Datos de Configuración Críticos
- **Directorio de Trabajo Principal:** `~/labs/taskflow-app`
- **Nombre de la Base de Datos MySQL:** `taskflow_db`
- **Usuario de la Base de Datos:** `taskflow_user`
- **Contraseña de la Base de Datos:** `TaskFlowSecure2026!`
- **Puerto de Conexión MySQL:** `3306`
- **URL de Desarrollo de Laravel:** `http://localhost:8000`
- **URL de Desarrollo de Vite:** `http://localhost:5173`
- **Rama de Trabajo de Git:** `lab-05-api-sanctum`

---

## Instrucciones Paso a Paso

### Paso 1: Inicializar la rama de Git y configurar el entorno de base de datos

**Objetivo:** Crear una rama limpia de trabajo para consolidar los cambios del laboratorio actual y asegurar la correcta conectividad con la base de datos MySQL parametrizada bajo las credenciales del proyecto.

**Instrucciones:**

1. Abre tu terminal y navega hasta el directorio principal del laboratorio:
   ```bash
   cd ~/labs/taskflow-app
   ```
2. Crea e ingresa a una nueva rama de Git específica para este laboratorio:
   ```bash
   git checkout -b lab-05-api-sanctum
   ```
3. Abre el archivo `.env` del proyecto y verifica que las credenciales de conexión coincidan exactamente con la configuración especificada:
   ```env
   DB_CONNECTION=mysql
   DB_HOST=127.0.0.1
   DB_PORT=3306
   DB_DATABASE=taskflow_db
   DB_USERNAME=taskflow_user
   DB_PASSWORD=TaskFlowSecure2026!
   ```
4. Comprueba la conectividad de la base de datos ejecutando el comando de estado de migraciones de Laravel:
   ```bash
   php artisan db:show
   ```

**Resultado esperado:**
La terminal debe mostrar información estructurada sobre el servidor MySQL, la versión del motor de la base de datos, las tablas existentes y la confirmación de la conexión exitosa.

**Verificación:**
Si la conexión falla, asegúrate de que el servicio de MySQL local esté iniciado en el puerto 3306 y que el usuario especificado tenga los privilegios necesarios creados mediante:
```sql
CREATE DATABASE IF NOT EXISTS taskflow_db;
CREATE USER IF NOT EXISTS 'taskflow_user'@'localhost' IDENTIFIED BY 'TaskFlowSecure2026!';
GRANT ALL PRIVILEGES ON taskflow_db.* TO 'taskflow_user'@'localhost';
FLUSH PRIVILEGES;
```

---

### Paso 2: Instalar y Configurar Laravel Sanctum 5.0.0

**Objetivo:** Configurar el paquete de autenticación Laravel Sanctum 5.0.0, habilitar la generación de tokens de acceso rápido para la API y configurar el modelo de usuario para soportar la emisión de tokens.

**Instrucciones:**

1. Instala el paquete de Laravel Sanctum mediante Composer (si no está ya preinstalado):
   ```bash
   composer require laravel/sanctum:^5.0.0
   ```
2. Publica el archivo de configuración y las migraciones nativas de Sanctum:
   ```bash
   php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
   ```
3. Ejecuta la instalación del andamiaje de la API en Laravel. Este comando creará el archivo de configuración `routes/api.php` y habilitará el soporte de Sanctum:
   ```bash
   php artisan install:api
   ```
4. Abre el archivo del modelo de usuario `app/Models/User.php`. Asegúrate de que el trait `HasApiTokens` esté correctamente importado y utilizado en la definición de la clase:
   ```php
   <?php

   namespace App\Models;

   use Illuminate\Database\Eloquent\Factories\HasFactory;
   use Illuminate\Foundation\Auth\User as Authenticatable;
   use Illuminate\Notifications\Notifiable;
   use Laravel\Sanctum\HasApiTokens; // Asegurar esta importación

   class User extends Authenticatable
   {
       use HasApiTokens, HasFactory, Notifiable;

       protected $fillable = [
           'name',
           'email',
           'password',
       ];

       protected $hidden = [
           'password',
           'remember_token',
       ];

       protected $casts = [
           'email_verified_at' => 'datetime',
           'password' => 'hashed',
       ];
   }
   ```
5. Ejecuta las migraciones de la base de datos para crear la tabla de tokens personales (`personal_access_tokens`):
   ```bash
   php artisan migrate
   ```

**Resultado esperado:**
La terminal reflejará la creación de las tablas de la base de datos, en particular la tabla `personal_access_tokens`, que es donde Sanctum persistirá las claves criptográficas asociadas a cada sesión de usuario API.

**Verificación:**
Ejecuta el siguiente comando para comprobar que la tabla de tokens existe en la base de datos actual:
```bash
php artisan db:table personal_access_tokens
```
Deberías ver la estructura de la tabla con campos clave como `tokenable_id`, `tokenable_type`, `name`, `token`, `abilities` y `last_used_at`.

---

### Paso 3: Definir el Modelo y la Migración de la Entidad Task

**Objetivo:** Crear el modelo `Task` que representará las tareas gestionadas por la API, vinculándolas relacionalmente con el usuario creador.

**Instrucciones:**

1. Genera el modelo `Task` junto con su correspondiente archivo de migración:
   ```bash
   php artisan make:model Task -m
   ```
2. Abre el archivo de migración recién creado dentro del directorio `database/migrations/` y define el esquema de la tabla `tasks`:
   ```php
   <?php

   use Illuminate\Database\Migrations\Migration;
   use Illuminate\Database\Schema\Blueprint;
   use Illuminate\Support\Facades\Schema;

   return new class extends Migration
   {
       public function up(): void
       {
           Schema::create('tasks', function (Blueprint $table) {
               $table->id();
               $table->foreignId('user_id')->constrained()->onDelete('cascade');
               $table->string('title');
               $table->text('description')->nullable();
               $table->enum('status', ['pending', 'completed'])->default('pending');
               $table->timestamps();
           });
       }

       public function down(): void
       {
           Schema::dropIfExists('tasks');
       }
   };
   ```
3. Ejecuta la migración para actualizar la base de datos:
   ```bash
   php artisan migrate
   ```
4. Abre el archivo del modelo `app/Models/Task.php` y configura la asignación masiva de campos y su relación inversa con el modelo `User`:
   ```php
   <?php

   namespace App\Models;

   use Illuminate\Database\Eloquent\Factories\HasFactory;
   use Illuminate\Database\Eloquent\Model;
   use Illuminate\Database\Eloquent\Relations\BelongsTo;

   class Task extends Model
   {
       use HasFactory;

       protected $fillable = [
           'title',
           'description',
           'status',
           'user_id',
       ];

       protected $casts = [
           'status' => 'string',
       ];

       /**
        * Obtiene el usuario propietario de la tarea.
        */
       public function user(): BelongsTo
       {
           return $this->belongsTo(User::class);
       }
   }
   ```
5. Actualiza el modelo `User.php` para definir la relación de uno a muchos (`hasMany`) hacia el modelo `Task`:
   ```php
   /**
    * Obtiene las tareas asociadas al usuario.
    */
   public function tasks(): \Illuminate\Database\Eloquent\Relations\HasMany
   {
       return $this->hasMany(Task::class);
   }
   ```

**Resultado esperado:**
La migración se completará sin errores. En la base de datos, la tabla `tasks` quedará relacionada a la tabla `users` mediante una restricción de clave foránea en cascada.

**Verificación:**
Valida la integridad de la base de datos ejecutando el comando:
```bash
php artisan model:show Task
```
Este comando analizará el modelo Eloquent y mostrará la estructura de atributos y relaciones validadas por Laravel.

---

### Paso 4: Implementar los Controladores de API y Autenticación

**Objetivo:** Desarrollar los controladores encargados de emitir tokens de autenticación para los usuarios y de gestionar las operaciones CRUD de las tareas de forma aislada y segura para el usuario autenticado.

**Instrucciones:**

1. Crea un controlador dedicado a la autenticación por API:
   ```bash
   php artisan make:controller Api/AuthController
   ```
2. Abre `app/Http/Controllers/Api/AuthController.php` e implementa la lógica de inicio de sesión y registro de usuarios, retornando el token de acceso correspondiente:
   ```php
   <?php

   namespace App\Http/Controllers/Api;

   use App\Http\Controllers\Controller;
   use App\Models\User;
   use Illuminate\Http\Request;
   use Illuminate\Support\Facades\Hash;
   use Illuminate\Validation\ValidationException;

   class AuthController extends Controller
   {
       /**
        * Registrar un nuevo usuario y retornar un Bearer Token de acceso.
        */
       public function register(Request $request)
       {
           $request->validate([
               'name' => 'required|string|max:255',
               'email' => 'required|string|email|max:255|unique:users',
               'password' => 'required|string|min:8',
           ]);

           $user = User::create([
               'name' => $request->name,
               'email' => $request->email,
               'password' => Hash::make($request->password),
           ]);

           $token = $user->createToken('auth_token')->plainTextToken;

           return response()->json([
               'access_token' => $token,
               'token_type' => 'Bearer',
               'user' => [
                   'id' => $user->id,
                   'name' => $user->name,
                   'email' => $user->email,
               ]
           ], 201);
       }

       /**
        * Autenticar un usuario existente y retornar un Bearer Token.
        */
       public function login(Request $request)
       {
           $request->validate([
               'email' => 'required|email',
               'password' => 'required',
           ]);

           $user = User::where('email', $request->email)->first();

           if (!$user || !Hash::check($request->password, $user->password)) {
               throw ValidationException::withMessages([
                   'email' => ['Las credenciales proporcionadas son incorrectas.'],
               ]);
           }

           // Opcional: Revocar tokens anteriores para mantener un solo token activo
           $user->tokens()->delete();

           $token = $user->createToken('auth_token')->plainTextToken;

           return response()->json([
               'access_token' => $token,
               'token_type' => 'Bearer',
               'user' => [
                   'id' => $user->id,
                   'name' => $user->name,
                   'email' => $user->email,
               ]
           ], 200);
       }

       /**
        * Cerrar la sesión del usuario (revocar token actual).
        */
       public function logout(Request $request)
       {
           $request->user()->currentAccessToken()->delete();

           return response()->json([
               'message' => 'Sesión cerrada exitosamente y token revocado.'
           ], 200);
       }
   }
   ```
3. Crea un controlador de tipo recurso para la API de tareas:
   ```bash
   php artisan make:controller Api/TaskController --api
   ```
4. Abre `app/Http/Controllers/Api/TaskController.php` e implementa las acciones CRUD asegurando que un usuario solo pueda interactuar con sus propias tareas:
   ```php
   <?php

   namespace App\Http\Controllers\Api;

   use App\Http\Controllers\Controller;
   use App\Models\Task;
   use Illuminate\Http\Request;
   use Illuminate\Support\Facades\Validator;

   class TaskController extends Controller
   {
       /**
        * Listar las tareas asociadas al usuario autenticado.
        */
       public function index(Request $request)
       {
           $tasks = $request->user()->tasks()
               ->orderBy('created_at', 'desc')
               ->get();

           return response()->json([
               'success' => true,
               'data' => $tasks
           ], 200);
       }

       /**
        * Guardar una nueva tarea asociada al usuario autenticado.
        */
       public function store(Request $request)
       {
           // Validación explícita y manual para control fino sobre la respuesta JSON de error
           $validator = Validator::make($request->all(), [
               'title' => 'required|string|max:255',
               'description' => 'nullable|string',
               'status' => 'nullable|in:pending,completed',
           ]);

           if ($validator->fails()) {
               return response()->json([
                   'success' => false,
                   'errors' => $validator->errors()
               ], 422);
           }

           // Sanitización contra ataques XSS en campos de texto libre
           $cleanTitle = strip_tags($request->input('title'));
           $cleanDescription = $request->input('description') ? strip_tags($request->input('description')) : null;

           $task = $request->user()->tasks()->create([
               'title' => $cleanTitle,
               'description' => $cleanDescription,
               'status' => $request->input('status', 'pending'),
           ]);

           return response()->json([
               'success' => true,
               'message' => 'Tarea creada exitosamente.',
               'data' => $task
           ], 201);
       }

       /**
        * Mostrar de forma individual el detalle de una tarea del usuario autenticado.
        */
       public function show(Request $request, $id)
       {
           $task = $request->user()->tasks()->find($id);

           if (!$task) {
               return response()->json([
                   'success' => false,
                   'message' => 'Recurso no encontrado o sin permisos de acceso.'
               ], 404);
           }

           return response()->json([
               'success' => true,
               'data' => $task
           ], 200);
       }

       /**
        * Actualizar una tarea existente del usuario autenticado.
        */
       public function update(Request $request, $id)
       {
           $task = $request->user()->tasks()->find($id);

           if (!$task) {
               return response()->json([
                   'success' => false,
                   'message' => 'Recurso no encontrado o sin permisos de acceso.'
               ], 404);
           }

           $validator = Validator::make($request->all(), [
               'title' => 'sometimes|required|string|max:255',
               'description' => 'nullable|string',
               'status' => 'sometimes|required|in:pending,completed',
           ]);

           if ($validator->fails()) {
               return response()->json([
                   'success' => false,
                   'errors' => $validator->errors()
               ], 422);
           }

           // Sanitización contra ataques XSS
           $data = $request->only(['title', 'description', 'status']);
           if (isset($data['title'])) {
               $data['title'] = strip_tags($data['title']);
           }
           if (isset($data['description'])) {
               $data['description'] = strip_tags($data['description']);
           }

           $task->update($data);

           return response()->json([
               'success' => true,
               'message' => 'Tarea actualizada exitosamente.',
               'data' => $task
           ], 200);
       }

       /**
        * Eliminar una tarea del usuario autenticado.
        */
       public function destroy(Request $request, $id)
       {
           $task = $request->user()->tasks()->find($id);

           if (!$task) {
               return response()->json([
                   'success' => false,
                   'message' => 'Recurso no encontrado o sin permisos de acceso.'
               ], 404);
           }

           $task->delete();

           return response()->json([
               'success' => true,
               'message' => 'Tarea eliminada de forma permanente.'
           ], 200);
       }
   }
   ```

**Resultado esperado:**
Controladores robustos que separen los contextos de autenticación de la lógica de recursos y devuelvan estructuraciones de datos JSON estandarizadas según la acción HTTP empleada.

**Verificación:**
Asegúrate de que no haya errores de sintaxis PHP ejecutando una validación de sintaxis estática:
```bash
php -l app/Http/Controllers/Api/AuthController.php
php -l app/Http/Controllers/Api/TaskController.php
```
Ambos comandos deben retornar `No syntax errors detected`.

---

### Paso 5: Registrar las Rutas de la API y Habilitar Protección Sanctum

**Objetivo:** Configurar las rutas HTTP expuestas de la API para que las operaciones CRUD requieran autenticación obligatoria y los mecanismos de emisión de tokens permanezcan públicos.

**Instrucciones:**

1. Abre el archivo `routes/api.php`.
2. Define las rutas de la API, encapsulando las rutas de gestión de tareas dentro del middleware `auth:sanctum`:
   ```php
   <?php

   use App\Http\Controllers\Api\AuthController;
   use App\Http\Controllers\Api\TaskController;
   use Illuminate\Support\Facades\Route;

   /*
   |--------------------------------------------------------------------------
   | API Routes
   |--------------------------------------------------------------------------
   */

   // Rutas Públicas de Autenticación
   Route::post('/register', [AuthController::class, 'register']);
   Route::post('/login', [AuthController::class, 'login']);

   // Rutas Protegidas por Sanctum
   Route::middleware('auth:sanctum')->group(function () {
       // Cierre de sesión seguro
       Route::post('/logout', [AuthController::class, 'logout']);

       // Rutas CRUD de Tareas (Excluyendo métodos que no aplican a la API REST)
       Route::apiResource('tasks', TaskController::class);
   });
   ```

**Resultado esperado:**
Un árbol de enrutamiento limpio donde las peticiones que vayan a `/api/tasks` requieran de forma obligatoria la presencia de la cabecera HTTP `Authorization: Bearer <token>`.

**Verificación:**
Ejecuta el comando para listar todas las rutas registradas y comprobar los middlewares aplicados:
```bash
php artisan route:list --path=api
```
Deberías visualizar un listado similar al siguiente:
```text
+--------+----------+-------------------+---------+----------------------------------------------+--------------+
| Domain | Method   | URI               | Name    | Action                                       | Middleware   |
+--------+----------+-------------------+---------+----------------------------------------------+--------------+
|        | POST     | api/login         |         | App\Http\Controllers\Api\AuthController@login| api          |
|        | POST     | api/logout        |         | App\Http\Controllers\Api\AuthController@logo| api,auth:sanc|
|        | POST     | api/register      |         | App\Http\Controllers\Api\AuthContr@register | api          |
|        | GET|HEAD | api/tasks         | tasks.in| App\Http\Controllers\Api\TaskController@index| api,auth:sanc|
|        | POST     | api/tasks         | tasks.st| App\Http\Controllers\Api\TaskContr@store     | api,auth:sanc|
|        | GET|HEAD | api/tasks/{task}  | tasks.sh| App\Http\Controllers\Api\TaskController@show | api,auth:sanc|
|        | PUT|PATCH| api/tasks/{task}  | tasks.up| App\Http\Controllers\Api\TaskContr@update    | api,auth:sanc|
|        | DELETE   | api/tasks/{task}  | tasks.de| App\Http\Controllers\Api\TaskContr@destroy   | api,auth:sanc|
+--------+----------+-------------------+---------+----------------------------------------------+--------------+
```

---

### Paso 6: Configurar e Integrar Bruno Client 1.38.0 para Pruebas de API

**Objetivo:** Crear una suite de pruebas manuales y automatizables dentro de una colección en el software cliente Bruno v1.38.0, estableciendo variables globales y scripts para procesar dinámicamente el Bearer Token obtenido del endpoint de inicio de sesión.

**Instrucciones:**

1. Inicia la aplicación de escritorio **Bruno 1.38.0**.
2. Crea una nueva colección haciendo clic en **"Create Collection"**.
   - **Name:** `TaskFlow API`
   - **Location:** Selecciona la ruta de tu disco (se sugiere guardarla en un directorio limpio, ej. `~/labs/bruno-taskflow`).
3. En las propiedades de la colección, crea un entorno de variables (*Environment*) llamado `Local`:
   - Configura la variable `base_url` con el valor `http://localhost:8000/api`.
   - Configura la variable `token` (déjala vacía inicialmente).
4. **Petición 1: Registro de Usuario**
   - Crea un request de tipo **POST** llamado `Register`.
   - URL: `{{base_url}}/register`
   - En la pestaña **Body**, selecciona formato **JSON** y escribe:
     ```json
     {
       "name": "Alex Dev",
       "email": "alex.dev@taskflow.com",
       "password": "TaskFlowSecure2026!"
     }
     ```
5. **Petición 2: Login de Usuario e Inyección Automática de Token**
   - Crea un request de tipo **POST** llamado `Login`.
   - URL: `{{base_url}}/login`
   - Body (JSON):
     ```json
     {
       "email": "alex.dev@taskflow.com",
       "password": "TaskFlowSecure2026!"
     }
     ```
   - En la pestaña **Script** -> **Post-Response** de Bruno, escribe el siguiente código para guardar automáticamente el token de acceso obtenido en el entorno:
     ```javascript
     if (res.status === 200) {
       const token = res.body.access_token;
       bru.setVar("token", token);
     }
     ```
6. **Petición 3: Obtener Lista de Tareas**
   - Crea un request de tipo **GET** llamado `Get Tasks`.
   - URL: `{{base_url}}/tasks`
   - En la pestaña **Headers**, agrega una clave manual para autenticarte usando la variable dinámica guardada anteriormente:
     - Key: `Authorization`
     - Value: `Bearer {{token}}`
7. **Petición 4: Crear Tarea**
   - Crea un request de tipo **POST** llamado `Create Task`.
   - URL: `{{base_url}}/tasks`
   - Headers:
     - Key: `Authorization`, Value: `Bearer {{token}}`
   - Body (JSON):
     ```json
     {
       "title": "Configurar rutas de API Sanctum",
       "description": "Asegurar que todas las rutas CRUD usen el middleware auth:sanctum",
       "status": "pending"
     }
     ```

**Resultado esperado:**
La ejecución del endpoint de Login actualizará dinámicamente el valor de la variable de entorno `token` sin necesidad de copiar y pegar manualmente. Las peticiones subsiguientes (como listado o creación de tareas) se ejecutarán de forma exitosa retornando códigos `200 OK` y `201 Created` respectivamente.

**Verificación:**
Ejecuta la petición `Create Task`. Verifica que la respuesta devuelva un estado `201 Created` y una estructura JSON conteniendo el id generado y el `user_id` asociado a la cuenta de Alex Dev.

---

### Paso 7: Desarrollar la Interfaz de Usuario Interactiva (Fetch API + Tailwind CSS 4)

**Objetivo:** Crear una interfaz responsiva integrada en el proyecto mediante una plantilla Blade híbrida. La plantilla consumirá los endpoints JSON de forma asíncrona mediante JavaScript puro y actualizará visualmente el DOM dinámicamente.

**Instrucciones:**

1. Crea un controlador tradicional para renderizar la interfaz de usuario de API orientada al navegador:
   ```bash
   php artisan make:controller TaskViewController
   ```
2. Abre `app/Http/Controllers/TaskViewController.php` y retorna la vista:
   ```php
   <?php

   namespace App\Http\Controllers;

   use Illuminate\Http\Request;

   class TaskViewController extends Controller
   {
       public function index()
       {
           return view('api-tasks');
       }
   }
   ```
3. Registra la ruta web en `routes/web.php` para acceder a esta interfaz:
   ```php
   use App\Http\Controllers\TaskViewController;

   Route::get('/api-client', [TaskViewController::class, 'index'])->name('api.client');
   ```
4. Crea el archivo de la vista en `resources/views/api-tasks.blade.php` e introduce el siguiente código completo. Este utiliza clases estables de **Tailwind CSS 4.0.0-alpha.15** y gestiona el flujo de autenticación, obtención y creación de tareas asíncronas:
   ```html
   <!DOCTYPE html>
   <html lang="es" class="h-full bg-slate-900">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>TaskFlow - Cliente API Interno</title>
       <!-- Importación de Tailwind CSS v4 mediante CDN para asegurar estilos consistentes -->
       <script src="https://unpkg.com/@tailwindcss/browser@4"></script>
   </head>
   <body class="h-full text-slate-100 font-sans">

       <div class="min-h-full flex flex-col justify-center py-12 sm:px-6 lg:px-8">
           <div class="sm:mx-auto sm:w-full sm:max-w-md">
               <h2 class="text-center text-3xl font-extrabold tracking-tight text-emerald-400">
                   TaskFlow API Hub
               </h2>
               <p class="mt-2 text-center text-sm text-slate-400">
                   Cliente dinámico conectado a Sanctum 5.0.0
               </p>
           </div>

           <!-- Panel de Autenticación / Login -->
           <div id="auth-panel" class="mt-8 sm:mx-auto sm:w-full sm:max-w-md">
               <div class="bg-slate-800 py-8 px-4 shadow-xl rounded-lg sm:px-10 border border-slate-700">
                   <h3 class="text-lg font-medium text-slate-200 mb-4 border-b border-slate-700 pb-2">Iniciar Sesión API</h3>
                   <div class="space-y-4">
                       <div>
                           <label class="block text-sm font-medium text-slate-300">Correo Electrónico</label>
                           <input id="login-email" type="email" value="alex.dev@taskflow.com" class="mt-1 block w-full rounded-md bg-slate-900 border border-slate-700 text-white px-3 py-2 focus:outline-none focus:border-emerald-500">
                       </div>
                       <div>
                           <label class="block text-sm font-medium text-slate-300">Contraseña</label>
                           <input id="login-password" type="password" value="TaskFlowSecure2026!" class="mt-1 block w-full rounded-md bg-slate-900 border border-slate-700 text-white px-3 py-2 focus:outline-none focus:border-emerald-500">
                       </div>
                       <button id="btn-login" class="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-slate-900 bg-emerald-400 hover:bg-emerald-300 focus:outline-none transition-colors">
                           Autenticar y Guardar Token
                       </button>
                       <div id="auth-error" class="hidden text-sm text-red-400 mt-2 bg-red-950/50 p-2 rounded border border-red-800"></div>
                   </div>
               </div>
           </div>

           <!-- Panel de Tareas (Oculto hasta autenticarse) -->
           <div id="tasks-panel" class="hidden mt-8 max-w-4xl mx-auto w-full px-4 sm:px-6">
               <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                   <!-- Formulario de Creación -->
                   <div class="bg-slate-800 p-6 rounded-lg border border-slate-700 h-fit">
                       <h3 class="text-lg font-medium text-emerald-400 mb-4">Nueva Tarea</h3>
                       <div class="space-y-4">
                           <div>
                               <label class="block text-sm font-medium text-slate-300">Título</label>
                               <input id="task-title" type="text" class="mt-1 block w-full rounded-md bg-slate-900 border border-slate-700 text-white px-3 py-2 focus:outline-none focus:border-emerald-500">
                           </div>
                           <div>
                               <label class="block text-sm font-medium text-slate-300">Descripción</label>
                               <textarea id="task-desc" rows="3" class="mt-1 block w-full rounded-md bg-slate-900 border border-slate-700 text-white px-3 py-2 focus:outline-none focus:border-emerald-500"></textarea>
                           </div>
                           <button id="btn-save-task" class="w-full py-2 px-4 rounded bg-emerald-500 text-slate-950 font-bold hover:bg-emerald-400 transition-colors">
                               Guardar Tarea
                           </button>
                       </div>
                   </div>

                   <!-- Listado Dinámico -->
                   <div class="bg-slate-800 p-6 rounded-lg border border-slate-700 md:col-span-2">
                       <div class="flex justify-between items-center mb-4">
                           <h3 class="text-lg font-medium text-slate-200">Mis Tareas</h3>
                           <button id="btn-logout" class="text-xs text-red-400 hover:underline">
                               Cerrar Sesión API
                           </button>
                       </div>
                       <div id="tasks-container" class="space-y-3">
                           <!-- Carga dinámica -->
                           <p class="text-slate-500 text-sm italic">Cargando tareas...</p>
                       </div>
                   </div>
               </div>
           </div>
       </div>

       <!-- Script de Manipulación DOM y Fetch RESTful -->
       <script>
           document.addEventListener('DOMContentLoaded', () => {
               const authPanel = document.getElementById('auth-panel');
               const tasksPanel = document.getElementById('tasks-panel');
               const btnLogin = document.getElementById('btn-login');
               const btnLogout = document.getElementById('btn-logout');
               const btnSaveTask = document.getElementById('btn-save-task');
               const tasksContainer = document.getElementById('tasks-container');
               const authError = document.getElementById('auth-error');

               let apiToken = localStorage.getItem('api_token') || null;

               // Comprobación de estado de autenticación inicial
               if (apiToken) {
                   showTasksDashboard();
               }

               // Acción de Inicio de Sesión
               btnLogin.addEventListener('click', async () => {
                   const email = document.getElementById('login-email').value;
                   const password = document.getElementById('login-password').value;
                   authError.classList.add('hidden');

                   try {
                       const response = await fetch('/api/login', {
                           method: 'POST',
                           headers: {
                               'Content-Type': 'application/json',
                               'Accept': 'application/json'
                           },
                           body: JSON.stringify({ email, password })
                       });

                       const result = await response.json();

                       if (!response.ok) {
                           throw new Error(result.message || 'Error en la autenticación.');
                       }

                       apiToken = result.access_token;
                       localStorage.setItem('api_token', apiToken);
                       showTasksDashboard();
                   } catch (error) {
                       authError.textContent = error.message;
                       authError.classList.remove('hidden');
                   }
               });

               // Acción de Cierre de Sesión
               btnLogout.addEventListener('click', async () => {
                   try {
                       await fetch('/api/logout', {
                           method: 'POST',
                           headers: {
                               'Authorization': `Bearer ${apiToken}`,
                               'Accept': 'application/json'
                           }
                       });
                   } catch (err) {
                       console.warn("Fallo al revocar token de forma remota: ", err);
                   } finally {
                       localStorage.removeItem('api_token');
                       apiToken = null;
                       showAuthPanel();
                   }
               });

               // Guardar Tarea por API
               btnSaveTask.addEventListener('click', async () => {
                   const title = document.getElementById('task-title').value;
                   const description = document.getElementById('task-desc').value;

                   if (!title.trim()) {
                       alert("El título de la tarea es obligatorio.");
                       return;
                   }

                   try {
                       const response = await fetch('/api/tasks', {
                           method: 'POST',
                           headers: {
                               'Content-Type': 'application/json',
                               'Authorization': `Bearer ${apiToken}`,
                               'Accept': 'application/json'
                           },
                           body: JSON.stringify({ title, description })
                       });

                       const result = await response.json();

                       if (response.ok) {
                           document.getElementById('task-title').value = '';
                           document.getElementById('task-desc').value = '';
                           fetchTasks();
                       } else {
                           alert(result.message || "Error al registrar la tarea.");
                       }
                   } catch (error) {
                       console.error("Error al guardar:", error);
                   }
               });

               // Cambiar paneles visibles
               function showTasksDashboard() {
                   authPanel.classList.add('hidden');
                   tasksPanel.classList.remove('hidden');
                   fetchTasks();
               }

               function showAuthPanel() {
                   tasksPanel.classList.add('hidden');
                   authPanel.classList.remove('hidden');
                   tasksContainer.innerHTML = '';
               }

               // Consumir el listado de Tareas mediante GET
               async function fetchTasks() {
                   try {
                       const response = await fetch('/api/tasks', {
                           method: 'GET',
                           headers: {
                               'Authorization': `Bearer ${apiToken}`,
                               'Accept': 'application/json'
                           }
                       });

                       if (response.status === 401) {
                           // Token expirado o revocado
                           localStorage.removeItem('api_token');
                           showAuthPanel();
                           return;
                       }

                       const json = await response.json();
                       renderTasks(json.data);
                   } catch (error) {
                       tasksContainer.innerHTML = `<p class="text-red-400">Error de conexión con la API.</p>`;
                   }
               }

               // Dibujar las tareas con soporte asíncrono para eliminar y marcar completado
               function renderTasks(tasks) {
                   if (!tasks || tasks.length === 0) {
                       tasksContainer.innerHTML = `<p class="text-slate-500 text-sm italic">No se encontraron tareas pendientes.</p>`;
                       return;
                   }

                   tasksContainer.innerHTML = '';
                   tasks.forEach(task => {
                       const div = document.createElement('div');
                       div.className = 'flex items-center justify-between p-3 rounded-md bg-slate-900 border border-slate-700 hover:border-emerald-500 transition-colors';
                       div.innerHTML = `
                           <div>
                               <h4 class="font-bold ${task.status === 'completed' ? 'line-through text-slate-500' : 'text-slate-200'}">${escapeHTML(task.title)}</h4>
                               <p class="text-xs text-slate-400 mt-1">${task.description ? escapeHTML(task.description) : 'Sin descripción'}</p>
                           </div>
                           <div class="flex items-center gap-2">
                               <button onclick="toggleTaskStatus(${task.id}, '${task.status === 'completed' ? 'pending' : 'completed'}')" class="px-2 py-1 text-xs rounded ${task.status === 'completed' ? 'bg-amber-600 text-white' : 'bg-emerald-600 text-white'}">
                                   ${task.status === 'completed' ? 'Pendiente' : 'Completar'}
                               </button>
                               <button onclick="deleteTask(${task.id})" class="px-2 py-1 text-xs rounded bg-red-600 text-white hover:bg-red-500">
                                   Eliminar
                               </button>
                           </div>
                       `;
                       tasksContainer.appendChild(div);
                   });
               }

               // Función auxiliar para escapar código html y prevenir XSS desde el cliente
               function escapeHTML(str) {
                   return str.replace(/[&<>'"]/g, 
                       tag => ({ '&': '&amp;', '<': '&lt;', '>': '&gt;', "'": '&#39;', '"': '&quot;' }[tag] || tag)
                   );
               }

               // Funciones expuestas globalmente para controladores de eventos inline
               window.toggleTaskStatus = async (id, status) => {
                   try {
                       const response = await fetch(`/api/tasks/${id}`, {
                           method: 'PUT',
                           headers: {
                               'Content-Type': 'application/json',
                               'Authorization': `Bearer ${apiToken}`,
                               'Accept': 'application/json'
                           },
                           body: JSON.stringify({ status })
                       });
                       if (response.ok) fetchTasks();
                   } catch (err) {
                       console.error(err);
                   }
               };

               window.deleteTask = async (id) => {
                   if (!confirm("¿Deseas eliminar permanentemente esta tarea?")) return;
                   try {
                       const response = await fetch(`/api/tasks/${id}`, {
                           method: 'DELETE',
                           headers: {
                               'Authorization': `Bearer ${apiToken}`,
                               'Accept': 'application/json'
                           }
                       });
                       if (response.ok) fetchTasks();
                   } catch (err) {
                       console.error(err);
                   }
               };
           });
       </script>
   </body>
   </html>
   ```

**Resultado esperado:**
Una página web completamente operativa e interactiva que permite a los usuarios iniciar sesión de manera asíncrona, recibir y guardar el token criptográfico emitido por Laravel Sanctum, cargar su listado personalizado de tareas, crear nuevos ítems de manera dinámica y actualizarlos o borrarlos sin necesidad de refrescar la ventana del navegador.

**Verificación:**
Levanta el servidor integrado de desarrollo de Laravel en tu consola principal:
```bash
php artisan serve
```
Abre un navegador e ingresa a `http://localhost:8000/api-client`. Asegúrate de que al hacer clic en **"Autenticar y Guardar Token"** cambie el flujo del panel cargando las tareas previas registradas.

---

### Paso 8: Escribir y Ejecutar Pruebas de Integración Automatizadas con Pest

**Objetivo:** Desarrollar casos de prueba automatizados para validar que las peticiones a la API requieran autenticación estricta, bloqueen accesos no autorizados y garanticen que la creación de tareas asocie correctamente el ID del usuario actual.

**Instrucciones:**

1. Crea un nuevo archivo de pruebas unitarias para la API de tareas:
   ```bash
   php artisan make:test Api/TaskTest --pest
   ```
2. Abre el archivo de prueba generado en `tests/Feature/Api/TaskTest.php` e implementa la validación de los escenarios críticos de seguridad y funcionalidad:
   ```php
   <?php

   use App\Models\User;
   use App\Models\Task;
   use Illuminate\Foundation\Testing\RefreshDatabase;

   uses(RefreshDatabase::class);

   test('un usuario no autenticado no puede listar tareas y recibe un codigo 401', function () {
       $response = $this->getJson('/api/tasks');

       $response->assertStatus(401);
   });

   test('un usuario autenticado puede listar solo sus propias tareas creadas', function () {
       // Crear dos usuarios separados en base de datos
       $userOne = User::factory()->create();
       $userTwo = User::factory()->create();

       // Generar tareas asociadas a cada usuario
       Task::factory()->create([
           'title' => 'Tarea de Usuario Uno',
           'user_id' => $userOne->id
       ]);

       Task::factory()->create([
           'title' => 'Tarea de Usuario Dos',
           'user_id' => $userTwo->id
       ]);

       // Simular autenticación del primer usuario mediante Sanctum
       $response = $this->actingAs($userOne, 'sanctum')
           ->getJson('/api/tasks');

       $response->assertStatus(200)
           ->assertJsonCount(1, 'data')
           ->assertJsonPath('data.0.title', 'Tarea de Usuario Uno');
   });

   test('un usuario puede registrar una nueva tarea de manera exitosa a traves de la API', function () {
       $user = User::factory()->create();

       $payload = [
           'title' => 'Implementar pruebas Pest',
           'description' => 'Validar endpoints REST con assertions de Pest y PHPUnit',
           'status' => 'pending'
       ];

       $response = $this->actingAs($user, 'sanctum')
           ->postJson('/api/tasks', $payload);

       $response->assertStatus(201)
           ->assertJsonStructure([
               'success',
               'message',
               'data' => [
                   'id',
                   'title',
                   'description',
                   'status',
                   'user_id'
               ]
           ]);

       $this->assertDatabaseHas('tasks', [
           'title' => 'Implementar pruebas Pest',
           'user_id' => $user->id
       ]);
   });

   test('el sistema rechaza la creacion de tareas con payload incompleto u omision del titulo', function () {
       $user = User::factory()->create();

       $payload = [
           'description' => 'Falta ingresar el titulo',
       ];

       $response = $this->actingAs($user, 'sanctum')
           ->postJson('/api/tasks', $payload);

       $response->assertStatus(422)
           ->assertJsonValidationErrors(['title']);
   });
   ```
3. Ejecuta el motor de pruebas de Laravel para validar el cumplimiento de todos los casos implementados:
   ```bash
   php artisan test
   ```

**Resultado esperado:**
Pest ejecutará las pruebas en un entorno aislado de base de datos (`RefreshDatabase`). El indicador visual de la consola debe mostrar que las 4 pruebas de integración de la API han pasado con éxito.

**Verificación:**
Deberías ver una salida en consola con formato de aprobación:
```text
  PASS  Tests\Feature\Api\TaskTest
  ✓ un usuario no autenticado no puede listar tareas y recibe un codigo 401
  ✓ un usuario autenticado puede listar solo sus propias tareas creadas
  ✓ un usuario puede registrar una nueva tarea de manera exitosa a traves de la API
  ✓ el sistema rechaza la creacion de tareas con payload incompleto u omision del titulo

  Tests:    4 passed (4 assertions)
  Duration: 0.24s
```

---

## Validación y Pruebas

Para garantizar que tu implementación sea robusta frente a fallos y ataques, ejecuta estas tres validaciones de seguridad avanzadas:

### Caso 1: Validación de Bloqueo de Token Inexistente o Expirado (Escenario Adversario)
Utilizaremos `curl` para interactuar con la API enviando credenciales corruptas para confirmar la ausencia de fugas de información.

**Comando de Prueba:**
```bash
curl -i -X GET http://localhost:8000/api/tasks \
  -H "Authorization: Bearer TokenTotalmenteInvalidoOExpirado" \
  -H "Accept: application/json"
```

**Resultado Esperado en Consola:**
```http
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
    "message": "Unauthenticated."
}
```

### Caso 2: Intento de Inyección de Código Malicioso XSS en Campos de Texto (Escenario Adversario)
Enviaremos una payload de datos que intente inyectar scripts JavaScript maliciosos dentro de un campo de creación para comprobar el comportamiento de nuestros filtros de sanitización.

**Comando de Prueba:**
```bash
## Nota: Primero inicia sesión a través de tu navegador o Bruno para obtener un Bearer token válido y sustitúyelo abajo:
curl -i -X POST http://localhost:8000/api/tasks \
  -H "Authorization: Bearer <TOKEN_VALIDO_AQUÍ>" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"title": "<script>alert(\"XSS Attack\")</script>Título Sanitizado", "description": "Lógica de prueba de inyección"}'
```

**Resultado Esperado en Consola:**
La API responderá con un código HTTP `201 Created`. Al inspeccionar el valor de la tarea devuelta o guardada en la base de datos, las etiquetas `<script>` se habrán eliminado por completo gracias al filtro de sanitización `strip_tags()` que implementamos en el controlador:
```json
{
    "success": true,
    "message": "Tarea creada exitosamente.",
    "data": {
        "title": "Título Sanitizado",
        "description": "Lógica de prueba de inyección",
        "status": "pending",
        "user_id": 1
    }
}
```

---

## Solución de Problemas

A continuación se listan dos de los problemas más comunes identificados durante la configuración de Laravel Sanctum junto con sus diagnósticos y soluciones paso a paso:

### Problema 1: Las peticiones a la API retornan un bucle de redirección a la ruta de inicio de sesión (`login`) con un error de respuesta HTML de Laravel en lugar de una respuesta JSON limpia.

- **Síntoma:** Cuando ejecutas pruebas en Bruno o realizas peticiones sin cabecera de autenticación, la consola del cliente arroja un código de estado `302 Found` o `200 OK` retornando una vista de formulario HTML tradicional (Blade de login), en lugar de retornar la estructura de respuesta de error JSON `401 Unauthenticated`.
- **Causa Raíz:** Este comportamiento ocurre porque el cliente de API no está enviando la cabecera `Accept: application/json`. Cuando ocurre un fallo de autenticación en Laravel, el framework verifica si el cliente acepta contenido JSON; si esa cabecera falta, asume que es un navegador web interactivo tradicional y redirige al usuario a la página visual de inicio de sesión por defecto de la aplicación web.
- **Resolución:**
  1. En tu cliente API Bruno (o llamadas Fetch de JavaScript), dirígete a la sección de **Headers**.
  2. Añade un nuevo encabezado explícito:
     - Clave: `Accept`
     - Valor: `application/json`
  3. Vuelve a lanzar la petición sin token de autenticación. Ahora, el middleware de Sanctum detectará la cabecera correctamente y enviará de vuelta una respuesta JSON nativa con el código `401 Unauthorized` esperado.

---

### Problema 2: Error de CORS (Cross-Origin Resource Sharing) en la consola del navegador al intentar consumir la API desde una URL de origen cruzado o puerto distinto (ej. Vite en el puerto 5173 hacia Laravel en el puerto 8000).

- **Síntoma:** En la consola de desarrollo del navegador visualizas un mensaje con el prefijo: *`Access to fetch at 'http://localhost:8000/api/tasks' from origin 'http://localhost:5173' has been blocked by CORS policy`*.
- **Causa Raíz:** Por cuestiones de seguridad, los navegadores impiden que scripts de JS consuman APIs web alojadas en un dominio, puerto o protocolo diferente al que sirve la página, a menos que el servidor de la API responda explícitamente enviando cabeceras CORS que autoricen dicho origen externo.
- **Resolución:**
  1. Abre el archivo de configuración global de CORS de Laravel ubicado en `config/cors.php`. (Si no existiese, puedes publicarlo ejecutando `php artisan config:publish cors`).
  2. Localiza el arreglo `allowed_origins` y edítalo para agregar explícitamente el origen desde el cual corre tu servidor Vite de desarrollo:
     ```php
     'allowed_origins' => [
         'http://localhost:5173',
         'http://127.0.0.1:5173'
     ],
     ```
  3. Asegúrate de que `supports_credentials` esté configurado en `true` en el mismo archivo para permitir el intercambio asíncrono de cookies de autenticación o tokens en caso de que utilices autenticación con estado.
  4. Limpia la caché de configuración para aplicar los cambios de inmediato:
     ```bash
     php artisan config:clear
     ```

---

## Limpieza

Para restaurar las configuraciones iniciales del laboratorio y asegurar un historial de Git ordenado de cara a los entregables de evaluación, ejecuta la siguiente rutina de limpieza:

1. Finaliza cualquier proceso activo en primer plano (servidores integrados de Laravel y Vite en ejecución) presionando `Ctrl + C` en todas tus terminales abiertas.
2. Agrega los archivos modificados a tu área de preparación (*staging*):
   ```bash
   git add .
   ```
3. Registra un commit descriptivo que marque la finalización exitosa del laboratorio actual:
   ```bash
   git commit -m "feat: implementar y asegurar API REST con Sanctum 5.0.0 y cliente interactivo Fetch"
   ```
4. Si necesitas volver a la rama principal por defecto de tu espacio de trabajo:
   ```bash
   git checkout main
   ```

---

## Resumen

### Puntos Clave Aprendidos

- **Laravel Sanctum 5.0.0** destaca como una herramienta extremadamente ligera y eficiente para habilitar la emisión de tokens rápidos de acceso a APIs RESTful sin la complejidad de protocolos pesados como OAuth2.
- Una API REST robusta debe validar y tipar estrictamente sus payloads de entrada, aplicar técnicas de **sanitización activa (XSS)** antes de registrar cadenas de texto en bases de datos y responder con los códigos de estado HTTP semánticamente correctos (`200`, `201`, `401`, `422`, `404`).
- Al consumir recursos externos asíncronamente con la **API Fetch**, es vital gestionar la persistencia segura de los Bearer tokens (ej. `localStorage` o cookies) y controlar los flujos de redirección del DOM ante respuestas de fallo de autorización (`401`).
- Los clientes API modernos como **Bruno v1.38.0** permiten desacoplar el desarrollo backend del frontend mediante suites de prueba manuales y scripting inteligente para agilizar y documentar el flujo de trabajo en equipo.

### Recursos de Aprendizaje Adicionales

- [Documentación Oficial de Laravel Sanctum (v11.x / 13.x)](https://laravel.com/docs/sanctum)
- [Estándar y Buenas Prácticas sobre API RESTful - REST API Tutorial](https://restfulapi.net/)
- [Guía de la API Fetch en Mozilla Developer Network (MDN)](https://developer.mozilla.org/es/docs/Web/API/Fetch_API)
- [Documentación de scripting avanzada en Bruno Client](https://docs.usebruno.com/)
