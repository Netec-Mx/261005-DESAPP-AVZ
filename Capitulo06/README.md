# 5 Práctica: Integración full-stack con Laravel 13.x, Blade, Vite, Inertia.js, Vue 3.x o React 19.x, TypeScript y Tailwind CSS 4.x

## Metadatos
| Campo | Detalle |
| :--- | :--- |
| **Duración** | 150 minutos |
| **Complejidad** | Alta |
| **Nivel de Bloom** | Aplicar (Apply) |

## Descripción General
En este laboratorio práctico, construirás de manera integral una aplicación híbrida de gestión de tareas colaborativas denominada **TaskFlow**. El proyecto se implementará desde cero en el directorio de trabajo `~/labs/taskflow-app`. 

La arquitectura de la aplicación combina de manera simultánea dos enfoques de renderizado:
1. **Un Panel Administrativo Clásico**: Renderizado en el servidor mediante vistas Blade, optimizado con clases de utilidad de **Tailwind CSS v4** y potenciado con interactividad puntual en JavaScript Vanilla integrado mediante **Vite 6**.
2. **Un Tablero de Tareas Interactivo (Kanban Board)**: Una SPA (Single Page Application) moderna e interactiva que utiliza componentes de **React 19** y **TypeScript**, sincronizados mediante el protocolo de transferencia de estado de **Inertia.js**.

La persistencia de datos se gestionará localmente utilizando un motor de base de datos **SQLite**. Finalmente, el flujo completo de datos y la validez de las respuestas HTTP de ambos extremos de la arquitectura se validarán con pruebas de integración utilizando el framework de testing **Pest PHP**.

---

## Objetivos de Aprendizaje
Al finalizar este laboratorio, serás capaz de:
- [ ] Inicializar un proyecto Laravel y configurar el entorno de base de datos local SQLite.
- [ ] Configurar Vite 6 para dar soporte simultáneo a plantillas clásicas Blade y aplicaciones SPA con Inertia.js.
- [ ] Integrar React 19 y TypeScript configurando los archivos de declaración de tipos y `tsconfig.json`.
- [ ] Implementar Tailwind CSS v4 utilizando la nueva arquitectura de compilación nativa sin el archivo `tailwind.config.js` clásico.
- [ ] Desarrollar vistas híbridas: un módulo de estadísticas clásicas en Blade y un módulo interactivo (Tablero Kanban) utilizando Componentes Inertia.js en React.
- [ ] Diseñar controladores y rutas unificadas que resuelvan la lógica de negocio y transferencia de estados.
- [ ] Implementar y ejecutar pruebas automatizadas de integración utilizando Pest PHP para validar respuestas HTTP 200 y estructuras de datos.

---

## Prerrequisitos
Para completar con éxito este laboratorio, requieres:
1. **Conocimientos teóricos y prácticos**:
   - Comprensión sólida de la arquitectura MVC y ruteo en Laravel.
   - Manejo del flujo de desarrollo moderno con JavaScript (ES6+), incluyendo promesas, importaciones de módulos y tipado estático con TypeScript.
   - Familiaridad básica con React (Hooks comunes: `useState`, `useEffect`) y clases utilitarias de Tailwind CSS.
2. **Acceso al entorno**:
   - Terminal de comandos con permisos de escritura en la ruta de usuario `~/labs/`.
   - Conexión a internet para la descarga de dependencias mediante Composer y npm.

---

## Entorno de Laboratorio

Las especificaciones exactas de hardware y software requeridas para garantizar la reproducibilidad y el correcto funcionamiento de este laboratorio son las siguientes:

### Especificaciones de Hardware
- **Procesador**: x86_64 o ARM64 (Apple Silicon) de mínimo 4 núcleos.
- **Memoria RAM**: Mínimo 8 GB (Recomendado 16 GB).
- **Almacenamiento**: Estado sólido (SSD) con al menos 10 GB de espacio libre dedicado.

### Tabla de Versiones de Software y Herramientas

| Software / Dependencia | Versión Exacta | Licencia | Enlace de Descarga / Fuente Oficial |
| :--- | :--- | :--- | :--- |
| **PHP (CLI/FPM)** | 8.5.0 | PHP License | [php.net](https://www.php.net/downloads) |
| **Composer** | 2.10.3 | MIT | [getcomposer.org](https://getcomposer.org/download/) |
| **Node.js** | 24.21.0 | MIT | [nodejs.org](https://nodejs.org/) |
| **npm** | 11.1.0 | Artistic License 2.0 | [npmjs.com](https://www.npmjs.com/) |
| **SQLite** | 3.45.3 | Public Domain | [sqlite.org](https://www.sqlite.org/) |
| **Laravel Framework** | 11.8.0 | MIT | [laravel.com](https://laravel.com) |
| **Tailwind CSS** | 4.0.0-alpha.15 | MIT | [tailwindcss.com](https://tailwindcss.com/) |
| **React** | 19.0.0-rc-f9947a08-20240521 | MIT | [react.dev](https://react.dev/) |
| **TypeScript** | 5.4.5 | Apache-2.0 | [typescriptlang.org](https://www.typescriptlang.org/) |
| **Inertia.js React** | 1.2.0 | MIT | [inertiajs.com](https://inertiajs.com/) |
| **Vite** | 6.0.5 | MIT | [vite.dev](https://vite.dev/) |
| **Pest PHP** | 2.34.0 | MIT | [pestphp.com](https://pestphp.com/) |

*(Nota: Si utilizas herramientas de asistencia de IA para acelerar el desarrollo del código, asegúrate de poseer una licencia corporativa o individual activa para **GitHub Copilot** o **Microsoft 365 Copilot Chat** configurada en tu IDE de preferencia como VS Code).*

### Preparación del Entorno
Antes de iniciar con el paso a paso, ejecuta los siguientes comandos en tu terminal para garantizar que el directorio base exista y limpies posibles ejecuciones anteriores:

```bash
mkdir -p ~/labs
cd ~/labs
rm -rf taskflow-app
```

---

## Instrucciones Paso a Paso

### Paso 1: Inicialización del Proyecto y Configuración de SQLite
**Objetivo**: Crear un esqueleto limpio de Laravel y configurar un archivo de base de datos local SQLite.

**Instrucciones**:
1. Crea el nuevo proyecto de Laravel usando Composer:
   ```bash
   composer create-project laravel/laravel:11.8.0 taskflow-app
   cd taskflow-app
   ```
2. Inicializa un repositorio Git local y cambia a la rama de trabajo asignada para este laboratorio:
   ```bash
   git init
   git checkout -b lab-06-integracion-fullstack
   ```
3. Crea el archivo físico de base de datos SQLite dentro del directorio correspondiente:
   ```bash
   touch database/database.sqlite
   ```
4. Abre el archivo `.env` en tu editor de código y modifica los parámetros de conexión de base de datos para utilizar de manera exclusiva la conexión SQLite. Reemplaza las líneas que configuran la base de datos por el siguiente bloque exacto:
   ```env
   DB_CONNECTION=sqlite
   DB_DATABASE=/home/user/labs/taskflow-app/database/database.sqlite
   ```
   *(Nota: Asegúrate de adaptar `/home/user` con la ruta absoluta real a tu directorio home de usuario en caso de que sea diferente en tu sistema local).*

5. Ejecuta las migraciones por defecto provistas por Laravel para validar la conexión con la base de datos SQLite:
   ```bash
   php artisan migrate
   ```

**Resultado esperado**:
La base de datos se conectará sin errores y se crearán las tablas por defecto en SQLite.

**Verificación**:
```bash
## Verificar que el archivo de base de datos no está vacío
sqlite3 database/database.sqlite "SELECT name FROM sqlite_master WHERE type='table';"
```
*(Deberías visualizar las tablas del sistema de Laravel, como `users`, `migrations`, `sessions`, entre otras).*

---

### Paso 2: Instalación de Dependencias Frontend y Configuración de TypeScript
**Objetivo**: Instalar los módulos de Node necesarios para integrar React 19, TypeScript e Inertia.js, y configurar la estructura de tipados.

**Instrucciones**:
1. Ejecuta la instalación del adaptador de backend de Inertia para Laravel mediante Composer:
   ```bash
   composer require inertiajs/inertia-laravel:^1.2.0
   ```
2. Instala las dependencias del frontend mediante npm, forzando la versión de React 19 especificada y los tipados necesarios para TypeScript:
   ```bash
   npm install @inertiajs/react@1.2.0 react@19.0.0-rc-f9947a08-20240521 react-dom@19.0.0-rc-f9947a08-20240521 typescript@5.4.5 @types/react@19.0.0-rc-f9947a08-20240521 @types/react-dom@19.0.0-rc-f9947a08-20240521 @vitejs/plugin-react@5.1.1 --save-dev
   ```
3. Inicializa el archivo de configuración de TypeScript (`tsconfig.json`) en la raíz del proyecto ejecutando el comando de compilación o creando el archivo de manera manual. Crea el archivo `tsconfig.json` con la siguiente configuración estricta y adaptada para Vite/React:
   ```json
   {
       "compilerOptions": {
           "target": "es2022",
           "useDefineForClassFields": true,
           "module": "ESNext",
           "lib": ["DOM", "DOM.Iterable", "ScriptHost", "ES2022"],
           "skipLibCheck": true,
           "moduleResolution": "node",
           "allowImportingTsExtensions": true,
           "resolveJsonModule": true,
           "isolatedModules": true,
           "noEmit": true,
           "jsx": "react-jsx",
           "strict": true,
           "noUnusedLocals": true,
           "noUnusedParameters": true,
           "noImplicitReturns": true,
           "noFallthroughCasesInSwitch": true,
           "baseUrl": ".",
           "paths": {
               "@/*": ["resources/js/*"]
           }
       },
       "include": ["resources/js/**/*.ts", "resources/js/**/*.tsx", "resources/js/**/*.d.ts"]
   }
   ```
4. Para evitar errores de resolución de tipos dentro del ecosistema de Inertia, crea el archivo de definición global `resources/js/types/inertia.d.ts`:
   ```bash
   mkdir -p resources/js/types
   touch resources/js/types/inertia.d.ts
   ```
   Agrega la siguiente declaración de módulos:
   ```typescript
   import { Page, PageProps } from '@inertiajs/core';

   declare global {
       interface Window {
           _token?: string;
       }
   }

   declare module '@inertiajs/react' {
       export interface PageProps {
           errors: Record<string, string>;
           flash?: {
               message?: string;
               success?: string;
               error?: string;
           };
           [key: string]: any;
       }
   }
   ```

**Resultado esperado**:
Se habrán creado el archivo `tsconfig.json` y la estructura de directorios del tipado sin discrepancias en las dependencias.

**Verificación**:
```bash
## Validar la correcta sintaxis y compilación básica de TypeScript
npx tsc --noEmit
```
*(No debe reportar errores de sintaxis en el archivo de configuración creado).*

---

### Paso 3: Configuración de Tailwind CSS v4 con la nueva arquitectura nativa
**Objetivo**: Integrar Tailwind CSS v4.0.0-alpha.15 utilizando el nuevo plugin de Vite nativo, eliminando la necesidad de archivos de configuración tradicionales.

**Instrucciones**:
1. Instala el motor de compilación nativo y el plugin de Vite oficiales de Tailwind CSS v4:
   ```bash
   npm install tailwindcss@4.0.0-alpha.15 @tailwindcss/vite@4.0.0-alpha.15 --save-dev
   ```
2. Modifica el archivo de estilos global en `resources/css/app.css` para utilizar la directiva simplificada e importación nativa de Tailwind CSS v4. Elimina cualquier contenido previo y reemplázalo exactamente por:
   ```css
   @import "tailwindcss";
   ```
   *(Nota: Tailwind CSS v4 ya no requiere las tres directivas tradicionales `@tailwind base; @tailwind components; @tailwind utilities;` en su nuevo motor).*

**Resultado esperado**:
El archivo de estilos CSS contendrá únicamente la importación nativa de la versión 4.x lista para ser procesada por el plugin oficial de Vite.

**Verificación**:
Verifica visualmente que el archivo de estilos contenga exclusivamente el `@import "tailwindcss";`.

---

### Paso 4: Configuración de Vite para Entrada Dual (Blade + Inertia)
**Objetivo**: Configurar `vite.config.js` para compilar simultáneamente los estilos con Tailwind CSS v4, el backend de React 19 y las rutas de entrada híbridas.

**Instrucciones**:
1. Edita el archivo `vite.config.js` en la raíz de tu proyecto. El archivo debe unificar el plugin de Laravel, el soporte de React y el optimizador de Tailwind. Escribe la siguiente configuración completa:
   ```javascript
   import { defineConfig } from 'vite';
   import laravel from 'laravel-vite-plugin';
   import react from '@vitejs/plugin-react';
   import tailwindcss from '@tailwindcss/vite';
   import path from 'path';

   export default defineConfig({
       plugins: [
           laravel({
               input: [
                   'resources/css/app.css',
                   'resources/js/app.js',
                   'resources/js/app.tsx'
               ],
               refresh: true,
           }),
           react(),
           tailwindcss(),
       ],
       resolve: {
           alias: {
               '@': path.resolve(__dirname, './resources/js'),
           },
       },
   });
   ```

**Resultado esperado**:
Un archivo de configuración unificado que declare de forma explícita las rutas de entrada `app.js` (usada en las vistas Blade) y `app.tsx` (usada en la Single Page Application de Inertia).

**Verificación**:
Ejecuta el servidor de desarrollo de Vite para verificar que no existan errores sintácticos o de dependencias ausentes:
```bash
npm run dev -- --run
```
*(Cancela el proceso con `Ctrl+C` una vez verificado que se inicia sin fallas).*

---

### Paso 5: Implementación de la Vista Administrativa Clásica (Blade + JS Vanilla + Tailwind 4)
**Objetivo**: Construir el modelo de datos `Task`, migrar la base de datos y crear la vista clásica en Blade que interactúa dinámicamente con un script de JavaScript mediante atributos de datos `data-*`.

**Instrucciones**:
1. Genera el modelo `Task` junto con su archivo de migración correspondiente:
   ```bash
   php artisan make:model Task -m
   ```
2. Modifica la migración creada en `database/migrations/xxxx_xx_xx_xxxxxx_create_tasks_table.php` definiendo la estructura requerida:
   ```php
   <?php

   use Illuminate\Database\Migrations\Migration;
   use Illuminate\Database\Schema\Blueprint;
   use Illuminate\Support\Facades\Schema;

   return new class extends Migration {
       public function up(): void
       {
           Schema::create('tasks', function (Blueprint $table) {
               $table->id();
               $table->string('title');
               $table->text('description');
               $table->string('status')->default('todo'); // Opciones: todo, in_progress, done
               $table->timestamps();
           });
       }

       public function down(): void
       {
           Schema::dropIfExists('tasks');
       }
   };
   ```
3. Ejecuta la migración para crear la tabla de tareas en SQLite:
   ```bash
   php artisan migrate
   ```
4. Registra datos de prueba en la base de datos. Para ello, crea un seeder rápido en `database/seeders/DatabaseSeeder.php`:
   ```php
   <?php

   namespace Database\Seeders;

   use Illuminate\Database\Seeder;
   use App\Models\Task;

   class DatabaseSeeder extends Seeder
   {
       public function run(): void
       {
           Task::create([
               'title' => 'Configurar Servidor de Desarrollo',
               'description' => 'Configurar Apache/Nginx y verificar puertos de enlace.',
               'status' => 'done'
           ]);
           Task::create([
               'title' => 'Implementar Middleware de Seguridad',
               'description' => 'Configurar CORS y protección contra ataques XSS.',
               'status' => 'in_progress'
           ]);
           Task::create([
               'title' => 'Diseñar Interfaz con Tailwind v4',
               'description' => 'Construir el layout principal y el sidebar responsivo.',
               'status' => 'todo'
           ]);
       }
   }
   ```
   Ejecuta el seeder:
   ```bash
   php artisan db:seed
   ```
5. Implementa el controlador del Panel Administrativo de Blade. Ejecuta:
   ```bash
   php artisan make:controller DashboardController
   ```
   Abre `app/Http/Controllers/DashboardController.php` y escribe el siguiente código:
   ```php
   <?php

   namespace App\Http\Controllers;

   use App\Models\Task;
   use Illuminate\Http\Request;

   class DashboardController extends Controller
   {
       public function index()
       {
           // Cálculo de estadísticas rápidas para Blade
           $totalTasks = Task::count();
           $completedTasks = Task::where('status', 'done')->count();
           $pendingTasks = Task::where('status', 'todo')->count();
           $inProgressTasks = Task::where('status', 'in_progress')->count();

           return view('dashboard.index', compact('totalTasks', 'completedTasks', 'pendingTasks', 'inProgressTasks'));
       }
   }
   ```
6. Crea el archivo de entrada para interactividad en Blade. Modifica `resources/js/app.js` y agrega el soporte de interactividad rápida y lectura dinámica de atributos mediante clases nativas:
   ```javascript
   import './bootstrap';

   document.addEventListener('DOMContentLoaded', () => {
       const widget = document.getElementById('interactive-widget');
       if (widget) {
           const limit = parseInt(widget.dataset.alertLimit || '5', 10);
           const total = parseInt(widget.dataset.currentTasks || '0', 10);
           const alertBox = document.getElementById('status-alert');

           if (total >= limit && alertBox) {
               alertBox.classList.remove('hidden');
               alertBox.classList.add('flex');
           }
       }
   });
   ```
7. Crea la vista de Blade del Dashboard en `resources/views/dashboard/index.blade.php`:
   ```html
   <!DOCTYPE html>
   <html lang="es">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>TaskFlow | Panel Administrativo</title>
       @vite(['resources/css/app.css', 'resources/js/app.js'])
   </head>
   <body class="bg-slate-50 text-slate-900 font-sans antialiased">
       <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-10">
           <!-- Header -->
           <div class="flex justify-between items-center mb-10">
               <div>
                   <h1 class="text-4xl font-extrabold tracking-tight text-indigo-900">TaskFlow Admin</h1>
                   <p class="text-slate-500 mt-1">Monitoreo clásico y estadísticas del sistema (Vista Blade + Tailwind 4).</p>
               </div>
               <a href="/tasks" class="inline-flex items-center px-4 py-2 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 shadow-sm transition-colors">
                   Ir al Tablero SPA (Inertia) &rarr;
               </a>
           </div>

           <!-- Interactive Alert Box (JS Vanilla manipulation via dataset) -->
           <div id="interactive-widget" data-alert-limit="3" data-current-tasks="{{ $totalTasks }}">
               <div id="status-alert" class="hidden items-center p-4 mb-8 text-amber-800 border-l-4 border-amber-500 bg-amber-50 rounded-r-md" role="alert">
                   <svg class="flex-shrink-0 w-5 h-5 mr-3" fill="currentColor" viewBox="0 0 20 20">
                       <path fill-rule="evenodd" d="M8.257 3.099c.765-1.36 2.722-1.36 3.486 0l5.58 9.92c.75 1.334-.213 2.98-1.742 2.98H4.42c-1.53 0-2.493-1.646-1.743-2.98l5.58-9.92zM11 13a1 1 0 11-2 0 1 1 0 012 0zm-1-8a1 1 0 00-1 1v3a1 1 0 002 0V6a1 1 0 00-1-1z" clip-rule="evenodd"></path>
                   </svg>
                   <span class="font-bold mr-1">¡Atención!</span> Tienes un volumen alto de tareas asignadas en tu cola de trabajo. Considera delegar.
               </div>
           </div>

           <!-- Statistics Cards -->
           <div class="grid grid-cols-1 md:grid-cols-4 gap-6 mb-10">
               <div class="bg-white overflow-hidden shadow rounded-lg border border-slate-100 p-6">
                   <dt class="text-sm font-medium text-slate-500 truncate">Total de Tareas</dt>
                   <dd class="mt-1 text-3xl font-semibold text-indigo-600">{{ $totalTasks }}</dd>
               </div>
               <div class="bg-white overflow-hidden shadow rounded-lg border border-slate-100 p-6">
                   <dt class="text-sm font-medium text-slate-500 truncate">Pendientes</dt>
                   <dd class="mt-1 text-3xl font-semibold text-amber-600">{{ $pendingTasks }}</dd>
               </div>
               <div class="bg-white overflow-hidden shadow rounded-lg border border-slate-100 p-6">
                   <dt class="text-sm font-medium text-slate-500 truncate">En Progreso</dt>
                   <dd class="mt-1 text-3xl font-semibold text-blue-600">{{ $inProgressTasks }}</dd>
               </div>
               <div class="bg-white overflow-hidden shadow rounded-lg border border-slate-100 p-6">
                   <dt class="text-sm font-medium text-slate-500 truncate">Completadas</dt>
                   <dd class="mt-1 text-3xl font-semibold text-emerald-600">{{ $completedTasks }}</dd>
               </div>
           </div>
       </div>
   </body>
   </html>
   ```

**Resultado esperado**:
Una vista Blade que expone las estadísticas de la base de datos y que, mediante JS Vanilla detectando que hay 3 o más tareas guardadas, despliega automáticamente el aviso de alerta utilizando los estilos de Tailwind CSS v4.

**Verificación**:
No se requieren pruebas visuales aún. El comportamiento interactivo dinámico de la alerta se comprobará de forma automática con pruebas de integración.

---

### Paso 6: Implementación de la Single Page Application (SPA) con Inertia.js y React 19
**Objetivo**: Integrar y configurar la infraestructura de comunicación de Inertia.js con React 19 y TypeScript, diseñando un tablero interactivo tipo Kanban para el flujo de trabajo de tareas.

**Instrucciones**:
1. Registra el Middleware de Inertia en tu núcleo de Laravel. Ejecuta:
   ```bash
   php artisan inertia:middleware
   ```
2. Registra el middleware recién creado en el archivo de configuración global del framework en `bootstrap/app.php`. Configúralo dentro del grupo de middlewares de la siguiente manera exacta:
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
           $middleware->web(append: [
               \App\Http\Middleware\HandleInertiaRequests::class,
           ]);
       })
       ->withExceptions(function (Exceptions $exceptions) {
           //
       })->create();
   ```
3. Configura la vista raíz HTML donde se montará la SPA. Crea el archivo `resources/views/app.blade.php`:
   ```html
   <!DOCTYPE html>
   <html lang="es">
   <head>
       <meta charset="utf-8">
       <meta name="viewport" content="width=device-width, initial-scale=1">
       <title>TaskFlow SPA</title>
       @viteReactRefresh
       @vite(['resources/js/app.tsx', 'resources/css/app.css'])
       @inertiaHead
   </head>
   <body class="bg-slate-100 font-sans antialiased">
       @inertia
   </body>
   </html>
   ```
4. Crea el archivo de arranque (Bootstrap) de React 19 con soporte para Inertia. Crea `resources/js/app.tsx`:
   ```typescript
   import './bootstrap';
   import React from 'react';
   import { createRoot } from 'react-dom/client';
   import { createInertiaApp } from '@inertiajs/react';

   createInertiaApp({
       resolve: name => {
           const pages = import.meta.glob('./Pages/**/*.tsx', { eager: true });
           return pages[`./Pages/${name}.tsx`];
       },
       setup({ el, App, props }) {
           const root = createRoot(el);
           root.render(
               <React.StrictMode>
                   <App {...props} />
               </React.StrictMode>
           );
       },
   });
   ```
5. Genera el controlador de tareas que administrará la API e interactividad síncrona a través de Inertia:
   ```bash
   php artisan make:controller TaskController
   ```
   Abre `app/Http/Controllers/TaskController.php` y escribe la lógica completa que incluye validación estricta y re-direccionamiento síncrono de estado:
   ```php
   <?php

   namespace App\Http\Controllers;

   use App\Models\Task;
   use Illuminate\Http\Request;
   use Inertia\Inertia;

   class TaskController extends Controller
   {
       public function index()
       {
           $tasks = Task::all();
           return Inertia::render('Tasks/Index', [
               'tasks' => $tasks
           ]);
       }

       public function store(Request $request)
       {
           $validated = $request->validate([
               'title' => 'required|string|max:100',
               'description' => 'required|string|max:500',
               'status' => 'required|in:todo,in_progress,done'
           ]);

           Task::create($validated);

           return redirect()->route('tasks.index')->with('success', 'Tarea creada correctamente.');
       }

       public function updateStatus(Request $request, Task $task)
       {
           $validated = $request->validate([
               'status' => 'required|in:todo,in_progress,done'
           ]);

           $task->update($validated);

           return redirect()->route('tasks.index')->with('success', 'Estado actualizado.');
       }
   }
   ```
6. Crea el componente principal de la Single Page Application en `resources/js/Pages/Tasks/Index.tsx`:
   ```bash
   mkdir -p resources/js/Pages/Tasks
   touch resources/js/Pages/Tasks/Index.tsx
   ```
   Escribe el componente Kanban interactivo utilizando React 19 y tipado estático riguroso:
   ```tsx
   import React, { useState, useTransition } from 'react';
   import { router, usePage } from '@inertiajs/react';

   interface Task {
       id: number;
       title: string;
       description: string;
       status: 'todo' | 'in_progress' | 'done';
   }

   interface Props {
       tasks: Task[];
   }

   export default function Index({ tasks }: Props) {
       const [title, setTitle] = useState('');
       const [description, setDescription] = useState('');
       const [status, setStatus] = useState<'todo' | 'in_progress' | 'done'>('todo');
       const [isPending, startTransition] = useTransition();

       const { flash } = usePage().props;

       const handleSubmit = (e: React.FormEvent) => {
           e.preventDefault();
           
           router.post('/tasks', { title, description, status }, {
               onSuccess: () => {
                   setTitle('');
                   setDescription('');
                   setStatus('todo');
               }
           });
       };

       const handleMoveTask = (taskId: number, newStatus: 'todo' | 'in_progress' | 'done') => {
           startTransition(async () => {
               router.patch(`/tasks/${taskId}/status`, { status: newStatus });
           });
       };

       const columns: { id: 'todo' | 'in_progress' | 'done'; title: string; color: string }[] = [
           { id: 'todo', title: 'Por Hacer', color: 'bg-amber-100 text-amber-800' },
           { id: 'in_progress', title: 'En Progreso', color: 'bg-blue-100 text-blue-800' },
           { id: 'done', title: 'Completadas', color: 'bg-emerald-100 text-emerald-800' }
       ];

       return (
           <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-10">
               <div className="flex justify-between items-center mb-8">
                   <div>
                       <h1 className="text-3xl font-extrabold text-slate-900 tracking-tight">Tablero Kanban</h1>
                       <p className="text-slate-500 mt-1">Gestión fluida de requerimientos en tiempo real (SPA con React 19 + Inertia).</p>
                   </div>
                   <a href="/" className="text-sm font-medium text-indigo-600 hover:text-indigo-500">
                       &larr; Volver a Estadísticas (Blade)
                   </a>
               </div>

               {/* Flash Messages */}
               {flash?.success && (
                   <div className="mb-6 p-4 text-emerald-800 bg-emerald-50 rounded-md border border-emerald-200">
                       {flash.success}
                   </div>
               )}

               <div className="grid grid-cols-1 lg:grid-cols-3 gap-8">
                   {/* Formulario de Creación */}
                   <div className="lg:col-span-1 bg-white p-6 rounded-lg shadow-sm border border-slate-200">
                       <h2 className="text-xl font-bold text-slate-800 mb-6">Crear Nueva Tarea</h2>
                       <form onSubmit={handleSubmit} className="space-y-4">
                           <div>
                               <label className="block text-sm font-semibold text-slate-700 mb-1">Título</label>
                               <input
                                   type="text"
                                   value={title}
                                   onChange={(e) => setTitle(e.target.value)}
                                   className="w-full px-3 py-2 border border-slate-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500 text-sm"
                                   placeholder="Requerimiento técnico"
                                   required
                               />
                           </div>
                           <div>
                               <label className="block text-sm font-semibold text-slate-700 mb-1">Descripción</label>
                               <textarea
                                   value={description}
                                   onChange={(e) => setDescription(e.target.value)}
                                   className="w-full px-3 py-2 border border-slate-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500 text-sm"
                                   placeholder="Detalles sobre el requerimiento..."
                                   rows={3}
                                   required
                               />
                           </div>
                           <div>
                               <label className="block text-sm font-semibold text-slate-700 mb-1">Estado Inicial</label>
                               <select
                                   value={status}
                                   onChange={(e) => setStatus(e.target.value as 'todo' | 'in_progress' | 'done')}
                                   className="w-full px-3 py-2 border border-slate-300 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500 text-sm"
                               >
                                   <option value="todo">Por Hacer</option>
                                   <option value="in_progress">En Progreso</option>
                                   <option value="done">Completada</option>
                               </select>
                           </div>
                           <button
                               type="submit"
                               className="w-full py-2 px-4 bg-indigo-600 hover:bg-indigo-700 text-white font-medium rounded-md shadow-sm text-sm transition-colors focus:ring-2 focus:ring-indigo-500"
                           >
                               Guardar Tarea
                           </button>
                       </form>
                   </div>

                   {/* Tablero Kanban */}
                   <div className="lg:col-span-2 grid grid-cols-1 md:grid-cols-3 gap-4">
                       {columns.map((column) => (
                           <div key={column.id} className="bg-slate-50 p-4 rounded-lg border border-slate-200">
                               <div className="flex justify-between items-center mb-4">
                                   <h3 className="font-bold text-slate-800">{column.title}</h3>
                                   <span className={`px-2 py-0.5 text-xs rounded-full font-semibold ${column.color}`}>
                                       {tasks.filter(t => t.status === column.id).length}
                                   </span>
                               </div>
                               <div className="space-y-3">
                                   {tasks
                                       .filter((task) => task.status === column.id)
                                       .map((task) => (
                                           <div key={task.id} className="bg-white p-4 rounded-md shadow-sm border border-slate-100 hover:shadow-md transition-shadow">
                                               <h4 className="font-semibold text-slate-800 text-sm">{task.title}</h4>
                                               <p className="text-slate-500 text-xs mt-1">{task.description}</p>
                                               
                                               {/* Control de Transición de Estados */}
                                               <div className="flex justify-end space-x-1 mt-4">
                                                   {column.id !== 'todo' && (
                                                       <button
                                                           onClick={() => handleMoveTask(task.id, column.id === 'done' ? 'in_progress' : 'todo')}
                                                           className="px-2 py-1 bg-slate-100 hover:bg-slate-200 text-slate-700 text-[10px] font-semibold rounded transition-colors"
                                                       >
                                                           &larr; Retroceder
                                                       </button>
                                                   )}
                                                   {column.id !== 'done' && (
                                                       <button
                                                           onClick={() => handleMoveTask(task.id, column.id === 'todo' ? 'in_progress' : 'done')}
                                                           className="px-2 py-1 bg-indigo-50 hover:bg-indigo-100 text-indigo-700 text-[10px] font-semibold rounded transition-colors"
                                                       >
                                                           Avanzar &rarr;
                                                       </button>
                                                   )}
                                               </div>
                                           </div>
                                       ))}
                               </div>
                           </div>
                       ))}
                   </div>
               </div>
           </div>
       );
   }
   ```
7. Declara las rutas de la aplicación para unificar ambos mundos. Abre el archivo `routes/web.php` y reemplaza por completo su contenido:
   ```php
   <?php

   use App\Http\Controllers\DashboardController;
   use App\Http\Controllers\TaskController;
   use Illuminate\Support\Facades\Route;

   // Ruta Blade Clásica
   Route::get('/', [DashboardController::class, 'index'])->name('dashboard');

   // Rutas de SPA de Inertia
   Route::get('/tasks', [TaskController::class, 'index'])->name('tasks.index');
   Route::post('/tasks', [TaskController::class, 'store'])->name('tasks.store');
   Route::patch('/tasks/{task}/status', [TaskController::class, 'updateStatus'])->name('tasks.updateStatus');
   ```

**Resultado esperado**:
Un flujo completo donde las rutas de Blade conviven perfectamente en el mismo dominio con las rutas SPA manejadas por Inertia, compartiendo el mismo ciclo de vida de los datos de SQLite.

**Verificación**:
```bash
## Verificar el correcto mapeo de las rutas
php artisan route:list --path=tasks
```
*(Deberían aparecer las tres rutas asociadas a `TaskController` mapeando correctamente a los métodos `index`, `store` y `updateStatus`).*

---

### Paso 7: Pruebas de Integración Automatizadas con Pest
**Objetivo**: Instalar el framework de pruebas Pest, configurar el entorno de testing para usar SQLite en memoria e implementar pruebas que validen de manera estricta que ambos extremos de la arquitectura devuelven código HTTP de éxito y la estructura correcta de datos.

**Instrucciones**:
1. Instala e inicializa Pest PHP en el proyecto:
   ```bash
   composer require pestphp/pest:^2.34.0 pestphp/pest-plugin-laravel:^2.0 --dev
   ./vendor/bin/pest --init
   ```
   *(Selecciona los valores por defecto que asigne la terminal durante la instalación).*
2. Abre tu archivo `phpunit.xml` para asegurar que las pruebas usen SQLite en memoria. Asegúrate de configurar las siguientes variables de entorno dentro del bloque `<php>`:
   ```xml
   <env name="DB_CONNECTION" value="sqlite"/>
   <env name="DB_DATABASE" value=":memory:"/>
   ```
3. Crea un archivo de prueba de integración en `tests/Feature/TaskFlowTest.php`:
   ```bash
   mkdir -p tests/Feature
   touch tests/Feature/TaskFlowTest.php
   ```
4. Escribe el conjunto de pruebas que evalúen de manera estricta la carga de la página clásica de Blade, la carga síncrona de Inertia y la validación de envío de datos:
   ```php
   <?php

   use App\Models\Task;
   use Illuminate\Foundation\Testing\RefreshDatabase;

   uses(RefreshDatabase::class);

   test('el panel de blade carga con las estadisticas correctas', function () {
       // Datos ficticios de prueba
       Task::create(['title' => 'Tarea 1', 'description' => 'Desc 1', 'status' => 'todo']);
       Task::create(['title' => 'Tarea 2', 'description' => 'Desc 2', 'status' => 'done']);

       $response = $this->get('/');

       $response->assertStatus(200);
       $response->assertSee('TaskFlow Admin');
       // Validar que renderice el contador del dataset inyectado
       $response->assertSee('data-current-tasks="2"', false);
   });

   test('el tablero kanban de inertia carga los componentes correctos', function () {
       Task::create(['title' => 'Tarea Kanban 1', 'description' => 'Desc K1', 'status' => 'in_progress']);

       $response = $this->get('/tasks');

       $response->assertStatus(200);
       
       // Verificar que es una respuesta procesada de forma exitosa por Inertia
       $response->assertInertia(fn ($page) => $page
           ->component('Tasks/Index')
           ->has('tasks', 1)
           ->where('tasks.0.title', 'Tarea Kanban 1')
       );
   });

   test('se puede registrar una tarea de forma segura validando datos', function () {
       $payload = [
           'title' => 'Implementar Pest Tests',
           'description' => 'Verificar que la cobertura de pruebas cubra el código del frontend.',
           'status' => 'todo'
       ];

       $response = $this->post('/tasks', $payload);

       $response->assertRedirect('/tasks');
       $this->assertDatabaseHas('tasks', [
           'title' => 'Implementar Pest Tests'
       ]);
   });
   ```

**Resultado esperado**:
La suite de pruebas automatizadas compilará y ejecutará las aserciones validando de forma rigurosa la base de datos en memoria, el renderizado de la directiva Blade y el estado inyectado de Inertia.

**Verificación**:
Ejecuta la suite de pruebas desde la terminal:
```bash
./vendor/bin/pest
```

---

## Validación y Pruebas

Para garantizar que la integración full-stack funciona de forma integral y cumple con estándares estrictos de seguridad y control de fallas, realiza las siguientes pruebas.

### 1. Validación de Compilación de Assets
Compila los archivos listos para un despliegue de producción utilizando Vite para certificar que TypeScript, React 19 y Tailwind CSS v4 resuelven de manera estricta su árbol de dependencias sin discrepancias de tipos ni de variables ausentes:
```bash
npm run build
```
**Salida esperada de éxito**:
Vite listará la creación de archivos estáticos versionados dentro de `public/build/assets/` con sus respectivos hashes criptográficos e indicará la compilación exitosa del archivo `manifest.json`.

---

### 2. Casos Límite y Pruebas Adversarias (Seguridad y Resiliencia)

#### Caso Adversario A: Intento de Inyección XSS en Atributos de Datos de la Vista Blade
Un atacante malicioso intenta inyectar código Javascript malicioso (ataque XSS) a través del título de la tarea para intentar comprometer el renderizado en el navegador del usuario administrativo.
- **Acción**: Ejecuta en terminal la inserción manual de un payload en la base de datos de desarrollo:
  ```bash
  sqlite3 database/database.sqlite "INSERT INTO tasks (title, description, status, created_at, updated_at) VALUES ('<script>alert(\"XSS\")</script>', 'Inyección maliciosa', 'todo', datetime('now'), datetime('now'));"
  ```
- **Verificación**: Realiza una petición curl simulando un navegador cliente:
  ```bash
  curl -s http://localhost:8000/ | grep -o 'data-current-tasks="[^"]*"'
  ```
  *(La salida esperada de éxito de Laravel y el motor Blade es escapar de manera nativa los strings mediante caracteres HTML seguros como `&lt;script&gt;`, neutralizando el vector de ataque sin interferir con la estructura de variables del JS Vanilla).*

#### Caso Adversario B: Validación de Estructura de Tipos con Datos Vacíos o Incorrectos
Un cliente o atacante malicioso simula un payload incompleto enviado directamente al endpoint POST para evadir validaciones del cliente web en React.
- **Acción**: Envía una petición de inserción con campos requeridos ausentes mediante terminal:
  ```bash
  curl -X POST http://localhost:8000/tasks \
       -H "Content-Type: application/json" \
       -H "Accept: application/json" \
       -d '{"title": "", "status": "no_existe"}'
  ```
- **Resultado Esperado**: El backend interceptará de inmediato el payload, devolviendo un código de estado HTTP `422 Unprocessable Content` junto con el mapa detallado de errores de validación sin revelar trazas internas del framework ni fallos de base de datos.

---

## Solución de Problemas

A continuación se detallan los dos incidentes más comunes que ocurren durante la configuración de entornos híbridos de Laravel con Tailwind CSS v4 y React 19, junto con sus soluciones definitivas:

### Incidente 1: Errores de compilación de Tailwind CSS v4 en Vite debido a la presencia de dependencias de PostCSS obsoletas
- **Síntoma**: Al ejecutar `npm run dev` o `npm run build`, la terminal muestra un error indicando fallas en la lectura de archivos de configuración de estilo, o bien las directivas CSS de Tailwind no se renderizan y la página se muestra sin diseño de clases utilitarias.
- **Causa**: Tailwind CSS v4 utiliza un motor de compilación unificado llamado Lightning CSS e integra directamente la lógica en el plugin `@tailwindcss/vite`. Si existen archivos residuales en tu raíz como `postcss.config.js` o `tailwind.config.js` heredados de versiones anteriores o de skeletons por defecto de Laravel, el compilador entra en conflicto.
- **Solución**: 
  1. Elimina de manera definitiva los archivos de configuración obsoletos en la raíz:
     ```bash
     rm -f tailwind.config.js postcss.config.js
     ```
  2. Asegúrate de que tu archivo `resources/css/app.css` posea exclusivamente la directiva simplificada:
     ```css
     @import "tailwindcss";
     ```
  3. Detén e inicia nuevamente el servidor de desarrollo de Vite con `npm run dev`.

---

### Incidente 2: Error de Tipado Crítico "Cannot find module '@/*' or its corresponding type declarations" en archivos TSX de React
- **Síntoma**: Al ejecutar pruebas de tipos estáticos con `npx tsc --noEmit` o al abrir el proyecto en VS Code, la importación de scripts, hooks de Inertia o componentes falla visualmente con un error de TypeScript en la línea que lee: `import Index from '@/Pages/Tasks/Index'`.
- **Causa**: El compilador de TypeScript no cuenta con la directiva explícita de resolución de alias que mapea el prefijo `@` a la ruta absoluta de trabajo `resources/js/`.
- **Solución**:
  1. Abre el archivo `tsconfig.json` de la raíz.
  2. Confirma que la sección `compilerOptions` contiene el mapeo explícito de rutas:
     ```json
     "paths": {
         "@/*": ["resources/js/*"]
     }
     ```
  3. Asegúrate de que el plugin `vite.config.js` posea la resolución del alias del directorio idéntico utilizando `path.resolve`.

---

## Limpieza
Para regresar el espacio de trabajo a su estado inicial, detén cualquier proceso de background y elimina de manera segura los archivos transitorios generados:

1. Detén el servidor web de desarrollo y el bundle de desarrollo de Vite ejecutando `Ctrl+C` en las terminales activas.
2. Limpia los assets compilados localmente de producción para no interferir con otros proyectos:
   ```bash
   rm -rf public/build
   ```
3. Si deseas restaurar el repositorio Git y eliminar el laboratorio actual:
   ```bash
   cd ~/labs
   rm -rf taskflow-app
   ```

---

## Resumen
En este laboratorio práctico, has implementado exitosamente una arquitectura de renderizado híbrido de alta fidelidad, unificando en una sola aplicación Laravel la madurez del renderizado del servidor de Blade y el dinamismo interactivo de una SPA en React 19, todo potenciado por Vite 6.

### Conceptos Clave Consolidados:
- **Tailwind CSS v4** simplifica de forma notable el desarrollo de interfaces de usuario al eliminar archivos de configuración innecesarios, acelerando la velocidad de compilación a través de su arquitectura nativa basada en Lightning CSS.
- **Inertia.js** elimina la necesidad de crear APIs REST tradicionales complejas ni configurar tokens de autorización como Sanctum para el desarrollo interno de interfaces dinámicas, manteniendo el control de ruteo y validaciones centralizados en el backend de Laravel.
- **TypeScript y React 19** garantizan la robustez estructural en el frontend, reduciendo de manera drástica los errores de tipo y de estado dinámico del lado del cliente.
- **Pest PHP** ofrece una sintaxis expresiva y ágil para asegurar la calidad de software mediante pruebas automatizadas de extremo a extremo, facilitando la validación del modelo de datos e interactividad en un solo paso.

### Referencias Oficiales
- Documentación Oficial de Laravel: [laravel.com](https://laravel.com/docs)
- Repositorio y Guías de Inertia.js: [inertiajs.com](https://inertiajs.com)
- Guía de Migración de Tailwind CSS v4: [tailwindcss.com/docs/v4-beta](https://tailwindcss.com/docs/v4-beta)
- Framework de Pruebas Pest PHP: [pestphp.com](https://pestphp.com)
