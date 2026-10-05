# 7 Práctica: Fundamentos de Laravel 13.x y desarrollo full-stack con PHP 8.5.x, Composer 2.10.3+, Node.js 24.21.0 LTS, npm 11.x, Vite y Tailwind CSS 4.x

## Metadatos

| Atributo | Detalle |
| :--- | :--- |
| **Duración** | 180 minutos |
| **Complejidad** | Fácil |
| **Nivel de Bloom** | Crear |

---

## Descripción General

En este laboratorio práctico, el estudiante inicializará desde cero la estructura del proyecto web de pila completa (full-stack) denominado **TaskFlow**. Se empleará el gestor de dependencias Composer para estructurar la base del backend sobre el framework Laravel 13.0.0 en conjunto con PHP 8.5.0. 

Para la interfaz de usuario, se configurará la herramienta de compilación y empaquetado Vite 6.0.5 junto con el nuevo motor de estilos CSS Tailwind CSS 4.0.0. Al finalizar este laboratorio, el estudiante habrá establecido un flujo de enrutamiento web dinámico que conecta la lógica de un controlador (`TaskViewController`) con vistas modulares estructuradas mediante plantillas y componentes reutilizables de Blade. Este proyecto servirá como base incremental para el desarrollo de las arquitecturas híbridas y los mecanismos de seguridad que se verán en las sesiones posteriores.

---

## Objetivos de Aprendizaje

Al finalizar este laboratorio, serás capaz de:
- [ ] Inicializar un proyecto estructurado en Laravel 13.0.0 y configurar su entorno de ejecución local (`.env`) y control de versiones con Git.
- [ ] Configurar el pipeline de compilación de assets de frontend utilizando Vite 6.0.5 y la arquitectura nativa de plugins de Tailwind CSS 4.0.0.
- [ ] Crear rutas web semánticas y asociarlas a un controlador dedicado para gestionar la transferencia de colecciones de datos estructurados.
- [ ] Diseñar una interfaz de usuario limpia y responsiva empleando layouts maestros de Blade y componentes parametrizables de frontend.
- [ ] Validar la integridad del enrutamiento y la renderización visual mediante pruebas automatizadas con PHPUnit/Pest.

---

## Prerrequisitos

Para completar este laboratorio con éxito, se requiere:
1. **Conocimientos Previos**:
   - Comprensión intermedia de PHP (sintaxis estructurada, arreglos asociativos y programación orientada a objetos).
   - Dominio básico de comandos de terminal Unix/Linux (gestión de directorios y procesos).
   - Fundamentos de maquetado HTML5, CSS3 moderno y enrutamiento en aplicaciones web.

2. **Herramientas de Asistencia y Licenciamiento**:
   - En caso de utilizar herramientas de asistencia de desarrollo basadas en IA durante la práctica:
     * **GitHub Copilot / Copilot Chat**: Requiere una licencia activa corporativa o individual. Se debe configurar el archivo `.copilotignore` en la raíz del proyecto para evitar la indexación innecesaria del directorio `vendor/` o `node_modules/`, optimizando la precisión de las sugerencias.
     * **Microsoft 365 Copilot**: No requerido para la codificación directa, pero puede utilizarse para estructurar la documentación técnica del entregable bajo licencia comercial institucional.

---

## Entorno de Laboratorio

El laboratorio debe ser ejecutado en una estación de trabajo que cumpla con las especificaciones de hardware y software listadas a continuación.

### Requisitos de Hardware
- **Procesador**: Arquitectura x86_64 o ARM64 (Apple Silicon) con un mínimo de 4 núcleos físicos.
- **Memoria RAM**: Mínimo de 8 GB (Recomendado 16 GB para garantizar la ejecución paralela del servidor de desarrollo de Laravel y el compilador de Vite).
- **Almacenamiento**: Unidad de Estado Sólido (SSD) con al menos 20 GB de espacio libre en disco.

### Requisitos de Software

| Software | Versión Requerida | URL de Descarga Oficial |
| :--- | :--- | :--- |
| **PHP (CLI)** | 8.5.0 | [https://www.php.net/downloads](https://www.php.net/downloads) |
| **Composer** | 2.10.3 | [https://getcomposer.org/download/](https://getcomposer.org/download/) |
| **Node.js** | 24.21.0 LTS | [https://nodejs.org/](https://nodejs.org/) |
| **npm** | 11.1.0 | [https://www.npmjs.com/](https://www.npmjs.com/) |
| **MySQL Community Server** | 8.4.0 | [https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/) |
| **SQLite (CLI)** | 3.45.3 | [https://www.sqlite.org/download.html](https://www.sqlite.org/download.html) |
| **Git** | 2.43.0+ | [https://git-scm.com/downloads](https://git-scm.com/downloads) |

### Parámetros Globales del Proyecto
- **Directorio de trabajo**: `~/labs/taskflow-app`
- **Puerto de desarrollo Laravel**: HTTP `8000` (`http://localhost:8000`)
- **Puerto de desarrollo Vite**: HTTP `5173` (`http://localhost:5173`)
- **Puerto de base de datos MySQL**: TCP `3306`
- **Constantes de Entorno Predefinidas**:
  - `DB_DATABASE`: `taskflow_db`
  - `DB_USERNAME`: `taskflow_user`
  - `DB_PASSWORD`: `TaskFlowSecure2026!`

---

## Instrucciones Paso a Paso

### Paso 1: Inicialización del Proyecto Laravel 13.0.0 y Configuración de Git

**Objetivo**: Crear la estructura física de la aplicación empleando Composer, inicializar el control de versiones local de Git bajo una rama de desarrollo estandarizada y ajustar los parámetros del entorno de desarrollo.

**Instrucciones**:

1. Abra una ventana de terminal y navegue al directorio donde alojará sus proyectos de laboratorio. Si no existe la carpeta `~/labs`, créela:
   ```bash
   mkdir -p ~/labs
   cd ~/labs
   ```

2. Ejecute el comando de Composer para descargar el instalador e inicializar un nuevo proyecto limpio de Laravel 13.0.0 bajo el directorio `taskflow-app`:
   ```bash
   composer create-project laravel/laravel:^13.0 taskflow-app
   ```

3. Una vez finalizada la instalación de dependencias de PHP, ingrese al directorio del proyecto:
   ```bash
   cd taskflow-app
   ```

4. Inicialice un repositorio Git local, configure la identidad del autor si es la primera vez y cree una rama independiente llamada `lab-01-fundamentos`:
   ```bash
   git init
   git branch -M main
   git checkout -b lab-01-fundamentos
   ```

5. Copie el archivo de configuración de entorno para preparar la persistencia y la configuración del servidor:
   ```bash
   cp .env.example .env
   php artisan key:generate
   ```

6. Abra el archivo `.env` en su editor de código preferido y actualice las variables de conexión a la base de datos para alinearlas con las constantes del curso:
   ```env
   DB_CONNECTION=mysql
   DB_HOST=127.0.0.1
   DB_PORT=3306
   DB_DATABASE=taskflow_db
   DB_USERNAME=taskflow_user
   DB_PASSWORD=TaskFlowSecure2026!
   ```

**Resultado Esperado**:
Un directorio estructurado de Laravel con un repositorio Git local en la rama `lab-01-fundamentos` y un archivo `.env` configurado con las credenciales de base de datos relacionales estipuladas.

**Verificación**:
Ejecute el siguiente comando para comprobar el estado de Git y la versión instalada de Laravel:
```bash
git status
php artisan --version
```
La terminal debe retornar que se encuentra en la rama `lab-01-fundamentos` y que la versión del framework corresponde a `Laravel Framework 13.0.0` (o la subversión de parche disponible más reciente de la rama 13).

---

### Paso 2: Integración y Configuración de Vite 6.0.5 y Tailwind CSS 4.0.0

**Objetivo**: Instalar, configurar e integrar Tailwind CSS versión 4 en la canalización de compilación de frontend de Vite. Esta versión de Tailwind CSS elimina el uso del archivo de configuración tradicional (`tailwind.config.js`) y se apoya directamente en un plugin nativo de Vite y en directivas de importación CSS.

**Instrucciones**:

1. Instale las dependencias de Node.js necesarias en el proyecto, incluyendo las versiones exactas de Tailwind CSS 4.0.0 y su plugin oficial para Vite:
   ```bash
   npm install --save-dev tailwindcss@4.0.0 @tailwindcss/vite@4.0.0
   ```

2. Abra el archivo de configuración de Vite `vite.config.js` en la raíz del proyecto. Reemplace su contenido para importar e inicializar el plugin de Tailwind CSS 4.0.0:
   ```javascript
   // filepath: ~/labs/taskflow-app/vite.config.js
   import { defineConfig } from 'vite';
   import laravel from 'laravel-vite-plugin';
   import tailwindcss from '@tailwindcss/vite';

   export default defineConfig({
       plugins: [
           laravel({
               input: ['resources/css/app.css', 'resources/js/app.js'],
               refresh: true,
           }),
           tailwindcss(),
       ],
       server: {
           host: 'localhost',
           port: 5173,
       }
   });
   ```

3. Abra el archivo CSS principal de la aplicación ubicado en `resources/css/app.css` y reemplace todas las líneas existentes por la directiva de importación directa de Tailwind v4:
   ```css
   /* filepath: ~/labs/taskflow-app/resources/css/app.css */
   @import "tailwindcss";
   ```

4. Verifique que la sección de scripts del archivo `package.json` contenga las tareas de desarrollo y compilación de Vite:
   ```json
   "scripts": {
       "dev": "vite",
       "build": "vite build"
   }
   ```

**Resultado Esperado**:
El motor de compilación Vite configurado para procesar estilos de Tailwind CSS 4 sin necesidad de archivos de configuración pesados independientes de JavaScript.

**Verificación**:
Ejecute el compilador en modo de desarrollo:
```bash
npm run dev
```
La terminal debe indicar que el servidor de desarrollo de Vite está escuchando activamente en `http://localhost:5173/` y que ha cargado de manera correcta el archivo `resources/css/app.css`. Detenga el proceso temporalmente con `Ctrl + C` para continuar los pasos.

---

### Paso 3: Creación de Rutas y el Controlador TaskViewController

**Objetivo**: Generar un controlador de Laravel que retorne datos estructurados hacia una vista HTML, y registrar las rutas web correspondientes en la aplicación.

**Instrucciones**:

1. Genere un controlador vacío con nombre `TaskViewController` utilizando el comando Artisan CLI:
   ```bash
   php artisan make:controller TaskViewController
   ```

2. Abra el archivo del controlador recién creado en `app/Http/Controllers/TaskViewController.php`. Implemente el método `index()` para simular la recuperación de tareas desde la base de datos a través de una colección estática estructurada en un arreglo asociativo:
   ```php
   <?php
   // filepath: ~/labs/taskflow-app/app/Http/Controllers/TaskViewController.php

   namespace App\Http\Controllers;

   use Illuminate\Http\Request;

   class TaskViewController extends Controller
   {
       /**
        * Muestra la lista principal de tareas de la aplicación.
        */
       public function index()
       {
           // Estructura simulada de tareas obtenidas de la persistencia
           $tasks = [
               [
                   'id' => 1,
                   'title' => 'Configurar Entorno de Desarrollo',
                   'description' => 'Instalar PHP 8.5.0, Composer 2.10.3 y Node.js para arrancar el proyecto.',
                   'priority' => 'Alta',
                   'status' => 'Completada',
                   'due_date' => '2026-03-01'
               ],
               [
                   'id' => 2,
                   'title' => 'Integrar Tailwind CSS 4.0.0',
                   'description' => 'Configurar el plugin nativo de Vite 6 para compilar estilos modernos.',
                   'priority' => 'Alta',
                   'status' => 'En Progreso',
                   'due_date' => '2026-03-05'
               ],
               [
                   'id' => 3,
                   'title' => 'Crear Componentes de Blade',
                   'description' => 'Desarrollar componentes reutilizables para tarjetas y botones con diseño limpio.',
                   'priority' => 'Media',
                   'status' => 'Pendiente',
                   'due_date' => '2026-03-10'
               ],
               [
                   'id' => 4,
                   'title' => 'Implementar Pruebas con Pest',
                   'description' => 'Validar el renderizado correcto de la vista y la respuesta del servidor.',
                   'priority' => 'Baja',
                   'status' => 'Pendiente',
                   'due_date' => '2026-03-15'
               ]
           ];

           return view('tasks.index', compact('tasks'));
       }
   }
   ```

3. Abra el archivo de enrutamiento web ubicado en `routes/web.php`. Reemplace las rutas por defecto para mapear la raíz de la aplicación directamente al controlador `TaskViewController`:
   ```php
   <?php
   // filepath: ~/labs/taskflow-app/routes/web.php

   use Illuminate\Support\Facades\Route;
   use App\Http\Controllers\TaskViewController;

   // Redirigir la raíz de la plataforma a la vista de tareas controlada
   Route::get('/', [TaskViewController::class, 'index'])->name('tasks.index');
   ```

**Resultado Esperado**:
Un enrutador que mapea las solicitudes web de la raíz del dominio hacia el controlador `TaskViewController`, el cual expone un conjunto de registros de prueba (tareas) estructurados en un formato de datos robusto.

**Verificación**:
Para verificar que las rutas se registraron correctamente, ejecute:
```bash
php artisan route:list
```
El listado debe contener una fila con el URI `/` apuntando al método `App\Http\Controllers\TaskViewController@index` utilizando el método HTTP `GET`.

---

### Paso 4: Creación del Layout Maestro (Blade Layout) con Tailwind CSS 4

**Objetivo**: Diseñar una plantilla base (layout) utilizando el motor de plantillas Blade de Laravel que encapsule la cabecera HTML común, cargue los activos de Vite y declare el contenedor principal donde se insertarán las vistas hijas.

**Instrucciones**:

1. Cree los directorios requeridos para organizar las vistas del sistema dentro del directorio de recursos:
   ```bash
   mkdir -p resources/views/layouts
   mkdir -p resources/views/components
   mkdir -p resources/views/tasks
   ```

2. Cree un nuevo archivo de plantilla para el layout maestro en `resources/views/layouts/app.blade.php`:
   ```html
   <!-- filepath: ~/labs/taskflow-app/resources/views/layouts/app.blade.php -->
   <!DOCTYPE html>
   <html lang="es" class="h-full bg-slate-50">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>@yield('title', 'TaskFlow - Gestor de Tareas')</title>
       
       <!-- Inyección de assets procesados por Vite -->
       @vite(['resources/css/app.css', 'resources/js/app.js'])
   </head>
   <body class="flex flex-col min-h-screen text-slate-900 font-sans antialiased">
       <!-- Barra de Navegación Común -->
       <nav class="bg-white border-b border-slate-200">
           <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
               <div class="flex justify-between h-16">
                   <div class="flex items-center">
                       <div class="flex-shrink-0 flex items-center">
                           <span class="text-xl font-black text-indigo-600 tracking-wider">TASKFLOW</span>
                           <span class="ml-2 px-2 py-0.5 text-xs font-semibold bg-slate-100 text-slate-700 rounded-full">v1.0</span>
                       </div>
                       <div class="hidden sm:ml-6 sm:flex sm:space-x-8">
                           <a href="{{ route('tasks.index') }}" class="border-indigo-500 text-slate-900 inline-flex items-center px-1 pt-1 border-b-2 text-sm font-medium">
                               Panel de Tareas
                           </a>
                       </div>
                   </div>
                   <div class="flex items-center space-x-2">
                       <div class="w-2.5 h-2.5 rounded-full bg-emerald-500 animate-pulse"></div>
                       <span class="text-xs font-semibold text-slate-500">Servidor Activo (PHP 8.5)</span>
                   </div>
               </div>
           </div>
       </nav>

       <!-- Contenido Dinámico de la Vista -->
       <main class="flex-grow">
           <div class="max-w-7xl mx-auto py-8 px-4 sm:px-6 lg:px-8">
               @yield('content')
           </div>
       </main>

       <!-- Pie de Página -->
       <footer class="bg-white border-t border-slate-200 py-6">
           <div class="max-w-7xl mx-auto px-4 text-center sm:px-6 lg:px-8">
               <p class="text-sm text-slate-500">
                   &copy; {{ date('Y') }} TaskFlow. Desarrollado con Laravel 13 y Tailwind CSS 4.0.0.
               </p>
           </div>
       </footer>
   </body>
   </html>
   ```

**Resultado Esperado**:
Un archivo de plantilla estructurado de forma semántica con slots dinámicos (`@yield`) listos para recibir el contenido de vistas específicas y la directiva de inyección de recursos `@vite` configurada.

**Verificación**:
Inspeccione el contenido del archivo `resources/views/layouts/app.blade.php` con un lector del sistema para asegurar la correcta sintaxis de las directivas Blade:
```bash
cat resources/views/layouts/app.blade.php | grep -E '(@yield|@vite)'
```
Debe retornar las líneas que contienen `@yield('title'...)`, `@vite(...)` y `@yield('content')`.

---

### Paso 5: Diseño de Vistas de Tareas y Componentes Blade

**Objetivo**: Crear componentes Blade modulares y reutilizables (un componente de botón y una tarjeta de tareas) consumiendo los estilos de Tailwind v4, e implementar la vista principal de la lista de tareas.

**Instrucciones**:

1. Cree un componente reutilizable para botones de acción en `resources/views/components/button.blade.php`. Este componente aceptará parámetros de variante de color:
   ```html
   <!-- filepath: ~/labs/taskflow-app/resources/views/components/button.blade.php -->
   @props([
       'variant' => 'primary'
   ])

   @php
       $classes = 'inline-flex items-center px-4 py-2 border text-sm font-semibold rounded-lg shadow-xs focus:outline-hidden focus:ring-2 focus:ring-offset-2 transition-all duration-150 ';
       
       $variants = [
           'primary' => 'border-transparent text-white bg-indigo-600 hover:bg-indigo-700 focus:ring-indigo-500',
           'secondary' => 'border-slate-300 text-slate-700 bg-white hover:bg-slate-50 focus:ring-slate-500',
           'danger' => 'border-transparent text-white bg-rose-600 hover:bg-rose-700 focus:ring-rose-500'
       ];

       $classes .= $variants[$variant] ?? $variants['primary'];
   @endphp

   <button {{ $attributes->merge(['class' => $classes]) }}>
       {{ $slot }}
   </button>
   ```

2. Cree un componente para representar visualmente cada tarea en `resources/views/components/task-card.blade.php`. Este componente recibirá como propiedad un arreglo asociativo `$task`:
   ```html
   <!-- filepath: ~/labs/taskflow-app/resources/views/components/task-card.blade.php -->
   @props(['task'])

   @php
       // Mapeo de estilos según la prioridad
       $priorityClasses = [
           'Alta' => 'bg-rose-50 text-rose-700 border-rose-200',
           'Media' => 'bg-amber-50 text-amber-700 border-amber-200',
           'Baja' => 'bg-sky-50 text-sky-700 border-sky-200',
       ];
       $priorityStyle = $priorityClasses[$task['priority']] ?? 'bg-slate-50 text-slate-700 border-slate-200';

       // Mapeo de estilos según el estado
       $statusClasses = [
           'Completada' => 'bg-emerald-100 text-emerald-800',
           'En Progreso' => 'bg-blue-100 text-blue-800',
           'Pendiente' => 'bg-slate-100 text-slate-800',
       ];
       $statusStyle = $statusClasses[$task['status']] ?? 'bg-slate-100 text-slate-800';
   @endphp

   <div class="bg-white border border-slate-200 rounded-xl p-6 shadow-xs hover:shadow-md transition-shadow duration-200 flex flex-col justify-between">
       <div>
           <div class="flex items-center justify-between mb-4">
               <span class="px-2.5 py-1 text-xs font-bold border rounded-md {{ $priorityStyle }}">
                   Prioridad: {{ $task['priority'] }}
               </span>
               <span class="px-2.5 py-0.5 text-xs font-semibold rounded-full {{ $statusStyle }}">
                   {{ $task['status'] }}
               </span>
           </div>

           <h3 class="text-lg font-bold text-slate-800 line-clamp-1 mb-2">
               {{ $task['title'] }}
           </h3>
           <p class="text-sm text-slate-600 line-clamp-3 mb-4">
               {{ $task['description'] }}
           </p>
       </div>

       <div class="border-t border-slate-100 pt-4 mt-auto">
           <div class="flex items-center justify-between text-xs text-slate-500 mb-4">
               <span>Fecha Límite:</span>
               <span class="font-medium text-slate-700">{{ $task['due_date'] }}</span>
           </div>
           
           <div class="flex space-x-2">
               <x-button variant="secondary" class="flex-1 justify-center py-1.5 text-xs">
                   Editar
               </x-button>
               <x-button variant="primary" class="flex-1 justify-center py-1.5 text-xs">
                   Completar
               </x-button>
           </div>
       </div>
   </div>
   ```

3. Cree la plantilla principal del listado de tareas en `resources/views/tasks/index.blade.php`. Esta plantilla heredará la estructura del layout maestro, iterará la colección de tareas y las inyectará en los componentes correspondientes:
   ```html
   <!-- filepath: ~/labs/taskflow-app/resources/views/tasks/index.blade.php -->
   @extends('layouts.app')

   @section('title', 'Panel de Control de Tareas - TaskFlow')

   @section('content')
   <div class="space-y-8">
       <!-- Cabecera de Sección -->
       <div class="flex flex-col sm:flex-row sm:items-center sm:justify-between gap-4">
           <div>
               <h1 class="text-3xl font-black text-slate-900 tracking-tight">Panel de Tareas</h1>
               <p class="mt-1 text-sm text-slate-600">
                   Monitorea y organiza tu flujo de trabajo diario de manera ágil.
               </p>
           </div>
           <div>
               <x-button variant="primary" class="shadow-sm">
                   <svg class="w-5 h-5 mr-2 -ml-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                       <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4" />
                   </svg>
                   Nueva Tarea
               </x-button>
           </div>
       </div>

       <!-- Cuadrícula de Tareas -->
       <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
           @forelse($tasks as $task)
               <x-task-card :task="$task" />
           @empty
               <div class="col-span-full bg-white border border-dashed border-slate-300 rounded-xl p-12 text-center">
                   <svg class="mx-auto h-12 w-12 text-slate-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                       <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" />
                   </svg>
                   <h3 class="mt-2 text-sm font-medium text-slate-900">No se encontraron tareas</h3>
                   <p class="mt-1 text-sm text-slate-500">Comienza creando un elemento para ver tus pendientes.</p>
               </div>
           @endforelse
       </div>
   </div>
   @endsection
   ```

**Resultado Esperado**:
Una vista completamente modularizada construida bajo el paradigma de componentes Blade y renderizada sobre un layout maestro que integra de forma nativa los estilos de Tailwind CSS v4.

**Verificación**:
Asegúrese de que todos los archivos requeridos para el paso se hayan guardado en los directorios correctos:
```bash
ls resources/views/layouts/
ls resources/views/components/
ls resources/views/tasks/
```
Deben visualizarse los archivos: `app.blade.php`, `button.blade.php`, `task-card.blade.php` e `index.blade.php`.

---

## Validación y Pruebas

Para garantizar que la aplicación responde correctamente bajo los estándares del framework y valida de forma robusta la transferencia de datos y la seguridad contra ataques XSS, se implementará un conjunto de pruebas automatizadas mediante el motor integrado PHPUnit.

### 1. Creación del Archivo de Pruebas
Ejecute el generador de pruebas de Laravel para crear un caso de prueba funcional sobre las vistas de tareas:
```bash
php artisan make:test TaskViewTest
```

Modifique el archivo generado en `tests/Feature/TaskViewTest.php` para validar los comportamientos descritos en el script de abajo:
```php
<?php
// filepath: ~/labs/taskflow-app/tests/Feature/TaskViewTest.php

namespace Tests\Feature;

use Tests\TestCase;

class TaskViewTest extends TestCase
{
    /**
     * Prueba que la ruta principal responde de forma exitosa y renderiza los componentes.
     */
    public function test_task_index_page_loads_successfully_and_contains_tasks(): void
    {
        // 1. Ejecutar solicitud HTTP GET sobre la raíz
        $response = $this->get('/');

        // 2. Validar código de respuesta HTTP 200
        $response->assertStatus(200);

        // 3. Validar que se use la estructura del layout y vista correctos
        $response->assertViewIs('tasks.index');

        // 4. Asegurar que las variables de datos existan y contengan la información mockeada
        $response->assertViewHas('tasks');

        // 5. Verificar que el texto característico de la interfaz esté presente en el HTML compilado
        $response->assertSee('Panel de Tareas');
        $response->assertSee('Configurar Entorno de Desarrollo');
        $response->assertSee('Integrar Tailwind CSS 4.0.0');
    }

    /**
     * Prueba de seguridad (Adversarial XSS Case):
     * Verifica que el motor Blade escape de forma nativa entradas de texto que simulen 
     * inyecciones de código malicioso o scripts que busquen evadir el control.
     */
    public function test_blade_escapes_xss_attempts(): void
    {
        // Simulamos un valor de entrada cargado de un script de inyección malicioso
        $xssPayload = '<script>alert("XSS Attack!");</script>';
        
        // El motor Blade renderiza variables usando {{ }} las cuales automáticamente aplican htmlspecialchars().
        // Por ende, la etiqueta '<script>' debe transformarse en '&lt;script&gt;'
        $escapedString = e($xssPayload);

        $this->assertEquals('&lt;script&gt;alert(&quot;XSS Attack!&quot;);&lt;/script&gt;', $escapedString);
    }
}
```

### 2. Ejecución de las Pruebas
Ejecute la suite de pruebas del framework utilizando PHPUnit para validar que todo el código base pasa de manera correcta las pruebas:
```bash
php artisan test
```

La salida en la terminal debe ser similar a la siguiente imagen de éxito:
```text
   PASS  Tests\Feature\TaskViewTest
   ✓ task index page loads successfully and contains tasks                 0.18s
   ✓ blade escapes xss attempts                                            0.01s

  Tests:    2 passed (2 assertions)
  Duration: 0.28s
```

### 3. Prueba en Navegador
Para realizar la prueba interactiva del sistema completo, levante en paralelo ambos servidores de desarrollo en consolas independientes:

- **Consola 1**: Servidor backend Laravel PHP
  ```bash
  php artisan serve --port=8000
  ```

- **Consola 2**: Servidor de desarrollo Vite frontend
  ```bash
  npm run dev
  ```

Abra su navegador web e ingrese a `http://localhost:8000`. Debe ver el panel de control de TaskFlow con un diseño limpio de color índigo y gris claro. Modifique el tamaño de la pantalla del navegador para observar la adaptabilidad del diseño (grid responsivo que colapsa de 4 columnas en monitores grandes a 1 sola columna en dispositivos móviles).

---

## Solución de Problemas

A continuación, se listan dos problemas típicos y de alta probabilidad que pueden ocurrir al inicializar este tipo de infraestructuras junto con sus soluciones correspondientes.

### Problema 1: Los estilos de Tailwind v4 no se aplican en el navegador (La página renderiza HTML puro)
*   **Síntomas**: La página se visualiza con fuentes estándar del navegador (Times New Roman), sin colores de fondo, y los botones se amontonan de manera desordenada en la parte inferior izquierda de la pantalla.
*   **Causa**: El servidor de desarrollo de Vite (`npm run dev`) no se está ejecutando en segundo plano, o falta la directiva nativa `@import "tailwindcss";` dentro del archivo `resources/css/app.css`, lo que impide que el plugin preprocese el archivo de estilos dinámicamente.
*   **Solución**: 
    1. Asegúrese de que el servidor de Vite se encuentre en ejecución abriendo una terminal nueva y ejecutando `npm run dev`.
    2. Compruebe el contenido de `resources/css/app.css`. Debe tener únicamente `@import "tailwindcss";` y no las directivas de la versión 3.x (como `@tailwind base;`).
    3. Verifique que la etiqueta `@vite` esté declarada en la sección `<head>` de `resources/views/layouts/app.blade.php`.

### Problema 2: Error `Vite manifest not found` al abrir el navegador
*   **Síntomas**: Al cargar la página `http://localhost:8000`, la aplicación web de Laravel muestra una pantalla de error de color rojo de Flare (el manejador de excepciones de Laravel) indicando que el archivo manifest no fue encontrado en la ruta de construcción de producción de la aplicación.
*   **Causa**: Laravel está buscando el archivo de compilación estático generado en la carpeta `public/build` pero no se tiene un servidor activo de desarrollo (`npm run dev`) ni se ha ejecutado el comando de compilación estática.
*   **Solución**: 
    - **Para entorno de desarrollo (con recarga rápida)**: Mantenga una pestaña de terminal ejecutando `npm run dev`.
    - **Para entorno de simulación de producción (sin servidor Vite activo)**: Genere la compilación estática de activos del frontend en el disco ejecutando la tarea de compilación en su consola:
      ```bash
      npm run build
      ```
      Este comando compila el CSS y JS minimizados en la ruta `public/build`, solucionando inmediatamente el error del manifiesto.

---

## Limpieza

Para restaurar el entorno del laboratorio y preparar la entrega o desarrollo del siguiente módulo:

1. Detenga el servidor de desarrollo de Laravel y el de Vite utilizando la combinación de teclas en su consola:
   ```bash
   # Presione en las terminales activas
   Ctrl + C
   ```

2. Ejecute la tarea de limpieza del caché de Laravel para eliminar configuraciones temporales:
   ```bash
   php artisan config:clear
   php artisan route:clear
   php artisan view:clear
   ```

3. Revise que no existan cambios sin guardar en su repositorio de Git, y confirme sus progresos con un commit descriptivo sobre la rama local:
   ```bash
   git add .
   git commit -m "feat: inicializacion de TaskFlow con Laravel 13, Vite 6, Tailwind CSS 4, layout maestro y suite de pruebas iniciales"
   ```

---

## Resumen

En este laboratorio, hemos alcanzado hitos críticos para el desarrollo de la aplicación full-stack **TaskFlow**:

1. **Estructuración del Backend**: Inicializamos un proyecto limpio basado en **Laravel 13.0.0** y **PHP 8.5.0**, configurando sus parámetros de variables de entorno y base de datos para alinearlo con las necesidades del curso.
2. **Pipelines de Frontend Modernos**: Integramos las herramientas más vanguardistas del desarrollo web moderno, implementando **Vite 6.0.5** junto con la revolucionaria especificación de **Tailwind CSS 4.0.0**, eliminando configuraciones complejas de Javascript para agilizar la compilación de estilos mediante plugins de entorno nativos.
3. **Mapeo MVC y Blade**: Implementamos un controlador `TaskViewController` que transfiere colecciones de datos simuladas a vistas compuestas mediante un layout base de Blade, optimizando la modularidad a través de componentes autocontenidos y parametrizables como tarjetas (`task-card`) y botones (`button`).
4. **Validaciones de Seguridad y Robustez**: Validamos el flujo de ejecución mediante pruebas automatizadas que aseguran que el motor de renderizado escape de forma nativa amenazas de inyección de código tipo Cross-Site Scripting (XSS), consolidando las bases de seguridad del proyecto.

### Recursos Adicionales Recomendados
- [Documentación Oficial de Laravel 13.x](https://laravel.com/docs/11.x) *(o de la versión estable de su ciclo en laravel.com)*
- [Guía de Migración y Características de Tailwind CSS v4](https://tailwindcss.com/docs/v4-beta)
- [Documentación de Integración de Vite en Laravel](https://laravel.com/docs/vite)
