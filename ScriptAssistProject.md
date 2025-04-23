
#Script Assist Project Plan, Structures and Elements
This will be a living document to track, document, and catpure all the elements, schemas, work, and code adjustments to the ScriptAssist Project.

##Database schema
The following is the database schema for Script Assist as of 4/4/25

**Begin Schema**
```markdown
# Database Schema Overview

This document outlines the database schema for the `script_assist` database. It includes the tables, their structure, and the relationships between them.

## 1. Core Entities and Their Relationships

### Clients
- **Table**: `client`
  - **Columns**:
    - `id`: INT (Primary Key)
    - `firstName`: VARCHAR(255)
    - `lastName`: VARCHAR(255)
    - `dob`: DATE
    - `phone`: VARCHAR(255)
    - `email`: VARCHAR(255)
    - `union`: VARCHAR(255)
    - `created_at`: TIMESTAMP
    - `updated_at`: TIMESTAMP
    - `marital_status`: INT (Foreign Key)
    - `notes`: TEXT
  - **Relationships**:
    - `marital_status` references `marital_status_lookup(id)`.
    - Related to `client_address`, `client_relationships`, and `referrals`.

### Agents
- **Table**: `agent`
  - **Columns**:
    - `id`: INT (Primary Key)
    - `firstName`: VARCHAR(255)
    - `lastName`: VARCHAR(255)
    - `email`: VARCHAR(255)
    - `password`: VARCHAR(255)
    - `phone`: VARCHAR(255)
    - `lastLogin`: TIMESTAMP
    - `createdAt`: DATETIME
    - `updatedAt`: DATETIME
    - `agency`: INT (Foreign Key)
    - `status`: INT (Foreign Key)
  - **Relationships**:
    - `agency` references `agency(id)`.
    - `status` references `agent_status_lookup(id)`.

### Agencies
- **Table**: `agency`
  - **Columns**:
    - `id`: INT (Primary Key)
    - `name`: VARCHAR(255)
    - `address`: VARCHAR(255)
    - `city`: VARCHAR(255)
    - `zip`: VARCHAR(255)
    - `phone`: VARCHAR(255)
    - `email`: VARCHAR(255)
    - `website`: VARCHAR(255)
    - `industry`: VARCHAR(255)
    - `active`: TINYINT(1)
    - `dateCreated`: TIMESTAMP
    - `lastUpdated`: TIMESTAMP
    - `state`: INT (Foreign Key)
  - **Relationships**:
    - `state` references `stateLookup(id)`.

## 2. Address Management

### Addresses
- **Table**: `address`
  - **Columns**:
    - `id`: INT (Primary Key)
    - `streetAddress`: VARCHAR(255)
    - `aptSuite`: VARCHAR(255)
    - `city`: VARCHAR(255)
    - `state`: INT (Foreign Key)
    - `zipCode`: VARCHAR(20)
    - `createdAt`: TIMESTAMP
    - `lastUpdated`: TIMESTAMP
  - **Relationships**:
    - `state` references `stateLookup(id)`.

### Client Addresses
- **Table**: `client_address`
  - **Columns**:
    - `id`: INT (Primary Key)
    - `clientId`: INT (Foreign Key)
    - `addressId`: INT (Foreign Key)
    - `addressType`: INT (Foreign Key)
    - `isPrimary`: TINYINT(1)
  - **Relationships**:
    - `clientId` references `client(id)`.
    - `addressId` references `address(id)`.

## 3. Lookup Tables

### State Lookup
- **Table**: `stateLookup`
  - **Columns**:
    - `id`: INT (Primary Key)
    - `stateCode`: VARCHAR(2)
    - `stateName`: VARCHAR(50)

### Marital Status Lookup
- **Table**: `marital_status_lookup`
  - **Columns**:
    - `id`: INT (Primary Key)
    - `maritalStatus`: VARCHAR(255)

### Agent Status Lookup
- **Table**: `agent_status_lookup`
  - **Columns**:
    - `id`: INT (Primary Key)
    - `status`: VARCHAR(50)

## 4. Scripts and Presentations

### Scripts
- **Table**: `script`
  - **Columns**:
    - `id`: INT (Primary Key)
    - `name`: VARCHAR(255)
    - `description`: VARCHAR(255)
    - `agencyID`: INT (Foreign Key)
    - `active`: TINYINT(1)
    - `version`: INT
    - `createdAt`: TIMESTAMP
    - `updatedAt`: TIMESTAMP
  - **Relationships**:
    - `agencyID` references `agency(id)`.

### Presentations
- **Table**: `presentations`
  - **Columns**:
    - `id`: INT (Primary Key)
    - `scriptId`: INT (Foreign Key)
    - `createdAt`: TIMESTAMP
    - `updatedAt`: TIMESTAMP
    - `clientID`: INT (Foreign Key)
    - `selected_sections`: JSON
  - **Relationships**:
    - `scriptId` references `script(id)`.
    - `clientID` references `client(id)`.

### Sales Presentations
- **Table**: `sales_presentation`
  - **Columns**:
    - `id`: BIGINT (Primary Key)
    - `presentation_id`: INT (Foreign Key)
    - `agent_id`: INT (Foreign Key)
    - `client_id`: INT (Foreign Key)
    - `outcome_id`: BIGINT (Foreign Key)
    - `start_time`: TIMESTAMP
    - `end_time`: TIMESTAMP
    - `notes`: TEXT
    - `created_at`: TIMESTAMP
    - `updated_at`: TIMESTAMP
  - **Relationships**:
    - `presentation_id` references `presentations(id)`.
    - `agent_id` references `agent(id)`.
    - `client_id` references `client(id)`.
    - `outcome_id` references `presentation_outcome_lookup(id)`.

## 5. Relationships Between Clients

### Client Relationships
- **Table**: `client_relationships`
  - **Columns**:
    - `id`: INT (Primary Key)
    - `client_id_1`: INT (Foreign Key)
    - `client_id_2`: INT (Foreign Key)
    - `relationship_type_id`: INT (Foreign Key)
  - **Relationships**:
    - `client_id_1` references `client(id)`.
    - `client_id_2` references `client(id)`.
    - `relationship_type_id` references `relationship_type_lookup(id)`.

### Referrals
- **Table**: `referrals`
  - **Columns**:
    - `id`: INT (Primary Key)
    - `clientId`: INT (Foreign Key)
    - `firstName`: VARCHAR(255)
    - `lastName`: VARCHAR(255)
    - `relationshipTypeID`: INT (Foreign Key)
    - `phone`: VARCHAR(255)
    - `email`: VARCHAR(255)
    - `createdAt`: TIMESTAMP
    - `notes`: TEXT
    - `lastUpdated`: TIMESTAMP
  - **Relationships**:
    - `clientId` references `client(id)`.
    - `relationshipTypeID` references `relationship_type_lookup(id)`.

## 6. Miscellaneous Tables

### Failed Jobs
- **Table**: `failed_jobs`
  - **Columns**:
    - `id`: BIGINT (Primary Key)
    - `uuid`: VARCHAR(255)
    - `connection`: TEXT
    - `queue`: TEXT
    - `payload`: LONGTEXT
    - `exception`: LONGTEXT
    - `failed_at`: TIMESTAMP

### Migrations
- **Table**: `migrations`
  - **Columns**:
    - `id`: INT (Primary Key)
    - `migration`: VARCHAR(255)
    - `batch`: INT

### Password Reset Tokens
- **Table**: `password_reset_tokens`
  - **Columns**:
    - `email`: VARCHAR(255) (Primary Key)
    - `token`: VARCHAR(255)
    - `created_at`: TIMESTAMP

### Personal Access Tokens
- **Table**: `personal_access_tokens`
  - **Columns**:
    - `id`: BIGINT (Primary Key)
    - `tokenable_type`: VARCHAR(255)
    - `tokenable_id`: BIGINT
    - `name`: VARCHAR(255)
    - `token`: VARCHAR(64)
    - `abilities`: TEXT
    - `last_used_at`: TIMESTAMP
    - `expires_at`: TIMESTAMP
    - `created_at`: TIMESTAMP
    - `updated_at`: TIMESTAMP

### Lookup Types
- **Table**: `presentationType`
  - **Columns**:
    - `id`: INT (Primary Key)
    - `typeName`: VARCHAR(255)
    - `agencyId`: INT (Foreign Key)

### Presentation Outcomes
- **Table**: `presentation_outcome_lookup`
  - **Columns**:
    - `id`: BIGINT (Primary Key)
    - `outcome_name`: VARCHAR(255)
    - `created_at`: TIMESTAMP
    - `updated_at`: TIMESTAMP

### Scripts Content
- **Table**: `scriptContent`
  - **Columns**:
    - `id`: INT (Primary Key)
    - `scriptLine`: MEDIUMTEXT
    - `linked_slide_id`: INT (Foreign Key)
    - `lineSortOrder
```


## Tech Stack

Script Assist is being written in Laravel with Blade Templates in VS Code and run out of a docker container with mysql, php, and all supported elements.

*Dockerfile*
 Dockerfile for PHP Application This Dockerfile sets up a PHP 8.2 environment with necessary extensions and Composer. ## Base Image ```dockerfile FROM php:8.2-fpm
//RUN apt-get update && apt-get install -y \ 
libpng-dev \ 
libonig-dev \ 
libxml2-dev \ 
zip \ unzip \ 
curl \ git \ 
default-mysql-client \ 
&& docker-php-ext-install pdo pdo_mysql
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer  
WORKDIR /var/www

## Docker-compose.yml ##
```markdown
# Docker Compose Configuration

This document outlines the configuration for the Docker Compose setup for the `script_assist` application.

```yaml
services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: script_assist_app
    working_dir: /var/www
    volumes:
      - .:/var/www
    networks:
      - script_assist_network
    depends_on:
      - mysql
    environment:
      - DB_HOST=mysql
      - DB_DATABASE=script_assist
      - DB_USERNAME=root
      - DB_PASSWORD=yourpassword

  nginx:
    image: nginx:latest
    container_name: script_assist_nginx
    ports:
      - "8000:80"
    volumes:
      - .:/var/www
      - ./docker/nginx/default.conf:/etc/nginx/conf.d/default.conf
    networks:
      - script_assist_network
    depends_on:
      - app

  mysql:
    image: mysql:8
    container_name: script_assist_mysql
    restart: always
    environment:
      MYSQL_DATABASE: script_assist
      MYSQL_ROOT_PASSWORD: yourpassword
    ports:
      - "3306:3306"
    networks:
      - script_assist_network
    volumes:
      - mysql_data:/var/lib/mysql

networks:
  script_assist_network:

volumes:
  mysql_data:
```

### .htaccess File

This `.htaccess` file is used for configuring URL rewriting and other server settings in an Apache web server environment.

```apache
<IfModule mod_rewrite.c>
    <IfModule mod_negotiation.c>
        Options -MultiViews -Indexes
    </IfModule>

    RewriteEngine On

    # Handle Authorization Header
    RewriteCond %{HTTP:Authorization} .
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

    # Redirect Trailing Slashes If Not A Folder...
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_URI} (.+)/$
    RewriteRule ^ %1 [L,R=301]

    # Send Requests To Front Controller...
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [L]
</IfModule>
** .env file **
```markdown
# .env File

This `.env` file contains environment-specific configuration settings for a Laravel application.

```env
APP_NAME=Laravel
APP_ENV=local
APP_KEY=base64:HN+SACOVDNflFqRIlIsqS66ObhyjV+te0M3mvgGr+w4=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=script_assist
DB_USERNAME=root
DB_PASSWORD=yourpassword

BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DISK=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MEMCACHED_HOST=127.0.0.1

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_HOST=
PUSHER_PORT=443
PUSHER_SCHEME=https
PUSHER_APP_CLUSTER=mt1

VITE_APP_NAME="${APP_NAME}"
VITE_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
VITE_PUSHER_HOST="${PUSHER_HOST}"
VITE_PUSHER_PORT="${PUSHER_PORT}"
VITE_PUSHER_SCHEME="${PUSHER_SCHEME}"
VITE_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"
```

## Routes ##

### api.php ##
```php
<?php

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;

/*
|--------------------------------------------------------------------------
| API Routes
|--------------------------------------------------------------------------
|
| Here is where you can register API routes for your application. These
| routes are loaded by the RouteServiceProvider and all of them will
| be assigned to the "api" middleware group. Make something great!
|
*/

Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
    return $request->user();
});
```
### Web.php ###
```php
<?php

use Illuminate\Support\Facades\Route;

/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider and all of them will
| be assigned to the "web" middleware group. Make something great!
|
*/

Route::get('/', function () {
    return view('welcome');
});

use App\Http\Controllers\ScriptController;

```

## # Web-simplified.php ###

```php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\ScriptBuilderController;

Route::get('/', function () {
    return view('welcome');
});

// Test static route to make sure Laravel is serving anything custom
Route::get('/test', function () {
    return 'This test route works!';
});

// Core test route to confirm controller is being called
Route::get('/script-builder', [ScriptBuilderController::class, 'index'])->name('scriptBuilder.index');
```

### Console.php ###
```php
<?php

use Illuminate\Foundation\Inspiring;
use Illuminate\Support\Facades\Artisan;

/*
|--------------------------------------------------------------------------
| Console Routes
|--------------------------------------------------------------------------
|
| This file is where you may define all of your Closure based console
| commands. Each Closure is bound to a command instance allowing a
| simple approach to interacting with each command's IO methods.
|
*/

Artisan::command('inspire', function () {
    $this->comment(Inspiring::quote());
})->purpose('Display an inspiring quote');
```

###  Channels.php
```markdown
# Project Summary Document

This document provides an overview of the routing and broadcasting configurations used in the `script_assist` project.

## Broadcast Channels Configuration (`channels.php`)

The following code defines the broadcast channels for the application:

```php
<?php

use Illuminate\Support\Facades\Broadcast;

/*
|--------------------------------------------------------------------------
| Broadcast Channels
|--------------------------------------------------------------------------
|
| Here you may register all of the event broadcasting channels that your
| application supports. The given channel authorization callbacks are
| used to check if an authenticated user can listen to the channel.
|
*/

Broadcast::channel('App.Models.User.{id}', function ($user, $id) {
    return (int) $user->id === (int) $id;
});
```
## Blade Templates

/views/layouts/blade.php
```markdown
# Blade Templates Summary

This document provides an overview of the Blade templates used in the `script_assist` project. Each template is listed with its corresponding file path and content.

---

## `/views/layouts/app.blade.php`

```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@yield('title', 'Script Assist')</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
    @yield('head') <!-- This will allow child views to add additional styles -->
</head>
<body>
    <div class="container">
        @yield('content')
    </div>

     <!-- Bootstrap JS and Popper.js -->
     <script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.11.6/dist/umd/popper.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.min.js"></script>

    @yield('scripts') <!-- Allows for additional JS in child views -->
</body>
</html>
```
 `/views/layouts/presentation/show.blade.php`
```php
@extends('layouts.app')

@section('content')
<div class="container-fluid">
    <div class="row" style="height: 90vh;">

        {{-- Left Column: Slides (30%) --}}
        <div class="col-md-3 border-end bg-light">
            <h5 class="mt-3">Slides</h5>
            {{-- Placeholder for now --}}
            <div id="slideDisplay" class="mt-3">
                <p>Slide content will go here.</p>
            </div>
        </div>

        {{-- Center Column: Script (40%) --}}
        <div class="col-md-6 d-flex flex-column">
            <div class="py-3 border-bottom">
                <h5 class="mb-3">Script</h5>
                <select id="sectionSelect" class="form-select" onchange="navigateToSection(this.value)">
                    @foreach($sections as $section)
                        <option value="{{ $section->id }}" {{ $selectedSectionId == $section->id ? 'selected' : '' }}>
                            {{ $section->sectionName }}
                        </option>
                    @endforeach
                </select>
            </div>

            <div id="scriptContainer" class="flex-grow-1 overflow-auto py-3">
                <div id="scriptLines">
                    @foreach($scriptContent as $line)
                        <div class="script-line py-2 border-bottom">{{ $line->scriptLine }}</div>
                    @endforeach
                </div>
            </div>
        </div>

        {{-- Right Column: Tools (30%) --}}
        <div class="col-md-3 border-start bg-light">
            <h5 class="mt-3">Tools</h5>
            {{-- Placeholder --}}
            <p class="mt-3">Agent tools and notes will go here.</p>
        </div>

    </div>
</div>

<script>
    function navigateToSection(sectionId) {
        const url = new URL(window.location.href);
        url.searchParams.set('section', sectionId);
        window.location.href = url.toString();
    }

    let currentLineIndex = 0;

    function scrollToActiveLine() {
        const lines = document.querySelectorAll('.script-line');
        lines.forEach(line => line.classList.remove('active'));
        const active = lines[currentLineIndex];
        if (active) {
            active.classList.add('active');
            active.scrollIntoView({ behavior: 'smooth', block: 'start' });
        }
    }

    document.addEventListener('DOMContentLoaded', () => {
        scrollToActiveLine(); // Initial highlight

        document.addEventListener('keydown', function (e) {
            const lines = document.querySelectorAll('.script-line');

            if (e.key === 'ArrowDown') {
                if (currentLineIndex < lines.length - 1) {
                    currentLineIndex++;
                    scrollToActiveLine();
                }
            }

            if (e.key === 'ArrowUp') {
                if (currentLineIndex > 0) {
                    currentLineIndex--;
                    scrollToActiveLine();
                }
            }

            if (e.key === 'ArrowRight' || e.key === 'ArrowLeft') {
                const sectionSelect = document.getElementById('sectionSelect');
                const options = Array.from(sectionSelect.options);
                let currentIndex = options.findIndex(opt => opt.selected);

                if (e.key === 'ArrowRight' && currentIndex < options.length - 1) {
                    sectionSelect.selectedIndex = currentIndex + 1;
                    sectionSelect.dispatchEvent(new Event('change'));
                }

                if (e.key === 'ArrowLeft' && currentIndex > 0) {
                    sectionSelect.selectedIndex = currentIndex - 1;
                    sectionSelect.dispatchEvent(new Event('change'));
                }
            }
        });
    });
</script>
@endsection
```
 `/views/layouts/script/show.blade.php`
```php
@extends('layouts.app')  {{-- Extends the main layout (if using one) --}}

@section('content')
<div class="container-fluid">
    <div class="row">
        <div class="col-md-3"></div> <!-- Left empty column -->

        <div class="col-md-6">
            <div class="card shadow p-4">
                <h2 class="text-center">Script Content</h2>
                <hr>
                 <!-- Section Dropdown -->
                 <form method="GET" action="">
                    <label for="section">Filter by Section:</label>
                    <select name="section" id="section" class="form-select" onchange="this.form.submit()">
                        <option value="all" {{ $selectedSection == 'all' ? 'selected' : '' }}>All Sections</option>
                        @foreach ($sections as $section)
                            <option value="{{ $section->id }}" {{ $selectedSection == $section->id ? 'selected' : '' }}>
                                {{ $section->sectionName }}
                            </option>
                        @endforeach
                    </select>
                </form>
                @if($scriptContent->isEmpty())
                    <p class="text-center">No content available for this script.</p>
                @else
                    @foreach ($scriptContent as $line)
                        <p>{{ $line->scriptLine }}</p>
                    @endforeach
                @endif
            </div>
        </div>

        <div class="col-md-3"></div> <!-- Right empty column -->
    </div>
</div>
@endsection
```

 `/views/layouts/customer-slideshow.blade.php`

```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Customer Slide View</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css">
    <style>
        .slide-enter {
            opacity: 0;
            transition: opacity 0.5s ease-in-out;
        }

        .slide-enter-active {
            opacity: 1;
        }
    </style>
</head>
<body class="bg-black text-white">
    <div id="slide-container" class="w-full h-screen flex items-center justify-center">
        <!-- Slide will be injected here -->
    </div>

    <script>
    const customerChannel = new BroadcastChannel('slide-sync');

    customerChannel.onmessage = (event) => {
        if (event.data.type === 'UPDATE_SLIDE') {
            renderSlide({
                slideContent: event.data.slideContent,
                transition_type: 'fade', // You can make this dynamic later
                transition_duration: 500
            });
        }
    };

    function renderSlide(slide) {
        const container = document.getElementById('slide-container');
        const wrapper = document.createElement('div');
        wrapper.classList.add('slide-enter');
        wrapper.innerHTML = slide.slideContent;

        container.innerHTML = '';
        container.appendChild(wrapper);

        setTimeout(() => {
            wrapper.classList.add('slide-enter-active');
        }, 50);
    }
</script>

</body>
</html>
```
 `/views/layouts/demo.blade.php`
```php
@extends('layouts.app')

@section('head')
    <link rel="stylesheet" href="{{ asset('css/dark.css') }}">
@endsection

@section('content')
<div class="container-fluid" style="width: 90%; max-width: 1200px; margin: 0 auto;">
    <div class="row" style="height: 90vh;">
        {{-- Left Column: Slide Preview --}}
        <div class="col-md-3 p-4 bg-dark border-end border-secondary d-flex flex-column">
            <h2 class="text-white mb-3">Slide Preview</h2>
            @if (!empty($currentSlide) && !empty($currentSlide->slideContent))
                <div class="bg-black border border-secondary rounded-lg overflow-hidden p-2 mb-4">
                    {!! $currentSlide->slideContent !!}
                </div>
            @else
                <p class="text-light">No slide assigned to this line.</p>
            @endif
        </div>
        {{-- Center Column: Script --}}
        <div class="col-md-6 d-flex flex-column" style="
```

## Project Overview

### What does ScriptAssist Do?
ScriptAssist is an onine tool designed to help phone/video confrerence sales agents keep track of their scripts, presentations, and client information.  It's there to help them caputre client information, gather referrals, get the data required to fill out the proper applications, and do all this in a single clean interface.

### Flows
Agents log in and are attached to agency (set at account creation)
Clients are stored in clients table.
Spouses and children are stored in clients table using a relationship lookup table.
Referrals are stored in the referrals table linked by client key.

Scripts are attache dto agency
Scripts can have multiple versions
Each version can have multiple sections
Each section can have multiple lines
Each line can have 1 slide associated to it.

Each presentation can be tied to a single scripts (via script id)
Each presentation can have multiple slides.
Each slide is attached to one line based on  sldieID in the line table.

### Functions

Script Assist has the following stages.
1) Script assembly: Agent would sign into the project and pick the type of script they need to do, choose the elements (sections) that would be included, and enter the client information.  Once those steps are done the application does the following:

1) Pulls the script from the database based on the id of the choice.
2) Pulls out the sections based on the script ID
3) Pulls out the slides for the presentation based on the script ID and the section ID
4) Pulls out the script content (lines) based on the scriptID and the section ID.
5) Script is assembled and presented to the agent by section (in order determined by the sort order of the selected sections.
6) Slide show is loaded in the background and is synced to the script content and the section (each line can have a slide ID attached to it) changing the line via keyboard navigation would change the slide.
7) A customer slide show window would be opend full screen that the agent could then share to the customer via online meeting app (zoom).
8) a thumbnail view of the slides is shown on the main presentation page in a left most column so the agent can see what slide is currently being shown.
9) Agent uses arrow keys to navigate through the script (up arrow/down arrow advances the script line by line with the topmost line always being the current line so the agent doesn't have to move their eyes from the camera to see what they are looking at.
10) left right arrow keys move section by section through the script sections (loading the appropriate lines and slides based on the active section.


### File Structure
* generated by 'tree' command at the project root
Folder PATH listing for volume vol1
Volume serial number is A463-13B8
E:.
ª   .editorconfig
ª   .env
ª   .env.example
ª   .gitattributes
ª   .gitignore
ª   artisan
ª   composer.json
ª   composer.lock
ª   docker-compose.yml
ª   Dockerfile
ª   Dockerfile_old
ª   filestructure.txt
ª   package.json
ª   phpunit.xml
ª   README.md
ª   vite.config.js
ª   
+---app
ª   +---Console
ª   ª       Kernel.php
ª   ª       
ª   +---Exceptions
ª   ª       Handler.php
ª   ª       
ª   +---Http
ª   ª   ª   Kernel.php
ª   ª   ª   
ª   ª   +---Controllers
ª   ª   ª       Controller.php
ª   ª   ª       DemoController.php
ª   ª   ª       PresentationController.php
ª   ª   ª       ScriptbuilderController.php
ª   ª   ª       ScriptbuilderController_simplifed.php
ª   ª   ª       ScriptController.php
ª   ª   ª       
ª   ª   +---Middleware
ª   ª           Authenticate.php
ª   ª           EncryptCookies.php
ª   ª           PreventRequestsDuringMaintenance.php
ª   ª           RedirectIfAuthenticated.php
ª   ª           TrimStrings.php
ª   ª           TrustHosts.php
ª   ª           TrustProxies.php
ª   ª           ValidateSignature.php
ª   ª           VerifyCsrfToken.php
ª   ª           
ª   +---Models
ª   ª       MaritalStatusLookup.php
ª   ª       Presentation.php
ª   ª       Rebuttal.php
ª   ª       Script.php
ª   ª       ScriptContent.php
ª   ª       Section.php
ª   ª       Slide.php
ª   ª       StateLookup.php
ª   ª       User.php
ª   ª       
ª   +---Providers
ª           AppServiceProvider.php
ª           AuthServiceProvider.php
ª           BroadcastServiceProvider.php
ª           EventServiceProvider.php
ª           RouteServiceProvider.php
ª           
+---bootstrap
ª   ª   app.php
ª   ª   
ª   +---cache
ª           .gitignore
ª           packages.php
ª           services.php
ª           
+---config
ª       app.php
ª       auth.php
ª       broadcasting.php
ª       cache.php
ª       cors.php
ª       database.php
ª       filesystems.php
ª       hashing.php
ª       logging.php
ª       mail.php
ª       queue.php
ª       sanctum.php
ª       services.php
ª       session.php
ª       view.php
ª       
+---database
ª   ª   .gitignore
ª   ª   
ª   +---factories
ª   ª       UserFactory.php
ª   ª       
ª   +---migrations
ª   ª       2014_10_12_000000_create_users_table.php
ª   ª       2014_10_12_100000_create_password_reset_tokens_table.php
ª   ª       2019_08_19_000000_create_failed_jobs_table.php
ª   ª       2019_12_14_000001_create_personal_access_tokens_table.php
ª   ª       2025_03_27_010907_add_union_to_client_table.php
ª   ª       2025_03_27_134617_create_presentation_outcome_lookup_table.php
ª   ª       2025_03_27_134733_create_sales_presentation_table.php
ª   ª       2025_03_27_155734_add_selected_sections_to_presentations_table.php
ª   ª       2025_03_31_030829_update_slides_table_for_transition_and_sync.php
ª   ª       2025_03_31_030943_add_linked_slide_id_to_script_content.php
ª   ª       2025_03_31_031118_create_section_slides_table.php
ª   ª       
ª   +---seeders
ª           DatabaseSeeder.php
ª           
+---docker
ª   +---nginx
ª           default.conf
ª           
+---public
ª   ª   .htaccess
ª   ª   favicon.ico
ª   ª   index.php
ª   ª   robots.txt
ª   ª   
ª   +---css
ª           app.css
ª           dark.css
ª           
+---resources
ª   +---js
ª   ª       app.js
ª   ª       bootstrap.js
ª   ª       
ª   +---views
ª       ª   customer-slideshow.blade.php
ª       ª   demo.blade.php
ª       ª   script-builder.blade.php
ª       ª   welcome.blade.php
ª       ª   
ª       +---layouts
ª       ª       app.blade.php
ª       ª       
ª       +---presentation
ª       ª       show.blade.php
ª       ª       
ª       +---script
ª               show.blade.php
ª               
+---routes
ª       api.php
ª       channels.php
ª       console.php
ª       web.php
ª       web_simplified.php
ª       
+---storage
ª   +---app
ª   ª   ª   .gitignore
ª   ª   ª   
ª   ª   +---public
ª   ª           .gitignore
ª   ª           
ª   +---framework
ª   ª   ª   .gitignore
ª   ª   ª   
ª   ª   +---cache
ª   ª   ª   ª   .gitignore
ª   ª   ª   ª   
ª   ª   ª   +---data
ª   ª   ª           .gitignore
ª   ª   ª           
ª   ª   +---sessions
ª   ª   ª       .gitignore
ª   ª   ª       11OlMstQcU7rbaS2hSJWingiYL5UMqctl9wYusZl
ª   ª   ª       5Bptsl9NsfqyYBuR8ofNwQnlRAcAAwHpB3P2eFNC
ª   ª   ª       NEXc9ZJ03oO2qbzQPFx4aqnFIlAxBfidHn2BmC3F
ª   ª   ª       O8ZYsLVIuaI7yzlEiljdbyRF1LM6p9byP3RG6G1x
ª   ª   ª       
ª   ª   +---testing
ª   ª   ª       .gitignore
ª   ª   ª       
ª   ª   +---views
ª   ª           .gitignore
ª   ª           2766c2d165881ca598ac5cd1631e6dd7.php
ª   ª           37422ffb80460090c907197a73241c7b.php
ª   ª           75a9795721a1038122d609915c45e6f8.php
ª   ª           c79b03b44b206c900eabdbea4cd7a6ca.php
ª   ª           cc787b33c682cd113f6cebf7b0ed5393.php
ª   ª           dbb84313ac11f5807c35103f5e3b5b9a.php
ª   ª           
ª   +---logs
ª           .gitignore
ª           laravel.log
ª           
+---tests
ª   ª   CreatesApplication.php
ª   ª   TestCase.php
ª   ª   
ª   +---Feature
ª   ª       ExampleTest.php
ª   ª       
ª   +---Unit
ª           ExampleTest.php
ª           
+---vendor
    ª   autoload.php
    ª   
    +---bin
    ª       carbon
    ª       patch-type-declarations
    ª       php-parse
    ª       phpunit
    ª       pint
    ª       psysh
    ª       sail
    ª       var-dump-server
    ª       yaml-lint
    ª       
    +---brick
    ª   +---math
    ª       ª   CHANGELOG.md
    ª       ª   composer.json
    ª       ª   LICENSE
    ª       ª   psalm-baseline.xml
    ª       ª   
    ª       +---src
    ª           ª   BigDecimal.php
    ª           ª   BigInteger.php
    ª           ª   BigNumber.php
    ª           ª   BigRational.php
    ª           ª   RoundingMode.php
    ª           ª   
    ª           +---Exception
    ª           ª       DivisionByZeroException.php
    ª           ª       IntegerOverflowException.php
    ª           ª       MathException.php
    ª           ª       NegativeNumberException.php
    ª           ª       NumberFormatException.php
    ª           ª       RoundingNecessaryException.php
    ª           ª       
    ª           +---Internal
    ª               ª   Calculator.php
    ª               ª   
    ª               +---Calculator
    ª                       BcMathCalculator.php
    ª                       GmpCalculator.php
    ª                       NativeCalculator.php
    ª                       
    +---carbonphp
    ª   +---carbon-doctrine-types
    ª       ª   composer.json
    ª       ª   LICENSE
    ª       ª   README.md
    ª       ª   
    ª       +---src
    ª           +---Carbon
    ª               +---Doctrine
    ª                       CarbonDoctrineType.php
    ª                       CarbonImmutableType.php
    ª                       CarbonType.php
    ª                       CarbonTypeConverter.php
    ª                       DateTimeDefaultPrecision.php
    ª                       DateTimeImmutableType.php
    ª                       DateTimeType.php
    ª                       
    +---composer
    ª       autoload_classmap.php
    ª       autoload_files.php
    ª       autoload_namespaces.php
    ª       autoload_psr4.php
    ª       autoload_real.php
    ª       autoload_static.php
    ª       ClassLoader.php
    ª       installed.json
    ª       installed.php
    ª       InstalledVersions.php
    ª       LICENSE
    ª       platform_check.php
    ª       
    +---dflydev
    ª   +---dot-access-data
    ª       ª   CHANGELOG.md
    ª       ª   composer.json
    ª       ª   LICENSE
    ª       ª   README.md
    ª       ª   
    ª       +---src
    ª           ª   Data.php
    ª           ª   DataInterface.php
    ª           ª   Util.php
    ª           ª   
    ª           +---Exception
    ª                   DataException.php
    ª                   InvalidPathException.php
    ª                   MissingPathException.php
    ª                   
    +---doctrine
    ª   +---inflector
    ª   ª   ª   composer.json
    ª   ª   ª   LICENSE
    ª   ª   ª   README.md
    ª   ª   ª   
    ª   ª   +---docs
    ª   ª   ª   +---en
    ª   ª   ª           index.rst
    ª   ª   ª           
    ª   ª   +---lib
    ª   ª       +---Doctrine
    ª   ª           +---Inflector
    ª   ª               ª   CachedWordInflector.php
    ª   ª               ª   GenericLanguageInflectorFactory.php
    ª   ª               ª   Inflector.php
    ª   ª               ª   InflectorFactory.php
    ª   ª               ª   Language.php
    ª   ª               ª   LanguageInflectorFactory.php
    ª   ª               ª   NoopWordInflector.php
    ª   ª               ª   RulesetInflector.php
    ª   ª               ª   WordInflector.php
    ª   ª               ª   
    ª   ª               +---Rules
    ª   ª                   ª   Pattern.php
    ª   ª                   ª   Patterns.php
    ª   ª                   ª   Ruleset.php
    ª   ª                   ª   Substitution.php
    ª   ª                   ª   Substitutions.php
    ª   ª                   ª   Transformation.php
    ª   ª                   ª   Transformations.php
    ª   ª                   ª   Word.php
    ª   ª                   ª   
    ª   ª                   +---English
    ª   ª                   ª       Inflectible.php
    ª   ª                   ª       InflectorFactory.php
    ª   ª                   ª       Rules.php
    ª   ª                   ª       Uninflected.php
    ª   ª                   ª       
    ª   ª                   +---French
    ª   ª                   ª       Inflectible.php
    ª   ª                   ª       InflectorFactory.php
    ª   ª                   ª       Rules.php
    ª   ª                   ª       Uninflected.php
    ª   ª                   ª       
    ª   ª                   +---NorwegianBokmal
    ª   ª                   ª       Inflectible.php
    ª   ª                   ª       InflectorFactory.php
    ª   ª                   ª       Rules.php
    ª   ª                   ª       Uninflected.php
    ª   ª                   ª       
    ª   ª                   +---Portuguese
    ª   ª                   ª       Inflectible.php
    ª   ª                   ª       InflectorFactory.php
    ª   ª                   ª       Rules.php
    ª   ª                   ª       Uninflected.php
    ª   ª                   ª       
    ª   ª                   +---Spanish
    ª   ª                   ª       Inflectible.php
    ª   ª                   ª       InflectorFactory.php
    ª   ª                   ª       Rules.php
    ª   ª                   ª       Uninflected.php
    ª   ª                   ª       
    ª   ª                   +---Turkish
    ª   ª                           Inflectible.php
    ª   ª                           InflectorFactory.php
    ª   ª                           Rules.php
    ª   ª                           Uninflected.php
    ª   ª                           
    ª   +---lexer
    ª       ª   composer.json
    ª       ª   LICENSE
    ª       ª   README.md
    ª       ª   UPGRADE.md
    ª       ª   
    ª       +---src
    ª               AbstractLexer.php
    ª               Token.php
    ª               
    +---dragonmantank
    ª   +---cron-expression
    ª       ª   CHANGELOG.md
    ª       ª   composer.json
    ª       ª   LICENSE
    ª       ª   README.md
    ª       ª   
    ª       +---src
    ª           +---Cron
    ª                   AbstractField.php
    ª                   CronExpression.php
    ª                   DayOfMonthField.php
    ª                   DayOfWeekField.php
    ª                   FieldFactory.php
    ª                   FieldFactoryInterface.php
    ª                   FieldInterface.php
    ª                   HoursField.php
    ª                   MinutesField.php
    ª                   MonthField.php
    ª                   
    +---egulias
    ª   +---email-validator
    ª       ª   composer.json
    ª       ª   CONTRIBUTING.md
    ª       ª   LICENSE
    ª       ª   
    ª       +---src
    ª           ª   EmailLexer.php
    ª           ª   EmailParser.php
    ª           ª   EmailValidator.php
    ª           ª   MessageIDParser.php
    ª           ª   Parser.php
    ª           ª   
    ª           +---Parser
    ª           ª   ª   Comment.php
    ª           ª   ª   DomainLiteral.php
    ª           ª   ª   DomainPart.php
    ª           ª   ª   DoubleQuote.php
    ª           ª   ª   FoldingWhiteSpace.php
    ª           ª   ª   IDLeftPart.php
    ª           ª   ª   IDRightPart.php
    ª           ª   ª   LocalPart.php
    ª           ª   ª   PartParser.php
    ª           ª   ª   
    ª           ª   +---CommentStrategy
    ª           ª           CommentStrategy.php
    ª           ª           DomainComment.php
    ª           ª           LocalComment.php
    ª           ª           
    ª           +---Result
    ª           ª   ª   InvalidEmail.php
    ª           ª   ª   MultipleErrors.php
    ª           ª   ª   Result.php
    ª           ª   ª   SpoofEmail.php
    ª           ª   ª   ValidEmail.php
    ª           ª   ª   
    ª           ª   +---Reason
    ª           ª           AtextAfterCFWS.php
    ª           ª           CharNotAllowed.php
    ª           ª           CommaInDomain.php
    ª           ª           CommentsInIDRight.php
    ª           ª           ConsecutiveAt.php
    ª           ª           ConsecutiveDot.php
    ª           ª           CRLFAtTheEnd.php
    ª           ª           CRLFX2.php
    ª           ª           CRNoLF.php
    ª           ª           DetailedReason.php
    ª           ª           DomainAcceptsNoMail.php
    ª           ª           DomainHyphened.php
    ª           ª           DomainTooLong.php
    ª           ª           DotAtEnd.php
    ª           ª           DotAtStart.php
    ª           ª           EmptyReason.php
    ª           ª           ExceptionFound.php
    ª           ª           ExpectingATEXT.php
    ª           ª           ExpectingCTEXT.php
    ª           ª           ExpectingDomainLiteralClose.php
    ª           ª           ExpectingDTEXT.php
    ª           ª           LabelTooLong.php
    ª           ª           LocalOrReservedDomain.php
    ª           ª           NoDNSRecord.php
    ª           ª           NoDomainPart.php
    ª           ª           NoLocalPart.php
    ª           ª           Reason.php
    ª           ª           RFCWarnings.php
    ª           ª           SpoofEmail.php
    ª           ª           UnableToGetDNSRecord.php
    ª           ª           UnclosedComment.php
    ª           ª           UnclosedQuotedString.php
    ª           ª           UnOpenedComment.php
    ª           ª           UnusualElements.php
    ª           ª           
    ª           +---Validation
    ª           ª   ª   DNSCheckValidation.php
    ª           ª   ª   DNSGetRecordWrapper.php
    ª           ª   ª   DNSRecords.php
    ª           ª   ª   EmailValidation.php
    ª           ª   ª   MessageIDValidation.php
    ª           ª   ª   MultipleValidationWithAnd.php
    ª           ª   ª   NoRFCWarningsValidation.php
    ª           ª   ª   RFCValidation.php
    ª           ª   ª   
    ª           ª   +---Exception
    ª           ª   ª       EmptyValidationList.php
    ª           ª   ª       
    ª           ª   +---Extra
    ª           ª           SpoofCheckValidation.php
    ª           ª           
    ª           +---Warning
    ª                   AddressLiteral.php
    ª                   CFWSNearAt.php
    ª                   CFWSWithFWS.php
    ª                   Comment.php
    ª                   DeprecatedComment.php
    ª                   DomainLiteral.php
    ª                   EmailTooLong.php
    ª                   IPV6BadChar.php
    ª                   IPV6ColonEnd.php
    ª                   IPV6ColonStart.php
    ª                   IPV6Deprecated.php
    ª                   IPV6DoubleColon.php
    ª                   IPV6GroupCount.php
    ª                   IPV6MaxGroups.php
    ª                   LocalTooLong.php
    ª                   NoDNSMXRecord.php
    ª                   ObsoleteDTEXT.php
    ª                   QuotedPart.php
    ª                   QuotedString.php
    ª                   TLD.php
    ª                   Warning.php
    ª                   
    +---fakerphp
    ª   +---faker
    ª       ª   CHANGELOG.md
    ª       ª   composer.json
    ª       ª   LICENSE
    ª       ª   README.md
    ª       ª   rector-migrate.php
    ª       ª   
    ª       +---src
    ª           ª   autoload.php
    ª           ª   
    ª           +---Faker
    ª               ª   ChanceGenerator.php
    ª               ª   DefaultGenerator.php
    ª               ª   Documentor.php
    ª               ª   Factory.php
    ª               ª   Generator.php
    ª               ª   UniqueGenerator.php
    ª               ª   ValidGenerator.php
    ª               ª   
    ª               +---Calculator
    ª               ª       Ean.php
    ª               ª       Iban.php
    ª               ª       Inn.php
    ª               ª       Isbn.php
    ª               ª       Luhn.php
    ª               ª       TCNo.php
    ª               ª       
    ª               +---Container
    ª               ª       Container.php
    ª               ª       ContainerBuilder.php
    ª               ª       ContainerException.php
    ª               ª       ContainerInterface.php
    ª               ª       NotInContainerException.php
    ª               ª       
    ª               +---Core
    ª               ª       Barcode.php
    ª               ª       Blood.php
    ª               ª       Color.php
    ª               ª       Coordinates.php
    ª               ª       DateTime.php
    ª               ª       File.php
    ª               ª       Number.php
    ª               ª       Uuid.php
    ª               ª       Version.php
    ª               ª       
    ª               +---Extension
    ª               ª       AddressExtension.php
    ª               ª       BarcodeExtension.php
    ª               ª       BloodExtension.php
    ª               ª       ColorExtension.php
    ª               ª       CompanyExtension.php
    ª               ª       CountryExtension.php
    ª               ª       DateTimeExtension.php
    ª               ª       Extension.php
    ª               ª       ExtensionNotFound.php
    ª               ª       FileExtension.php
    ª               ª       GeneratorAwareExtension.php
    ª               ª       GeneratorAwareExtensionTrait.php
    ª               ª       Helper.php
    ª               ª       NumberExtension.php
    ª               ª       PersonExtension.php
    ª               ª       PhoneNumberExtension.php
    ª               ª       UuidExtension.php
    ª               ª       VersionExtension.php
    ª               ª       
    ª               +---Guesser
    ª               ª       Name.php
    ª               ª       
    ª               +---ORM
    ª               ª   +---CakePHP
    ª               ª   ª       ColumnTypeGuesser.php
    ª               ª   ª       EntityPopulator.php
    ª               ª   ª       Populator.php
    ª               ª   ª       
    ª               ª   +---Doctrine
    ª               ª   ª       backward-compatibility.php
    ª               ª   ª       ColumnTypeGuesser.php
    ª               ª   ª       EntityPopulator.php
    ª               ª   ª       Populator.php
    ª               ª   ª       
    ª               ª   +---Mandango
    ª               ª   ª       ColumnTypeGuesser.php
    ª               ª   ª       EntityPopulator.php
    ª               ª   ª       Populator.php
    ª               ª   ª       
    ª               ª   +---Propel
    ª               ª   ª       ColumnTypeGuesser.php
    ª               ª   ª       EntityPopulator.php
    ª               ª   ª       Populator.php
    ª               ª   ª       
    ª               ª   +---Propel2
    ª               ª   ª       ColumnTypeGuesser.php
    ª               ª   ª       EntityPopulator.php
    ª               ª   ª       Populator.php
    ª               ª   ª       
    ª               ª   +---Spot
    ª               ª           ColumnTypeGuesser.php
    ª               ª           EntityPopulator.php
    ª               ª           Populator.php
    ª               ª           
    ª               +---Provider
    ª                   ª   Address.php
    ª                   ª   Barcode.php
    ª                   ª   Base.php
    ª                   ª   Biased.php
    ª                   ª   Color.php
    ª                   ª   Company.php
    ª                   ª   DateTime.php
    ª                   ª   File.php
    ª                   ª   HtmlLorem.php
    ª                   ª   Image.php
    ª                   ª   Internet.php
    ª                   ª   Lorem.php
    ª                   ª   Medical.php
    ª                   ª   Miscellaneous.php
    ª                   ª   Payment.php
    ª                   ª   Person.php
    ª                   ª   PhoneNumber.php
    ª                   ª   Text.php
    ª                   ª   UserAgent.php
    ª                   ª   Uuid.php
    ª                   ª   
    ª                   +---ar_EG
    ª                   ª       Address.php
    ª                   ª       Color.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---ar_JO
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Person.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---ar_SA
    ª                   ª       Address.php
    ª                   ª       Color.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---at_AT
    ª                   ª       Payment.php
    ª                   ª       
    ª                   +---bg_BG
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---bn_BD
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Utils.php
    ª                   ª       
    ª                   +---cs_CZ
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       DateTime.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---da_DK
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---de_AT
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---de_CH
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---de_DE
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---el_CY
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---el_GR
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---en_AU
    ª                   ª       Address.php
    ª                   ª       Internet.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---en_CA
    ª                   ª       Address.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---en_GB
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---en_HK
    ª                   ª       Address.php
    ª                   ª       Internet.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---en_IN
    ª                   ª       Address.php
    ª                   ª       Internet.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---en_NG
    ª                   ª       Address.php
    ª                   ª       Internet.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---en_NZ
    ª                   ª       Address.php
    ª                   ª       Internet.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---en_PH
    ª                   ª       Address.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---en_SG
    ª                   ª       Address.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---en_UG
    ª                   ª       Address.php
    ª                   ª       Internet.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---en_US
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---en_ZA
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---es_AR
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---es_ES
    ª                   ª       Address.php
    ª                   ª       Color.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---es_PE
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---es_VE
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---et_EE
    ª                   ª       Person.php
    ª                   ª       
    ª                   +---fa_IR
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---fi_FI
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---fr_BE
    ª                   ª       Address.php
    ª                   ª       Color.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---fr_CA
    ª                   ª       Address.php
    ª                   ª       Color.php
    ª                   ª       Company.php
    ª                   ª       Person.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---fr_CH
    ª                   ª       Address.php
    ª                   ª       Color.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---fr_FR
    ª                   ª       Address.php
    ª                   ª       Color.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---he_IL
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---hr_HR
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---hu_HU
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---hy_AM
    ª                   ª       Address.php
    ª                   ª       Color.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---id_ID
    ª                   ª       Address.php
    ª                   ª       Color.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---is_IS
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---it_CH
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---it_IT
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---ja_JP
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---ka_GE
    ª                   ª       Address.php
    ª                   ª       Color.php
    ª                   ª       Company.php
    ª                   ª       DateTime.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---kk_KZ
    ª                   ª       Address.php
    ª                   ª       Color.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---ko_KR
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---lt_LT
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---lv_LV
    ª                   ª       Address.php
    ª                   ª       Color.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---me_ME
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---mn_MN
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---ms_MY
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Miscellaneous.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---nb_NO
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---ne_NP
    ª                   ª       Address.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---nl_BE
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---nl_NL
    ª                   ª       Address.php
    ª                   ª       Color.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---pl_PL
    ª                   ª       Address.php
    ª                   ª       Color.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       LicensePlate.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---pt_BR
    ª                   ª       Address.php
    ª                   ª       check_digit.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---pt_PT
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---ro_MD
    ª                   ª       Address.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---ro_RO
    ª                   ª       Address.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---ru_RU
    ª                   ª       Address.php
    ª                   ª       Color.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---sk_SK
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---sl_SI
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---sr_Cyrl_RS
    ª                   ª       Address.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       
    ª                   +---sr_Latn_RS
    ª                   ª       Address.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       
    ª                   +---sr_RS
    ª                   ª       Address.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       
    ª                   +---sv_SE
    ª                   ª       Address.php
    ª                   ª       Company.php
    ª                   ª       Municipality.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---th_TH
    ª                   ª       Address.php
    ª                   ª       Color.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---tr_TR
    ª                   ª       Address.php
    ª                   ª       Color.php
    ª                   ª       Company.php
    ª                   ª       DateTime.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---uk_UA
    ª                   ª       Address.php
    ª                   ª       Color.php
    ª                   ª       Company.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       Text.php
    ª                   ª       
    ª                   +---vi_VN
    ª                   ª       Address.php
    ª                   ª       Color.php
    ª                   ª       Internet.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---zh_CN
    ª                   ª       Address.php
    ª                   ª       Color.php
    ª                   ª       Company.php
    ª                   ª       DateTime.php
    ª                   ª       Internet.php
    ª                   ª       Payment.php
    ª                   ª       Person.php
    ª                   ª       PhoneNumber.php
    ª                   ª       
    ª                   +---zh_TW
    ª                           Address.php
    ª                           Color.php
    ª                           Company.php
    ª                           DateTime.php
    ª                           Internet.php
    ª                           Payment.php
    ª                           Person.php
    ª                           PhoneNumber.php
    ª                           Text.php
    ª                           
    +---filp
    ª   +---whoops
    ª       ª   .mailmap
    ª       ª   CHANGELOG.md
    ª       ª   composer.json
    ª       ª   LICENSE.md
    ª       ª   SECURITY.md
    ª       ª   
    ª       +---src
    ª           +---Whoops
    ª               ª   Run.php
    ª               ª   RunInterface.php
    ª               ª   
    ª               +---Exception
    ª               ª       ErrorException.php
    ª               ª       Formatter.php
    ª               ª       Frame.php
    ª               ª       FrameCollection.php
    ª               ª       Inspector.php
    ª               ª       
    ª               +---Handler
    ª               ª       CallbackHandler.php
    ª               ª       Handler.php
    ª               ª       HandlerInterface.php
    ª               ª       JsonResponseHandler.php
    ª               ª       PlainTextHandler.php
    ª               ª       PrettyPageHandler.php
    ª               ª       XmlResponseHandler.php
    ª               ª       
    ª               +---Inspector
    ª               ª       InspectorFactory.php
    ª               ª       InspectorFactoryInterface.php
    ª               ª       InspectorInterface.php
    ª               ª       
    ª               +---Resources
    ª               ª   +---css
    ª               ª   ª       prism.css
    ª               ª   ª       whoops.base.css
    ª               ª   ª       
    ª               ª   +---js
    ª               ª   ª       clipboard.min.js
    ª               ª   ª       prism.js
    ª               ª   ª       whoops.base.js
    ª               ª   ª       zepto.min.js
    ª               ª   ª       
    ª               ª   +---views
    ª               ª           env_details.html.php
    ª               ª           frames_container.html.php
    ª               ª           frames_description.html.php
    ª               ª           frame_code.html.php
    ª               ª           frame_list.html.php
    ª               ª           header.html.php
    ª               ª           header_outer.html.php
    ª               ª           layout.html.php
    ª               ª           panel_details.html.php
    ª               ª           panel_details_outer.html.php
    ª               ª           panel_left.html.php
    ª               ª           panel_left_outer.html.php
    ª               ª           
    ª               +---Util
    ª                       HtmlDumperOutput.php
    ª                       Misc.php
    ª                       SystemFacade.php
    ª                       TemplateHelper.php
    ª                       
    +---fruitcake
    ª   +---php-cors
    ª       ª   composer.json
    ª       ª   LICENSE
    ª       ª   README.md
    ª       ª   
    ª       +---src
    ª           ª   CorsService.php
    ª           ª   
    ª           +---Exceptions
    ª                   InvalidOptionException.php
    ª                   
    +---graham-campbell
    ª   +---result-type
    ª       ª   composer.json
    ª       ª   LICENSE
    ª       ª   
    ª       +---src
    ª               Error.php
    ª               Result.php
    ª               Success.php
    ª               
    +---guzzlehttp
    ª   +---guzzle
    ª   ª   ª   CHANGELOG.md
    ª   ª   ª   composer.json
    ª   ª   ª   LICENSE
    ª   ª   ª   README.md
    ª   ª   ª   UPGRADING.md
    ª   ª   ª   
    ª   ª   +---src
    ª   ª       ª   BodySummarizer.php
    ª   ª       ª   BodySummarizerInterface.php
    ª   ª       ª   Client.php
    ª   ª       ª   ClientInterface.php
    ª   ª       ª   ClientTrait.php
    ª   ª       ª   functions.php
    ª   ª       ª   functions_include.php
    ª   ª       ª   HandlerStack.php
    ª   ª       ª   MessageFormatter.php
    ª   ª       ª   MessageFormatterInterface.php
    ª   ª       ª   Middleware.php
    ª   ª       ª   Pool.php
    ª   ª       ª   PrepareBodyMiddleware.php
    ª   ª       ª   RedirectMiddleware.php
    ª   ª       ª   RequestOptions.php
    ª   ª       ª   RetryMiddleware.php
    ª   ª       ª   TransferStats.php
    ª   ª       ª   Utils.php
    ª   ª       ª   
    ª   ª       +---Cookie
    ª   ª       ª       CookieJar.php
    ª   ª       ª       CookieJarInterface.php
    ª   ª       ª       FileCookieJar.php
    ª   ª       ª       SessionCookieJar.php
    ª   ª       ª       SetCookie.php
    ª   ª       ª       
    ª   ª       +---Exception
    ª   ª       ª       BadResponseException.php
    ª   ª       ª       ClientException.php
    ª   ª       ª       ConnectException.php
    ª   ª       ª       GuzzleException.php
    ª   ª       ª       InvalidArgumentException.php
    ª   ª       ª       RequestException.php
    ª   ª       ª       ServerException.php
    ª   ª       ª       TooManyRedirectsException.php
    ª   ª       ª       TransferException.php
    ª   ª       ª       
    ª   ª       +---Handler
    ª   ª               CurlFactory.php
    ª   ª               CurlFactoryInterface.php
    ª   ª               CurlHandler.php
    ª   ª               CurlMultiHandler.php
    ª   ª               EasyHandle.php
    ª   ª               HeaderProcessor.php
    ª   ª               MockHandler.php
    ª   ª               Proxy.php
    ª   ª               StreamHandler.php
    ª   ª               
    ª   +---promises
    ª   ª   ª   CHANGELOG.md
    ª   ª   ª   composer.json
    ª   ª   ª   LICENSE
    ª   ª   ª   README.md
    ª   ª   ª   
    ª   ª   +---src
    ª   ª           AggregateException.php
    ª   ª           CancellationException.php
    ª   ª           Coroutine.php
    ª   ª           Create.php
    ª   ª           Each.php
    ª   ª           EachPromise.php
    ª   ª           FulfilledPromise.php
    ª   ª           Is.php
    ª   ª           Promise.php
    ª   ª           PromiseInterface.php
    ª   ª           PromisorInterface.php
    ª   ª           RejectedPromise.php
    ª   ª           RejectionException.php
    ª   ª           TaskQueue.php
    ª   ª           TaskQueueInterface.php
    ª   ª           Utils.php
    ª   ª           
    ª   +---psr7
    ª   ª   ª   CHANGELOG.md
    ª   ª   ª   composer.json
    ª   ª   ª   LICENSE
    ª   ª   ª   README.md
    ª   ª   ª   
    ª   ª   +---src
    ª   ª       ª   AppendStream.php
    ª   ª       ª   BufferStream.php
    ª   ª       ª   CachingStream.php
    ª   ª       ª   DroppingStream.php
    ª   ª       ª   FnStream.php
    ª   ª       ª   Header.php
    ª   ª       ª   HttpFactory.php
    ª   ª       ª   InflateStream.php
    ª   ª       ª   LazyOpenStream.php
    ª   ª       ª   LimitStream.php
    ª   ª       ª   Message.php
    ª   ª       ª   MessageTrait.php
    ª   ª       ª   MimeType.php
    ª   ª       ª   MultipartStream.php
    ª   ª       ª   NoSeekStream.php
    ª   ª       ª   PumpStream.php
    ª   ª       ª   Query.php
    ª   ª       ª   Request.php
    ª   ª       ª   Response.php
    ª   ª       ª   Rfc7230.php
    ª   ª       ª   ServerRequest.php
    ª   ª       ª   Stream.php
    ª   ª       ª   StreamDecoratorTrait.php
    ª   ª       ª   StreamWrapper.php
    ª   ª       ª   UploadedFile.php
    ª   ª       ª   Uri.php
    ª   ª       ª   UriComparator.php
    ª   ª       ª   UriNormalizer.php
    ª   ª       ª   UriResolver.php
    ª   ª       ª   Utils.php
    ª   ª       ª   
    ª   ª       +---Exception
    ª   ª               MalformedUriException.php
    ª   ª               
    ª   +---uri-template
    ª       ª   CHANGELOG.md
    ª       ª   composer.json
    ª       ª   LICENSE
    ª       ª   README.md
    ª       ª   
    ª       +---src
    ª               UriTemplate.php
    ª               
    +---hamcrest
    ª   +---hamcrest-php
    ª       ª   .coveralls.yml
    ª       ª   .gitignore
    ª       ª   .gush.yml
    ª       ª   .travis.yml
    ª       ª   CHANGES.txt
    ª       ª   composer.json
    ª       ª   LICENSE.txt
    ª       ª   README.md
    ª       ª   
    ª       +---.github
    ª       ª   +---workflows
    ª       ª           tests.yml
    ª       ª           
    ª       +---generator
    ª       ª   ª   FactoryCall.php
    ª       ª   ª   FactoryClass.php
    ª       ª   ª   FactoryFile.php
    ª       ª   ª   FactoryGenerator.php
    ª       ª   ª   FactoryMethod.php
    ª       ª   ª   FactoryParameter.php
    ª       ª   ª   GlobalFunctionFile.php
    ª       ª   ª   run.php
    ª       ª   ª   StaticMethodFile.php
    ª       ª   ª   
    ª       ª   +---parts
    ª       ª           file_header.txt
    ª       ª           functions_footer.txt
    ª       ª           functions_header.txt
    ª       ª           functions_imports.txt
    ª       ª           matchers_footer.txt
    ª       ª           matchers_header.txt
    ª       ª           matchers_imports.txt
    ª       ª           
    ª       +---hamcrest
    ª       ª   ª   Hamcrest.php
    ª       ª   ª   
    ª       ª   +---Hamcrest
    ª       ª       ª   AssertionError.php
    ª       ª       ª   BaseDescription.php
    ª       ª       ª   BaseMatcher.php
    ª       ª       ª   Description.php
    ª       ª       ª   DiagnosingMatcher.php
    ª       ª       ª   FeatureMatcher.php
    ª       ª       ª   Matcher.php
    ª       ª       ª   MatcherAssert.php
    ª       ª       ª   Matchers.php
    ª       ª       ª   NullDescription.php
    ª       ª       ª   SelfDescribing.php
    ª       ª       ª   StringDescription.php
    ª       ª       ª   TypeSafeDiagnosingMatcher.php
    ª       ª       ª   TypeSafeMatcher.php
    ª       ª       ª   Util.php
    ª       ª       ª   
    ª       ª       +---Arrays
    ª       ª       ª       IsArray.php
    ª       ª       ª       IsArrayContaining.php
    ª       ª       ª       IsArrayContainingInAnyOrder.php
    ª       ª       ª       IsArrayContainingInOrder.php
    ª       ª       ª       IsArrayContainingKey.php
    ª       ª       ª       IsArrayContainingKeyValuePair.php
    ª       ª       ª       IsArrayWithSize.php
    ª       ª       ª       MatchingOnce.php
    ª       ª       ª       SeriesMatchingOnce.php
    ª       ª       ª       
    ª       ª       +---Collection
    ª       ª       ª       IsEmptyTraversable.php
    ª       ª       ª       IsTraversableWithSize.php
    ª       ª       ª       
    ª       ª       +---Core
    ª       ª       ª       AllOf.php
    ª       ª       ª       AnyOf.php
    ª       ª       ª       CombinableMatcher.php
    ª       ª       ª       DescribedAs.php
    ª       ª       ª       Every.php
    ª       ª       ª       HasToString.php
    ª       ª       ª       Is.php
    ª       ª       ª       IsAnything.php
    ª       ª       ª       IsCollectionContaining.php
    ª       ª       ª       IsEqual.php
    ª       ª       ª       IsIdentical.php
    ª       ª       ª       IsInstanceOf.php
    ª       ª       ª       IsNot.php
    ª       ª       ª       IsNull.php
    ª       ª       ª       IsSame.php
    ª       ª       ª       IsTypeOf.php
    ª       ª       ª       Set.php
    ª       ª       ª       ShortcutCombination.php
    ª       ª       ª       
    ª       ª       +---Internal
    ª       ª       ª       SelfDescribingValue.php
    ª       ª       ª       
    ª       ª       +---Number
    ª       ª       ª       IsCloseTo.php
    ª       ª       ª       OrderingComparison.php
    ª       ª       ª       
    ª       ª       +---Text
    ª       ª       ª       IsEmptyString.php
    ª       ª       ª       IsEqualIgnoringCase.php
    ª       ª       ª       IsEqualIgnoringWhiteSpace.php
    ª       ª       ª       MatchesPattern.php
    ª       ª       ª       StringContains.php
    ª       ª       ª       StringContainsIgnoringCase.php
    ª       ª       ª       StringContainsInOrder.php
    ª       ª       ª       StringEndsWith.php
    ª       ª       ª       StringStartsWith.php
    ª       ª       ª       SubstringMatcher.php
    ª       ª       ª       
    ª       ª       +---Type
    ª       ª       ª       IsArray.php
    ª       ª       ª       IsBoolean.php
    ª       ª       ª       IsCallable.php
    ª       ª       ª       IsDouble.php
    ª       ª       ª       IsInteger.php
    ª       ª       ª       IsNumeric.php
    ª       ª       ª       IsObject.php
    ª       ª       ª       IsResource.php
    ª       ª       ª       IsScalar.php
    ª       ª       ª       IsString.php
    ª       ª       ª       
    ª       ª       +---Xml
    ª       ª               HasXPath.php
    ª       ª               
    ª       +---tests
    ª           ª   bootstrap.php
    ª           ª   phpunit.xml.dist
    ª           ª   
    ª           +---Hamcrest
    ª               ª   AbstractMatcherTest.php
    ª               ª   BaseMatcherTest.php
    ª               ª   FeatureMatcherTest.php
    ª               ª   InvokedMatcherTest.php
    ª               ª   MatcherAssertTest.php
    ª               ª   StringDescriptionTest.php
    ª               ª   UtilTest.php
    ª               ª   
    ª               +---Array
    ª               ª       IsArrayContainingInAnyOrderTest.php
    ª               ª       IsArrayContainingInOrderTest.php
    ª               ª       IsArrayContainingKeyTest.php
    ª               ª       IsArrayContainingKeyValuePairTest.php
    ª               ª       IsArrayContainingTest.php
    ª               ª       IsArrayTest.php
    ª               ª       IsArrayWithSizeTest.php
    ª               ª       
    ª               +---Collection
    ª               ª       IsEmptyTraversableTest.php
    ª               ª       IsTraversableWithSizeTest.php
    ª               ª       
    ª               +---Core
    ª               ª       AllOfTest.php
    ª               ª       AnyOfTest.php
    ª               ª       CombinableMatcherTest.php
    ª               ª       DescribedAsTest.php
    ª               ª       EveryTest.php
    ª               ª       HasToStringTest.php
    ª               ª       IsAnythingTest.php
    ª               ª       IsCollectionContainingTest.php
    ª               ª       IsEqualTest.php
    ª               ª       IsIdenticalTest.php
    ª               ª       IsInstanceOfTest.php
    ª               ª       IsNotTest.php
    ª               ª       IsNullTest.php
    ª               ª       IsSameTest.php
    ª               ª       IsTest.php
    ª               ª       IsTypeOfTest.php
    ª               ª       SampleBaseClass.php
    ª               ª       SampleSubClass.php
    ª               ª       SetTest.php
    ª               ª       
    ª               +---Number
    ª               ª       IsCloseToTest.php
    ª               ª       OrderingComparisonTest.php
    ª               ª       
    ª               +---Text
    ª               ª       IsEmptyStringTest.php
    ª               ª       IsEqualIgnoringCaseTest.php
    ª               ª       IsEqualIgnoringWhiteSpaceTest.php
    ª               ª       MatchesPatternTest.php
    ª               ª       StringContainsIgnoringCaseTest.php
    ª               ª       StringContainsInOrderTest.php
    ª               ª       StringContainsTest.php
    ª               ª       StringEndsWithTest.php
    ª               ª       StringStartsWithTest.php
    ª               ª       
    ª               +---Type
    ª               ª       IsArrayTest.php
    ª               ª       IsBooleanTest.php
    ª               ª       IsCallableTest.php
    ª               ª       IsDoubleTest.php
    ª               ª       IsIntegerTest.php
    ª               ª       IsNumericTest.php
    ª               ª       IsObjectTest.php
    ª               ª       IsResourceTest.php
    ª               ª       IsScalarTest.php
    ª               ª       IsStringTest.php
    ª               ª       
    ª               +---Xml
    ª                       HasXPathTest.php
    ª                       
    +---laravel
    ª   +---framework
    ª   ª   ª   CHANGELOG.md
    ª   ª   ª   composer.json
    ª   ª   ª   LICENSE.md
    ª   ª   ª   README.md
    ª   ª   ª   RELEASE.md
    ª   ª   ª   
    ª   ª   +---src
    ª   ª       +---Illuminate
    ª   ª           +---Auth
    ª   ª           ª   ª   Authenticatable.php
    ª   ª           ª   ª   AuthenticationException.php
    ª   ª           ª   ª   AuthManager.php
    ª   ª           ª   ª   AuthServiceProvider.php
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   CreatesUserProviders.php
    ª   ª           ª   ª   DatabaseUserProvider.php
    ª   ª           ª   ª   EloquentUserProvider.php
    ª   ª           ª   ª   GenericUser.php
    ª   ª           ª   ª   GuardHelpers.php
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   MustVerifyEmail.php
    ª   ª           ª   ª   Recaller.php
    ª   ª           ª   ª   RequestGuard.php
    ª   ª           ª   ª   SessionGuard.php
    ª   ª           ª   ª   TokenGuard.php
    ª   ª           ª   ª   
    ª   ª           ª   +---Access
    ª   ª           ª   ª   ª   AuthorizationException.php
    ª   ª           ª   ª   ª   Gate.php
    ª   ª           ª   ª   ª   HandlesAuthorization.php
    ª   ª           ª   ª   ª   Response.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---Events
    ª   ª           ª   ª           GateEvaluated.php
    ª   ª           ª   ª           
    ª   ª           ª   +---Console
    ª   ª           ª   ª   ª   ClearResetsCommand.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---stubs
    ª   ª           ª   ª       +---make
    ª   ª           ª   ª           +---views
    ª   ª           ª   ª               +---layouts
    ª   ª           ª   ª                       app.stub
    ª   ª           ª   ª                       
    ª   ª           ª   +---Events
    ª   ª           ª   ª       Attempting.php
    ª   ª           ª   ª       Authenticated.php
    ª   ª           ª   ª       CurrentDeviceLogout.php
    ª   ª           ª   ª       Failed.php
    ª   ª           ª   ª       Lockout.php
    ª   ª           ª   ª       Login.php
    ª   ª           ª   ª       Logout.php
    ª   ª           ª   ª       OtherDeviceLogout.php
    ª   ª           ª   ª       PasswordReset.php
    ª   ª           ª   ª       Registered.php
    ª   ª           ª   ª       Validated.php
    ª   ª           ª   ª       Verified.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Listeners
    ª   ª           ª   ª       SendEmailVerificationNotification.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Middleware
    ª   ª           ª   ª       Authenticate.php
    ª   ª           ª   ª       AuthenticateWithBasicAuth.php
    ª   ª           ª   ª       Authorize.php
    ª   ª           ª   ª       EnsureEmailIsVerified.php
    ª   ª           ª   ª       RequirePassword.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Notifications
    ª   ª           ª   ª       ResetPassword.php
    ª   ª           ª   ª       VerifyEmail.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Passwords
    ª   ª           ª           CanResetPassword.php
    ª   ª           ª           DatabaseTokenRepository.php
    ª   ª           ª           PasswordBroker.php
    ª   ª           ª           PasswordBrokerManager.php
    ª   ª           ª           PasswordResetServiceProvider.php
    ª   ª           ª           TokenRepositoryInterface.php
    ª   ª           ª           
    ª   ª           +---Broadcasting
    ª   ª           ª   ª   BroadcastController.php
    ª   ª           ª   ª   BroadcastEvent.php
    ª   ª           ª   ª   BroadcastException.php
    ª   ª           ª   ª   BroadcastManager.php
    ª   ª           ª   ª   BroadcastServiceProvider.php
    ª   ª           ª   ª   Channel.php
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   EncryptedPrivateChannel.php
    ª   ª           ª   ª   InteractsWithBroadcasting.php
    ª   ª           ª   ª   InteractsWithSockets.php
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   PendingBroadcast.php
    ª   ª           ª   ª   PresenceChannel.php
    ª   ª           ª   ª   PrivateChannel.php
    ª   ª           ª   ª   UniqueBroadcastEvent.php
    ª   ª           ª   ª   
    ª   ª           ª   +---Broadcasters
    ª   ª           ª           AblyBroadcaster.php
    ª   ª           ª           Broadcaster.php
    ª   ª           ª           LogBroadcaster.php
    ª   ª           ª           NullBroadcaster.php
    ª   ª           ª           PusherBroadcaster.php
    ª   ª           ª           RedisBroadcaster.php
    ª   ª           ª           UsePusherChannelConventions.php
    ª   ª           ª           
    ª   ª           +---Bus
    ª   ª           ª   ª   Batch.php
    ª   ª           ª   ª   Batchable.php
    ª   ª           ª   ª   BatchFactory.php
    ª   ª           ª   ª   BatchRepository.php
    ª   ª           ª   ª   BusServiceProvider.php
    ª   ª           ª   ª   ChainedBatch.php
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   DatabaseBatchRepository.php
    ª   ª           ª   ª   Dispatcher.php
    ª   ª           ª   ª   DynamoBatchRepository.php
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   PendingBatch.php
    ª   ª           ª   ª   PrunableBatchRepository.php
    ª   ª           ª   ª   Queueable.php
    ª   ª           ª   ª   UniqueLock.php
    ª   ª           ª   ª   UpdatedBatchJobCounts.php
    ª   ª           ª   ª   
    ª   ª           ª   +---Events
    ª   ª           ª           BatchDispatched.php
    ª   ª           ª           
    ª   ª           +---Cache
    ª   ª           ª   ª   ApcStore.php
    ª   ª           ª   ª   ApcWrapper.php
    ª   ª           ª   ª   ArrayLock.php
    ª   ª           ª   ª   ArrayStore.php
    ª   ª           ª   ª   CacheLock.php
    ª   ª           ª   ª   CacheManager.php
    ª   ª           ª   ª   CacheServiceProvider.php
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   DatabaseLock.php
    ª   ª           ª   ª   DatabaseStore.php
    ª   ª           ª   ª   DynamoDbLock.php
    ª   ª           ª   ª   DynamoDbStore.php
    ª   ª           ª   ª   FileLock.php
    ª   ª           ª   ª   FileStore.php
    ª   ª           ª   ª   HasCacheLock.php
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   Lock.php
    ª   ª           ª   ª   LuaScripts.php
    ª   ª           ª   ª   MemcachedConnector.php
    ª   ª           ª   ª   MemcachedLock.php
    ª   ª           ª   ª   MemcachedStore.php
    ª   ª           ª   ª   NoLock.php
    ª   ª           ª   ª   NullStore.php
    ª   ª           ª   ª   PhpRedisLock.php
    ª   ª           ª   ª   RateLimiter.php
    ª   ª           ª   ª   RedisLock.php
    ª   ª           ª   ª   RedisStore.php
    ª   ª           ª   ª   RedisTaggedCache.php
    ª   ª           ª   ª   RedisTagSet.php
    ª   ª           ª   ª   Repository.php
    ª   ª           ª   ª   RetrievesMultipleKeys.php
    ª   ª           ª   ª   TaggableStore.php
    ª   ª           ª   ª   TaggedCache.php
    ª   ª           ª   ª   TagSet.php
    ª   ª           ª   ª   
    ª   ª           ª   +---Console
    ª   ª           ª   ª   ª   CacheTableCommand.php
    ª   ª           ª   ª   ª   ClearCommand.php
    ª   ª           ª   ª   ª   ForgetCommand.php
    ª   ª           ª   ª   ª   PruneStaleTagsCommand.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---stubs
    ª   ª           ª   ª           cache.stub
    ª   ª           ª   ª           
    ª   ª           ª   +---Events
    ª   ª           ª   ª       CacheEvent.php
    ª   ª           ª   ª       CacheHit.php
    ª   ª           ª   ª       CacheMissed.php
    ª   ª           ª   ª       KeyForgotten.php
    ª   ª           ª   ª       KeyWritten.php
    ª   ª           ª   ª       
    ª   ª           ª   +---RateLimiting
    ª   ª           ª           GlobalLimit.php
    ª   ª           ª           Limit.php
    ª   ª           ª           Unlimited.php
    ª   ª           ª           
    ª   ª           +---Collections
    ª   ª           ª   ª   Arr.php
    ª   ª           ª   ª   Collection.php
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   Enumerable.php
    ª   ª           ª   ª   helpers.php
    ª   ª           ª   ª   HigherOrderCollectionProxy.php
    ª   ª           ª   ª   ItemNotFoundException.php
    ª   ª           ª   ª   LazyCollection.php
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   MultipleItemsFoundException.php
    ª   ª           ª   ª   
    ª   ª           ª   +---Traits
    ª   ª           ª           EnumeratesValues.php
    ª   ª           ª           
    ª   ª           +---Conditionable
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   HigherOrderWhenProxy.php
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   
    ª   ª           ª   +---Traits
    ª   ª           ª           Conditionable.php
    ª   ª           ª           
    ª   ª           +---Config
    ª   ª           ª       composer.json
    ª   ª           ª       LICENSE.md
    ª   ª           ª       Repository.php
    ª   ª           ª       
    ª   ª           +---Console
    ª   ª           ª   ª   Application.php
    ª   ª           ª   ª   BufferedConsoleOutput.php
    ª   ª           ª   ª   CacheCommandMutex.php
    ª   ª           ª   ª   Command.php
    ª   ª           ª   ª   CommandMutex.php
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   ConfirmableTrait.php
    ª   ª           ª   ª   ContainerCommandLoader.php
    ª   ª           ª   ª   GeneratorCommand.php
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   MigrationGeneratorCommand.php
    ª   ª           ª   ª   OutputStyle.php
    ª   ª           ª   ª   Parser.php
    ª   ª           ª   ª   PromptValidationException.php
    ª   ª           ª   ª   QuestionHelper.php
    ª   ª           ª   ª   Signals.php
    ª   ª           ª   ª   
    ª   ª           ª   +---Concerns
    ª   ª           ª   ª       CallsCommands.php
    ª   ª           ª   ª       ConfiguresPrompts.php
    ª   ª           ª   ª       CreatesMatchingTest.php
    ª   ª           ª   ª       HasParameters.php
    ª   ª           ª   ª       InteractsWithIO.php
    ª   ª           ª   ª       InteractsWithSignals.php
    ª   ª           ª   ª       PromptsForMissingInput.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Contracts
    ª   ª           ª   ª       NewLineAware.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Events
    ª   ª           ª   ª       ArtisanStarting.php
    ª   ª           ª   ª       CommandFinished.php
    ª   ª           ª   ª       CommandStarting.php
    ª   ª           ª   ª       ScheduledBackgroundTaskFinished.php
    ª   ª           ª   ª       ScheduledTaskFailed.php
    ª   ª           ª   ª       ScheduledTaskFinished.php
    ª   ª           ª   ª       ScheduledTaskSkipped.php
    ª   ª           ª   ª       ScheduledTaskStarting.php
    ª   ª           ª   ª       
    ª   ª           ª   +---resources
    ª   ª           ª   ª   +---views
    ª   ª           ª   ª       +---components
    ª   ª           ª   ª               alert.php
    ª   ª           ª   ª               bullet-list.php
    ª   ª           ª   ª               line.php
    ª   ª           ª   ª               two-column-detail.php
    ª   ª           ª   ª               
    ª   ª           ª   +---Scheduling
    ª   ª           ª   ª       CacheAware.php
    ª   ª           ª   ª       CacheEventMutex.php
    ª   ª           ª   ª       CacheSchedulingMutex.php
    ª   ª           ª   ª       CallbackEvent.php
    ª   ª           ª   ª       CommandBuilder.php
    ª   ª           ª   ª       Event.php
    ª   ª           ª   ª       EventMutex.php
    ª   ª           ª   ª       ManagesFrequencies.php
    ª   ª           ª   ª       Schedule.php
    ª   ª           ª   ª       ScheduleClearCacheCommand.php
    ª   ª           ª   ª       ScheduleFinishCommand.php
    ª   ª           ª   ª       ScheduleInterruptCommand.php
    ª   ª           ª   ª       ScheduleListCommand.php
    ª   ª           ª   ª       ScheduleRunCommand.php
    ª   ª           ª   ª       ScheduleTestCommand.php
    ª   ª           ª   ª       ScheduleWorkCommand.php
    ª   ª           ª   ª       SchedulingMutex.php
    ª   ª           ª   ª       
    ª   ª           ª   +---View
    ª   ª           ª       +---Components
    ª   ª           ª           ª   Alert.php
    ª   ª           ª           ª   Ask.php
    ª   ª           ª           ª   AskWithCompletion.php
    ª   ª           ª           ª   BulletList.php
    ª   ª           ª           ª   Choice.php
    ª   ª           ª           ª   Component.php
    ª   ª           ª           ª   Confirm.php
    ª   ª           ª           ª   Error.php
    ª   ª           ª           ª   Factory.php
    ª   ª           ª           ª   Info.php
    ª   ª           ª           ª   Line.php
    ª   ª           ª           ª   Secret.php
    ª   ª           ª           ª   Task.php
    ª   ª           ª           ª   TwoColumnDetail.php
    ª   ª           ª           ª   Warn.php
    ª   ª           ª           ª   
    ª   ª           ª           +---Mutators
    ª   ª           ª                   EnsureDynamicContentIsHighlighted.php
    ª   ª           ª                   EnsureNoPunctuation.php
    ª   ª           ª                   EnsurePunctuation.php
    ª   ª           ª                   EnsureRelativePaths.php
    ª   ª           ª                   
    ª   ª           +---Container
    ª   ª           ª       BoundMethod.php
    ª   ª           ª       composer.json
    ª   ª           ª       Container.php
    ª   ª           ª       ContextualBindingBuilder.php
    ª   ª           ª       EntryNotFoundException.php
    ª   ª           ª       LICENSE.md
    ª   ª           ª       RewindableGenerator.php
    ª   ª           ª       Util.php
    ª   ª           ª       
    ª   ª           +---Contracts
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   
    ª   ª           ª   +---Auth
    ª   ª           ª   ª   ª   Authenticatable.php
    ª   ª           ª   ª   ª   CanResetPassword.php
    ª   ª           ª   ª   ª   Factory.php
    ª   ª           ª   ª   ª   Guard.php
    ª   ª           ª   ª   ª   MustVerifyEmail.php
    ª   ª           ª   ª   ª   PasswordBroker.php
    ª   ª           ª   ª   ª   PasswordBrokerFactory.php
    ª   ª           ª   ª   ª   StatefulGuard.php
    ª   ª           ª   ª   ª   SupportsBasicAuth.php
    ª   ª           ª   ª   ª   UserProvider.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---Access
    ª   ª           ª   ª   ª       Authorizable.php
    ª   ª           ª   ª   ª       Gate.php
    ª   ª           ª   ª   ª       
    ª   ª           ª   ª   +---Middleware
    ª   ª           ª   ª           AuthenticatesRequests.php
    ª   ª           ª   ª           
    ª   ª           ª   +---Broadcasting
    ª   ª           ª   ª       Broadcaster.php
    ª   ª           ª   ª       Factory.php
    ª   ª           ª   ª       HasBroadcastChannel.php
    ª   ª           ª   ª       ShouldBeUnique.php
    ª   ª           ª   ª       ShouldBroadcast.php
    ª   ª           ª   ª       ShouldBroadcastNow.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Bus
    ª   ª           ª   ª       Dispatcher.php
    ª   ª           ª   ª       QueueingDispatcher.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Cache
    ª   ª           ª   ª       Factory.php
    ª   ª           ª   ª       Lock.php
    ª   ª           ª   ª       LockProvider.php
    ª   ª           ª   ª       LockTimeoutException.php
    ª   ª           ª   ª       Repository.php
    ª   ª           ª   ª       Store.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Config
    ª   ª           ª   ª       Repository.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Console
    ª   ª           ª   ª       Application.php
    ª   ª           ª   ª       Isolatable.php
    ª   ª           ª   ª       Kernel.php
    ª   ª           ª   ª       PromptsForMissingInput.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Container
    ª   ª           ª   ª       BindingResolutionException.php
    ª   ª           ª   ª       CircularDependencyException.php
    ª   ª           ª   ª       Container.php
    ª   ª           ª   ª       ContextualBindingBuilder.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Cookie
    ª   ª           ª   ª       Factory.php
    ª   ª           ª   ª       QueueingFactory.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Database
    ª   ª           ª   ª   ª   ModelIdentifier.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---Eloquent
    ª   ª           ª   ª   ª       Builder.php
    ª   ª           ª   ª   ª       Castable.php
    ª   ª           ª   ª   ª       CastsAttributes.php
    ª   ª           ª   ª   ª       CastsInboundAttributes.php
    ª   ª           ª   ª   ª       DeviatesCastableAttributes.php
    ª   ª           ª   ª   ª       SerializesCastableAttributes.php
    ª   ª           ª   ª   ª       SupportsPartialRelations.php
    ª   ª           ª   ª   ª       
    ª   ª           ª   ª   +---Events
    ª   ª           ª   ª   ª       MigrationEvent.php
    ª   ª           ª   ª   ª       
    ª   ª           ª   ª   +---Query
    ª   ª           ª   ª           Builder.php
    ª   ª           ª   ª           ConditionExpression.php
    ª   ª           ª   ª           Expression.php
    ª   ª           ª   ª           
    ª   ª           ª   +---Debug
    ª   ª           ª   ª       ExceptionHandler.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Encryption
    ª   ª           ª   ª       DecryptException.php
    ª   ª           ª   ª       Encrypter.php
    ª   ª           ª   ª       EncryptException.php
    ª   ª           ª   ª       StringEncrypter.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Events
    ª   ª           ª   ª       Dispatcher.php
    ª   ª           ª   ª       ShouldDispatchAfterCommit.php
    ª   ª           ª   ª       ShouldHandleEventsAfterCommit.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Filesystem
    ª   ª           ª   ª       Cloud.php
    ª   ª           ª   ª       Factory.php
    ª   ª           ª   ª       FileNotFoundException.php
    ª   ª           ª   ª       Filesystem.php
    ª   ª           ª   ª       LockTimeoutException.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Foundation
    ª   ª           ª   ª       Application.php
    ª   ª           ª   ª       CachesConfiguration.php
    ª   ª           ª   ª       CachesRoutes.php
    ª   ª           ª   ª       ExceptionRenderer.php
    ª   ª           ª   ª       MaintenanceMode.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Hashing
    ª   ª           ª   ª       Hasher.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Http
    ª   ª           ª   ª       Kernel.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Mail
    ª   ª           ª   ª       Attachable.php
    ª   ª           ª   ª       Factory.php
    ª   ª           ª   ª       Mailable.php
    ª   ª           ª   ª       Mailer.php
    ª   ª           ª   ª       MailQueue.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Notifications
    ª   ª           ª   ª       Dispatcher.php
    ª   ª           ª   ª       Factory.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Pagination
    ª   ª           ª   ª       CursorPaginator.php
    ª   ª           ª   ª       LengthAwarePaginator.php
    ª   ª           ª   ª       Paginator.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Pipeline
    ª   ª           ª   ª       Hub.php
    ª   ª           ª   ª       Pipeline.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Process
    ª   ª           ª   ª       InvokedProcess.php
    ª   ª           ª   ª       ProcessResult.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Queue
    ª   ª           ª   ª       ClearableQueue.php
    ª   ª           ª   ª       EntityNotFoundException.php
    ª   ª           ª   ª       EntityResolver.php
    ª   ª           ª   ª       Factory.php
    ª   ª           ª   ª       Job.php
    ª   ª           ª   ª       Monitor.php
    ª   ª           ª   ª       Queue.php
    ª   ª           ª   ª       QueueableCollection.php
    ª   ª           ª   ª       QueueableEntity.php
    ª   ª           ª   ª       ShouldBeEncrypted.php
    ª   ª           ª   ª       ShouldBeUnique.php
    ª   ª           ª   ª       ShouldBeUniqueUntilProcessing.php
    ª   ª           ª   ª       ShouldQueue.php
    ª   ª           ª   ª       ShouldQueueAfterCommit.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Redis
    ª   ª           ª   ª       Connection.php
    ª   ª           ª   ª       Connector.php
    ª   ª           ª   ª       Factory.php
    ª   ª           ª   ª       LimiterTimeoutException.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Routing
    ª   ª           ª   ª       BindingRegistrar.php
    ª   ª           ª   ª       Registrar.php
    ª   ª           ª   ª       ResponseFactory.php
    ª   ª           ª   ª       UrlGenerator.php
    ª   ª           ª   ª       UrlRoutable.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Session
    ª   ª           ª   ª   ª   Session.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---Middleware
    ª   ª           ª   ª           AuthenticatesSessions.php
    ª   ª           ª   ª           
    ª   ª           ª   +---Support
    ª   ª           ª   ª       Arrayable.php
    ª   ª           ª   ª       CanBeEscapedWhenCastToString.php
    ª   ª           ª   ª       DeferrableProvider.php
    ª   ª           ª   ª       DeferringDisplayableValue.php
    ª   ª           ª   ª       Htmlable.php
    ª   ª           ª   ª       Jsonable.php
    ª   ª           ª   ª       MessageBag.php
    ª   ª           ª   ª       MessageProvider.php
    ª   ª           ª   ª       Renderable.php
    ª   ª           ª   ª       Responsable.php
    ª   ª           ª   ª       ValidatedData.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Translation
    ª   ª           ª   ª       HasLocalePreference.php
    ª   ª           ª   ª       Loader.php
    ª   ª           ª   ª       Translator.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Validation
    ª   ª           ª   ª       DataAwareRule.php
    ª   ª           ª   ª       Factory.php
    ª   ª           ª   ª       ImplicitRule.php
    ª   ª           ª   ª       InvokableRule.php
    ª   ª           ª   ª       Rule.php
    ª   ª           ª   ª       UncompromisedVerifier.php
    ª   ª           ª   ª       ValidatesWhenResolved.php
    ª   ª           ª   ª       ValidationRule.php
    ª   ª           ª   ª       Validator.php
    ª   ª           ª   ª       ValidatorAwareRule.php
    ª   ª           ª   ª       
    ª   ª           ª   +---View
    ª   ª           ª           Engine.php
    ª   ª           ª           Factory.php
    ª   ª           ª           View.php
    ª   ª           ª           ViewCompilationException.php
    ª   ª           ª           
    ª   ª           +---Cookie
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   CookieJar.php
    ª   ª           ª   ª   CookieServiceProvider.php
    ª   ª           ª   ª   CookieValuePrefix.php
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   
    ª   ª           ª   +---Middleware
    ª   ª           ª           AddQueuedCookiesToResponse.php
    ª   ª           ª           EncryptCookies.php
    ª   ª           ª           
    ª   ª           +---Database
    ª   ª           ª   ª   ClassMorphViolationException.php
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   ConfigurationUrlParser.php
    ª   ª           ª   ª   Connection.php
    ª   ª           ª   ª   ConnectionInterface.php
    ª   ª           ª   ª   ConnectionResolver.php
    ª   ª           ª   ª   ConnectionResolverInterface.php
    ª   ª           ª   ª   DatabaseManager.php
    ª   ª           ª   ª   DatabaseServiceProvider.php
    ª   ª           ª   ª   DatabaseTransactionRecord.php
    ª   ª           ª   ª   DatabaseTransactionsManager.php
    ª   ª           ª   ª   DeadlockException.php
    ª   ª           ª   ª   DetectsConcurrencyErrors.php
    ª   ª           ª   ª   DetectsLostConnections.php
    ª   ª           ª   ª   Grammar.php
    ª   ª           ª   ª   LazyLoadingViolationException.php
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   LostConnectionException.php
    ª   ª           ª   ª   MigrationServiceProvider.php
    ª   ª           ª   ª   MultipleColumnsSelectedException.php
    ª   ª           ª   ª   MultipleRecordsFoundException.php
    ª   ª           ª   ª   MySqlConnection.php
    ª   ª           ª   ª   PostgresConnection.php
    ª   ª           ª   ª   QueryException.php
    ª   ª           ª   ª   README.md
    ª   ª           ª   ª   RecordsNotFoundException.php
    ª   ª           ª   ª   Seeder.php
    ª   ª           ª   ª   SQLiteConnection.php
    ª   ª           ª   ª   SQLiteDatabaseDoesNotExistException.php
    ª   ª           ª   ª   SqlServerConnection.php
    ª   ª           ª   ª   UniqueConstraintViolationException.php
    ª   ª           ª   ª   
    ª   ª           ª   +---Capsule
    ª   ª           ª   ª       Manager.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Concerns
    ª   ª           ª   ª       BuildsQueries.php
    ª   ª           ª   ª       CompilesJsonPaths.php
    ª   ª           ª   ª       ExplainsQueries.php
    ª   ª           ª   ª       ManagesTransactions.php
    ª   ª           ª   ª       ParsesSearchPath.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Connectors
    ª   ª           ª   ª       ConnectionFactory.php
    ª   ª           ª   ª       Connector.php
    ª   ª           ª   ª       ConnectorInterface.php
    ª   ª           ª   ª       MySqlConnector.php
    ª   ª           ª   ª       PostgresConnector.php
    ª   ª           ª   ª       SQLiteConnector.php
    ª   ª           ª   ª       SqlServerConnector.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Console
    ª   ª           ª   ª   ª   DatabaseInspectionCommand.php
    ª   ª           ª   ª   ª   DbCommand.php
    ª   ª           ª   ª   ª   DumpCommand.php
    ª   ª           ª   ª   ª   MonitorCommand.php
    ª   ª           ª   ª   ª   PruneCommand.php
    ª   ª           ª   ª   ª   ShowCommand.php
    ª   ª           ª   ª   ª   ShowModelCommand.php
    ª   ª           ª   ª   ª   TableCommand.php
    ª   ª           ª   ª   ª   WipeCommand.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---Factories
    ª   ª           ª   ª   ª   ª   FactoryMakeCommand.php
    ª   ª           ª   ª   ª   ª   
    ª   ª           ª   ª   ª   +---stubs
    ª   ª           ª   ª   ª           factory.stub
    ª   ª           ª   ª   ª           
    ª   ª           ª   ª   +---Migrations
    ª   ª           ª   ª   ª       BaseCommand.php
    ª   ª           ª   ª   ª       FreshCommand.php
    ª   ª           ª   ª   ª       InstallCommand.php
    ª   ª           ª   ª   ª       MigrateCommand.php
    ª   ª           ª   ª   ª       MigrateMakeCommand.php
    ª   ª           ª   ª   ª       RefreshCommand.php
    ª   ª           ª   ª   ª       ResetCommand.php
    ª   ª           ª   ª   ª       RollbackCommand.php
    ª   ª           ª   ª   ª       StatusCommand.php
    ª   ª           ª   ª   ª       TableGuesser.php
    ª   ª           ª   ª   ª       
    ª   ª           ª   ª   +---Seeds
    ª   ª           ª   ª       ª   SeedCommand.php
    ª   ª           ª   ª       ª   SeederMakeCommand.php
    ª   ª           ª   ª       ª   WithoutModelEvents.php
    ª   ª           ª   ª       ª   
    ª   ª           ª   ª       +---stubs
    ª   ª           ª   ª               seeder.stub
    ª   ª           ª   ª               
    ª   ª           ª   +---DBAL
    ª   ª           ª   ª       TimestampType.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Eloquent
    ª   ª           ª   ª   ª   BroadcastableModelEventOccurred.php
    ª   ª           ª   ª   ª   BroadcastsEvents.php
    ª   ª           ª   ª   ª   BroadcastsEventsAfterCommit.php
    ª   ª           ª   ª   ª   Builder.php
    ª   ª           ª   ª   ª   Collection.php
    ª   ª           ª   ª   ª   HigherOrderBuilderProxy.php
    ª   ª           ª   ª   ª   InvalidCastException.php
    ª   ª           ª   ª   ª   JsonEncodingException.php
    ª   ª           ª   ª   ª   MassAssignmentException.php
    ª   ª           ª   ª   ª   MassPrunable.php
    ª   ª           ª   ª   ª   MissingAttributeException.php
    ª   ª           ª   ª   ª   Model.php
    ª   ª           ª   ª   ª   ModelNotFoundException.php
    ª   ª           ª   ª   ª   PendingHasThroughRelationship.php
    ª   ª           ª   ª   ª   Prunable.php
    ª   ª           ª   ª   ª   QueueEntityResolver.php
    ª   ª           ª   ª   ª   RelationNotFoundException.php
    ª   ª           ª   ª   ª   Scope.php
    ª   ª           ª   ª   ª   SoftDeletes.php
    ª   ª           ª   ª   ª   SoftDeletingScope.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---Attributes
    ª   ª           ª   ª   ª       ObservedBy.php
    ª   ª           ª   ª   ª       ScopedBy.php
    ª   ª           ª   ª   ª       
    ª   ª           ª   ª   +---Casts
    ª   ª           ª   ª   ª       ArrayObject.php
    ª   ª           ª   ª   ª       AsArrayObject.php
    ª   ª           ª   ª   ª       AsCollection.php
    ª   ª           ª   ª   ª       AsEncryptedArrayObject.php
    ª   ª           ª   ª   ª       AsEncryptedCollection.php
    ª   ª           ª   ª   ª       AsEnumArrayObject.php
    ª   ª           ª   ª   ª       AsEnumCollection.php
    ª   ª           ª   ª   ª       AsStringable.php
    ª   ª           ª   ª   ª       Attribute.php
    ª   ª           ª   ª   ª       Json.php
    ª   ª           ª   ª   ª       
    ª   ª           ª   ª   +---Concerns
    ª   ª           ª   ª   ª       GuardsAttributes.php
    ª   ª           ª   ª   ª       HasAttributes.php
    ª   ª           ª   ª   ª       HasEvents.php
    ª   ª           ª   ª   ª       HasGlobalScopes.php
    ª   ª           ª   ª   ª       HasRelationships.php
    ª   ª           ª   ª   ª       HasTimestamps.php
    ª   ª           ª   ª   ª       HasUlids.php
    ª   ª           ª   ª   ª       HasUniqueIds.php
    ª   ª           ª   ª   ª       HasUuids.php
    ª   ª           ª   ª   ª       HidesAttributes.php
    ª   ª           ª   ª   ª       QueriesRelationships.php
    ª   ª           ª   ª   ª       
    ª   ª           ª   ª   +---Factories
    ª   ª           ª   ª   ª       BelongsToManyRelationship.php
    ª   ª           ª   ª   ª       BelongsToRelationship.php
    ª   ª           ª   ª   ª       CrossJoinSequence.php
    ª   ª           ª   ª   ª       Factory.php
    ª   ª           ª   ª   ª       HasFactory.php
    ª   ª           ª   ª   ª       Relationship.php
    ª   ª           ª   ª   ª       Sequence.php
    ª   ª           ª   ª   ª       
    ª   ª           ª   ª   +---Relations
    ª   ª           ª   ª       ª   BelongsTo.php
    ª   ª           ª   ª       ª   BelongsToMany.php
    ª   ª           ª   ª       ª   HasMany.php
    ª   ª           ª   ª       ª   HasManyThrough.php
    ª   ª           ª   ª       ª   HasOne.php
    ª   ª           ª   ª       ª   HasOneOrMany.php
    ª   ª           ª   ª       ª   HasOneThrough.php
    ª   ª           ª   ª       ª   MorphMany.php
    ª   ª           ª   ª       ª   MorphOne.php
    ª   ª           ª   ª       ª   MorphOneOrMany.php
    ª   ª           ª   ª       ª   MorphPivot.php
    ª   ª           ª   ª       ª   MorphTo.php
    ª   ª           ª   ª       ª   MorphToMany.php
    ª   ª           ª   ª       ª   Pivot.php
    ª   ª           ª   ª       ª   Relation.php
    ª   ª           ª   ª       ª   
    ª   ª           ª   ª       +---Concerns
    ª   ª           ª   ª               AsPivot.php
    ª   ª           ª   ª               CanBeOneOfMany.php
    ª   ª           ª   ª               ComparesRelatedModels.php
    ª   ª           ª   ª               InteractsWithDictionary.php
    ª   ª           ª   ª               InteractsWithPivotTable.php
    ª   ª           ª   ª               SupportsDefaultModels.php
    ª   ª           ª   ª               
    ª   ª           ª   +---Events
    ª   ª           ª   ª       ConnectionEstablished.php
    ª   ª           ª   ª       ConnectionEvent.php
    ª   ª           ª   ª       DatabaseBusy.php
    ª   ª           ª   ª       DatabaseRefreshed.php
    ª   ª           ª   ª       MigrationEnded.php
    ª   ª           ª   ª       MigrationEvent.php
    ª   ª           ª   ª       MigrationsEnded.php
    ª   ª           ª   ª       MigrationsEvent.php
    ª   ª           ª   ª       MigrationsStarted.php
    ª   ª           ª   ª       MigrationStarted.php
    ª   ª           ª   ª       ModelPruningFinished.php
    ª   ª           ª   ª       ModelPruningStarting.php
    ª   ª           ª   ª       ModelsPruned.php
    ª   ª           ª   ª       NoPendingMigrations.php
    ª   ª           ª   ª       QueryExecuted.php
    ª   ª           ª   ª       SchemaDumped.php
    ª   ª           ª   ª       SchemaLoaded.php
    ª   ª           ª   ª       StatementPrepared.php
    ª   ª           ª   ª       TransactionBeginning.php
    ª   ª           ª   ª       TransactionCommitted.php
    ª   ª           ª   ª       TransactionCommitting.php
    ª   ª           ª   ª       TransactionRolledBack.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Migrations
    ª   ª           ª   ª   ª   DatabaseMigrationRepository.php
    ª   ª           ª   ª   ª   Migration.php
    ª   ª           ª   ª   ª   MigrationCreator.php
    ª   ª           ª   ª   ª   MigrationRepositoryInterface.php
    ª   ª           ª   ª   ª   Migrator.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---stubs
    ª   ª           ª   ª           migration.create.stub
    ª   ª           ª   ª           migration.stub
    ª   ª           ª   ª           migration.update.stub
    ª   ª           ª   ª           
    ª   ª           ª   +---PDO
    ª   ª           ª   ª   ª   Connection.php
    ª   ª           ª   ª   ª   MySqlDriver.php
    ª   ª           ª   ª   ª   PostgresDriver.php
    ª   ª           ª   ª   ª   SQLiteDriver.php
    ª   ª           ª   ª   ª   SqlServerConnection.php
    ª   ª           ª   ª   ª   SqlServerDriver.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---Concerns
    ª   ª           ª   ª           ConnectsToDatabase.php
    ª   ª           ª   ª           
    ª   ª           ª   +---Query
    ª   ª           ª   ª   ª   Builder.php
    ª   ª           ª   ª   ª   Expression.php
    ª   ª           ª   ª   ª   IndexHint.php
    ª   ª           ª   ª   ª   JoinClause.php
    ª   ª           ª   ª   ª   JoinLateralClause.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---Grammars
    ª   ª           ª   ª   ª       Grammar.php
    ª   ª           ª   ª   ª       MySqlGrammar.php
    ª   ª           ª   ª   ª       PostgresGrammar.php
    ª   ª           ª   ª   ª       SQLiteGrammar.php
    ª   ª           ª   ª   ª       SqlServerGrammar.php
    ª   ª           ª   ª   ª       
    ª   ª           ª   ª   +---Processors
    ª   ª           ª   ª           MySqlProcessor.php
    ª   ª           ª   ª           PostgresProcessor.php
    ª   ª           ª   ª           Processor.php
    ª   ª           ª   ª           SQLiteProcessor.php
    ª   ª           ª   ª           SqlServerProcessor.php
    ª   ª           ª   ª           
    ª   ª           ª   +---Schema
    ª   ª           ª       ª   Blueprint.php
    ª   ª           ª       ª   Builder.php
    ª   ª           ª       ª   ColumnDefinition.php
    ª   ª           ª       ª   ForeignIdColumnDefinition.php
    ª   ª           ª       ª   ForeignKeyDefinition.php
    ª   ª           ª       ª   IndexDefinition.php
    ª   ª           ª       ª   MySqlBuilder.php
    ª   ª           ª       ª   MySqlSchemaState.php
    ª   ª           ª       ª   PostgresBuilder.php
    ª   ª           ª       ª   PostgresSchemaState.php
    ª   ª           ª       ª   SchemaState.php
    ª   ª           ª       ª   SQLiteBuilder.php
    ª   ª           ª       ª   SqliteSchemaState.php
    ª   ª           ª       ª   SqlServerBuilder.php
    ª   ª           ª       ª   
    ª   ª           ª       +---Grammars
    ª   ª           ª               ChangeColumn.php
    ª   ª           ª               Grammar.php
    ª   ª           ª               MySqlGrammar.php
    ª   ª           ª               PostgresGrammar.php
    ª   ª           ª               RenameColumn.php
    ª   ª           ª               SQLiteGrammar.php
    ª   ª           ª               SqlServerGrammar.php
    ª   ª           ª               
    ª   ª           +---Encryption
    ª   ª           ª       composer.json
    ª   ª           ª       Encrypter.php
    ª   ª           ª       EncryptionServiceProvider.php
    ª   ª           ª       LICENSE.md
    ª   ª           ª       MissingAppKeyException.php
    ª   ª           ª       
    ª   ª           +---Events
    ª   ª           ª       CallQueuedListener.php
    ª   ª           ª       composer.json
    ª   ª           ª       Dispatcher.php
    ª   ª           ª       EventServiceProvider.php
    ª   ª           ª       functions.php
    ª   ª           ª       InvokeQueuedClosure.php
    ª   ª           ª       LICENSE.md
    ª   ª           ª       NullDispatcher.php
    ª   ª           ª       QueuedClosure.php
    ª   ª           ª       
    ª   ª           +---Filesystem
    ª   ª           ª       AwsS3V3Adapter.php
    ª   ª           ª       composer.json
    ª   ª           ª       Filesystem.php
    ª   ª           ª       FilesystemAdapter.php
    ª   ª           ª       FilesystemManager.php
    ª   ª           ª       FilesystemServiceProvider.php
    ª   ª           ª       functions.php
    ª   ª           ª       LICENSE.md
    ª   ª           ª       LockableFile.php
    ª   ª           ª       
    ª   ª           +---Foundation
    ª   ª           ª   ª   AliasLoader.php
    ª   ª           ª   ª   Application.php
    ª   ª           ª   ª   CacheBasedMaintenanceMode.php
    ª   ª           ª   ª   Cloud.php
    ª   ª           ª   ª   ComposerScripts.php
    ª   ª           ª   ª   EnvironmentDetector.php
    ª   ª           ª   ª   FileBasedMaintenanceMode.php
    ª   ª           ª   ª   helpers.php
    ª   ª           ª   ª   Inspiring.php
    ª   ª           ª   ª   MaintenanceModeManager.php
    ª   ª           ª   ª   Mix.php
    ª   ª           ª   ª   PackageManifest.php
    ª   ª           ª   ª   Precognition.php
    ª   ª           ª   ª   ProviderRepository.php
    ª   ª           ª   ª   Vite.php
    ª   ª           ª   ª   ViteManifestNotFoundException.php
    ª   ª           ª   ª   
    ª   ª           ª   +---Auth
    ª   ª           ª   ª   ª   EmailVerificationRequest.php
    ª   ª           ª   ª   ª   User.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---Access
    ª   ª           ª   ª           Authorizable.php
    ª   ª           ª   ª           AuthorizesRequests.php
    ª   ª           ª   ª           
    ª   ª           ª   +---Bootstrap
    ª   ª           ª   ª       BootProviders.php
    ª   ª           ª   ª       HandleExceptions.php
    ª   ª           ª   ª       LoadConfiguration.php
    ª   ª           ª   ª       LoadEnvironmentVariables.php
    ª   ª           ª   ª       RegisterFacades.php
    ª   ª           ª   ª       RegisterProviders.php
    ª   ª           ª   ª       SetRequestForConsole.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Bus
    ª   ª           ª   ª       Dispatchable.php
    ª   ª           ª   ª       DispatchesJobs.php
    ª   ª           ª   ª       PendingChain.php
    ª   ª           ª   ª       PendingClosureDispatch.php
    ª   ª           ª   ª       PendingDispatch.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Concerns
    ª   ª           ª   ª       ResolvesDumpSource.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Console
    ª   ª           ª   ª   ª   AboutCommand.php
    ª   ª           ª   ª   ª   CastMakeCommand.php
    ª   ª           ª   ª   ª   ChannelListCommand.php
    ª   ª           ª   ª   ª   ChannelMakeCommand.php
    ª   ª           ª   ª   ª   ClearCompiledCommand.php
    ª   ª           ª   ª   ª   CliDumper.php
    ª   ª           ª   ª   ª   ClosureCommand.php
    ª   ª           ª   ª   ª   ComponentMakeCommand.php
    ª   ª           ª   ª   ª   ConfigCacheCommand.php
    ª   ª           ª   ª   ª   ConfigClearCommand.php
    ª   ª           ª   ª   ª   ConfigShowCommand.php
    ª   ª           ª   ª   ª   ConsoleMakeCommand.php
    ª   ª           ª   ª   ª   DocsCommand.php
    ª   ª           ª   ª   ª   DownCommand.php
    ª   ª           ª   ª   ª   EnvironmentCommand.php
    ª   ª           ª   ª   ª   EnvironmentDecryptCommand.php
    ª   ª           ª   ª   ª   EnvironmentEncryptCommand.php
    ª   ª           ª   ª   ª   EventCacheCommand.php
    ª   ª           ª   ª   ª   EventClearCommand.php
    ª   ª           ª   ª   ª   EventGenerateCommand.php
    ª   ª           ª   ª   ª   EventListCommand.php
    ª   ª           ª   ª   ª   EventMakeCommand.php
    ª   ª           ª   ª   ª   ExceptionMakeCommand.php
    ª   ª           ª   ª   ª   JobMakeCommand.php
    ª   ª           ª   ª   ª   Kernel.php
    ª   ª           ª   ª   ª   KeyGenerateCommand.php
    ª   ª           ª   ª   ª   LangPublishCommand.php
    ª   ª           ª   ª   ª   ListenerMakeCommand.php
    ª   ª           ª   ª   ª   MailMakeCommand.php
    ª   ª           ª   ª   ª   ModelMakeCommand.php
    ª   ª           ª   ª   ª   NotificationMakeCommand.php
    ª   ª           ª   ª   ª   ObserverMakeCommand.php
    ª   ª           ª   ª   ª   OptimizeClearCommand.php
    ª   ª           ª   ª   ª   OptimizeCommand.php
    ª   ª           ª   ª   ª   PackageDiscoverCommand.php
    ª   ª           ª   ª   ª   PolicyMakeCommand.php
    ª   ª           ª   ª   ª   ProviderMakeCommand.php
    ª   ª           ª   ª   ª   QueuedCommand.php
    ª   ª           ª   ª   ª   RequestMakeCommand.php
    ª   ª           ª   ª   ª   ResourceMakeCommand.php
    ª   ª           ª   ª   ª   RouteCacheCommand.php
    ª   ª           ª   ª   ª   RouteClearCommand.php
    ª   ª           ª   ª   ª   RouteListCommand.php
    ª   ª           ª   ª   ª   RuleMakeCommand.php
    ª   ª           ª   ª   ª   ScopeMakeCommand.php
    ª   ª           ª   ª   ª   ServeCommand.php
    ª   ª           ª   ª   ª   StorageLinkCommand.php
    ª   ª           ª   ª   ª   StorageUnlinkCommand.php
    ª   ª           ª   ª   ª   StubPublishCommand.php
    ª   ª           ª   ª   ª   TestMakeCommand.php
    ª   ª           ª   ª   ª   UpCommand.php
    ª   ª           ª   ª   ª   VendorPublishCommand.php
    ª   ª           ª   ª   ª   ViewCacheCommand.php
    ª   ª           ª   ª   ª   ViewClearCommand.php
    ª   ª           ª   ª   ª   ViewMakeCommand.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---stubs
    ª   ª           ª   ª           cast.inbound.stub
    ª   ª           ª   ª           cast.stub
    ª   ª           ª   ª           channel.stub
    ª   ª           ª   ª           console.stub
    ª   ª           ª   ª           event.stub
    ª   ª           ª   ª           exception-render-report.stub
    ª   ª           ª   ª           exception-render.stub
    ª   ª           ª   ª           exception-report.stub
    ª   ª           ª   ª           exception.stub
    ª   ª           ª   ª           job.queued.stub
    ª   ª           ª   ª           job.stub
    ª   ª           ª   ª           listener-duck.stub
    ª   ª           ª   ª           listener-queued-duck.stub
    ª   ª           ª   ª           listener-queued.stub
    ª   ª           ª   ª           listener.stub
    ª   ª           ª   ª           mail.stub
    ª   ª           ª   ª           maintenance-mode.stub
    ª   ª           ª   ª           markdown-mail.stub
    ª   ª           ª   ª           markdown-notification.stub
    ª   ª           ª   ª           markdown.stub
    ª   ª           ª   ª           model.morph-pivot.stub
    ª   ª           ª   ª           model.pivot.stub
    ª   ª           ª   ª           model.stub
    ª   ª           ª   ª           notification.stub
    ª   ª           ª   ª           observer.plain.stub
    ª   ª           ª   ª           observer.stub
    ª   ª           ª   ª           pest.stub
    ª   ª           ª   ª           pest.unit.stub
    ª   ª           ª   ª           policy.plain.stub
    ª   ª           ª   ª           policy.stub
    ª   ª           ª   ª           provider.stub
    ª   ª           ª   ª           request.stub
    ª   ª           ª   ª           resource-collection.stub
    ª   ª           ª   ª           resource.stub
    ª   ª           ª   ª           routes.stub
    ª   ª           ª   ª           rule.implicit.stub
    ª   ª           ª   ª           rule.stub
    ª   ª           ª   ª           scope.stub
    ª   ª           ª   ª           test.stub
    ª   ª           ª   ª           test.unit.stub
    ª   ª           ª   ª           view-component.stub
    ª   ª           ª   ª           view.pest.stub
    ª   ª           ª   ª           view.stub
    ª   ª           ª   ª           view.test.stub
    ª   ª           ª   ª           
    ª   ª           ª   +---Events
    ª   ª           ª   ª       DiscoverEvents.php
    ª   ª           ª   ª       Dispatchable.php
    ª   ª           ª   ª       LocaleUpdated.php
    ª   ª           ª   ª       MaintenanceModeDisabled.php
    ª   ª           ª   ª       MaintenanceModeEnabled.php
    ª   ª           ª   ª       PublishingStubs.php
    ª   ª           ª   ª       VendorTagPublished.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Exceptions
    ª   ª           ª   ª   ª   Handler.php
    ª   ª           ª   ª   ª   RegisterErrorViewPaths.php
    ª   ª           ª   ª   ª   ReportableHandler.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---views
    ª   ª           ª   ª   ª       401.blade.php
    ª   ª           ª   ª   ª       402.blade.php
    ª   ª           ª   ª   ª       403.blade.php
    ª   ª           ª   ª   ª       404.blade.php
    ª   ª           ª   ª   ª       419.blade.php
    ª   ª           ª   ª   ª       429.blade.php
    ª   ª           ª   ª   ª       500.blade.php
    ª   ª           ª   ª   ª       503.blade.php
    ª   ª           ª   ª   ª       layout.blade.php
    ª   ª           ª   ª   ª       minimal.blade.php
    ª   ª           ª   ª   ª       
    ª   ª           ª   ª   +---Whoops
    ª   ª           ª   ª           WhoopsExceptionRenderer.php
    ª   ª           ª   ª           WhoopsHandler.php
    ª   ª           ª   ª           
    ª   ª           ª   +---Http
    ª   ª           ª   ª   ª   FormRequest.php
    ª   ª           ª   ª   ª   HtmlDumper.php
    ª   ª           ª   ª   ª   Kernel.php
    ª   ª           ª   ª   ª   MaintenanceModeBypassCookie.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---Events
    ª   ª           ª   ª   ª       RequestHandled.php
    ª   ª           ª   ª   ª       
    ª   ª           ª   ª   +---Middleware
    ª   ª           ª   ª           CheckForMaintenanceMode.php
    ª   ª           ª   ª           ConvertEmptyStringsToNull.php
    ª   ª           ª   ª           HandlePrecognitiveRequests.php
    ª   ª           ª   ª           PreventRequestsDuringMaintenance.php
    ª   ª           ª   ª           TransformsRequest.php
    ª   ª           ª   ª           TrimStrings.php
    ª   ª           ª   ª           ValidatePostSize.php
    ª   ª           ª   ª           VerifyCsrfToken.php
    ª   ª           ª   ª           
    ª   ª           ª   +---Providers
    ª   ª           ª   ª       ArtisanServiceProvider.php
    ª   ª           ª   ª       ComposerServiceProvider.php
    ª   ª           ª   ª       ConsoleSupportServiceProvider.php
    ª   ª           ª   ª       FormRequestServiceProvider.php
    ª   ª           ª   ª       FoundationServiceProvider.php
    ª   ª           ª   ª       
    ª   ª           ª   +---resources
    ª   ª           ª   ª       server.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Routing
    ª   ª           ª   ª       PrecognitionCallableDispatcher.php
    ª   ª           ª   ª       PrecognitionControllerDispatcher.php
    ª   ª           ª   ª       
    ª   ª           ª   +---stubs
    ª   ª           ª   ª       facade.stub
    ª   ª           ª   ª       
    ª   ª           ª   +---Support
    ª   ª           ª   ª   +---Providers
    ª   ª           ª   ª           AuthServiceProvider.php
    ª   ª           ª   ª           EventServiceProvider.php
    ª   ª           ª   ª           RouteServiceProvider.php
    ª   ª           ª   ª           
    ª   ª           ª   +---Testing
    ª   ª           ª   ª   ª   DatabaseMigrations.php
    ª   ª           ª   ª   ª   DatabaseTransactions.php
    ª   ª           ª   ª   ª   DatabaseTransactionsManager.php
    ª   ª           ª   ª   ª   DatabaseTruncation.php
    ª   ª           ª   ª   ª   LazilyRefreshDatabase.php
    ª   ª           ª   ª   ª   RefreshDatabase.php
    ª   ª           ª   ª   ª   RefreshDatabaseState.php
    ª   ª           ª   ª   ª   TestCase.php
    ª   ª           ª   ª   ª   WithConsoleEvents.php
    ª   ª           ª   ª   ª   WithFaker.php
    ª   ª           ª   ª   ª   WithoutEvents.php
    ª   ª           ª   ª   ª   WithoutMiddleware.php
    ª   ª           ª   ª   ª   Wormhole.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---Concerns
    ª   ª           ª   ª   ª       InteractsWithAuthentication.php
    ª   ª           ª   ª   ª       InteractsWithConsole.php
    ª   ª           ª   ª   ª       InteractsWithContainer.php
    ª   ª           ª   ª   ª       InteractsWithDatabase.php
    ª   ª           ª   ª   ª       InteractsWithDeprecationHandling.php
    ª   ª           ª   ª   ª       InteractsWithExceptionHandling.php
    ª   ª           ª   ª   ª       InteractsWithRedis.php
    ª   ª           ª   ª   ª       InteractsWithSession.php
    ª   ª           ª   ª   ª       InteractsWithTestCaseLifecycle.php
    ª   ª           ª   ª   ª       InteractsWithTime.php
    ª   ª           ª   ª   ª       InteractsWithViews.php
    ª   ª           ª   ª   ª       MakesHttpRequests.php
    ª   ª           ª   ª   ª       
    ª   ª           ª   ª   +---Traits
    ª   ª           ª   ª           CanConfigureMigrationCommands.php
    ª   ª           ª   ª           
    ª   ª           ª   +---Validation
    ª   ª           ª           ValidatesRequests.php
    ª   ª           ª           
    ª   ª           +---Hashing
    ª   ª           ª       AbstractHasher.php
    ª   ª           ª       Argon2IdHasher.php
    ª   ª           ª       ArgonHasher.php
    ª   ª           ª       BcryptHasher.php
    ª   ª           ª       composer.json
    ª   ª           ª       HashManager.php
    ª   ª           ª       HashServiceProvider.php
    ª   ª           ª       LICENSE.md
    ª   ª           ª       
    ª   ª           +---Http
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   File.php
    ª   ª           ª   ª   FileHelpers.php
    ª   ª           ª   ª   JsonResponse.php
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   RedirectResponse.php
    ª   ª           ª   ª   Request.php
    ª   ª           ª   ª   Response.php
    ª   ª           ª   ª   ResponseTrait.php
    ª   ª           ª   ª   UploadedFile.php
    ª   ª           ª   ª   
    ª   ª           ª   +---Client
    ª   ª           ª   ª   ª   ConnectionException.php
    ª   ª           ª   ª   ª   Factory.php
    ª   ª           ª   ª   ª   HttpClientException.php
    ª   ª           ª   ª   ª   PendingRequest.php
    ª   ª           ª   ª   ª   Pool.php
    ª   ª           ª   ª   ª   Request.php
    ª   ª           ª   ª   ª   RequestException.php
    ª   ª           ª   ª   ª   Response.php
    ª   ª           ª   ª   ª   ResponseSequence.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---Concerns
    ª   ª           ª   ª   ª       DeterminesStatusCode.php
    ª   ª           ª   ª   ª       
    ª   ª           ª   ª   +---Events
    ª   ª           ª   ª           ConnectionFailed.php
    ª   ª           ª   ª           RequestSending.php
    ª   ª           ª   ª           ResponseReceived.php
    ª   ª           ª   ª           
    ª   ª           ª   +---Concerns
    ª   ª           ª   ª       CanBePrecognitive.php
    ª   ª           ª   ª       InteractsWithContentTypes.php
    ª   ª           ª   ª       InteractsWithFlashData.php
    ª   ª           ª   ª       InteractsWithInput.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Exceptions
    ª   ª           ª   ª       HttpResponseException.php
    ª   ª           ª   ª       PostTooLargeException.php
    ª   ª           ª   ª       ThrottleRequestsException.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Middleware
    ª   ª           ª   ª       AddLinkHeadersForPreloadedAssets.php
    ª   ª           ª   ª       CheckResponseForModifications.php
    ª   ª           ª   ª       FrameGuard.php
    ª   ª           ª   ª       HandleCors.php
    ª   ª           ª   ª       SetCacheHeaders.php
    ª   ª           ª   ª       TrustHosts.php
    ª   ª           ª   ª       TrustProxies.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Resources
    ª   ª           ª   ª   ª   CollectsResources.php
    ª   ª           ª   ª   ª   ConditionallyLoadsAttributes.php
    ª   ª           ª   ª   ª   DelegatesToResource.php
    ª   ª           ª   ª   ª   MergeValue.php
    ª   ª           ª   ª   ª   MissingValue.php
    ª   ª           ª   ª   ª   PotentiallyMissing.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---Json
    ª   ª           ª   ª           AnonymousResourceCollection.php
    ª   ª           ª   ª           JsonResource.php
    ª   ª           ª   ª           PaginatedResourceResponse.php
    ª   ª           ª   ª           ResourceCollection.php
    ª   ª           ª   ª           ResourceResponse.php
    ª   ª           ª   ª           
    ª   ª           ª   +---Testing
    ª   ª           ª           File.php
    ª   ª           ª           FileFactory.php
    ª   ª           ª           MimeType.php
    ª   ª           ª           
    ª   ª           +---Log
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   Logger.php
    ª   ª           ª   ª   LogManager.php
    ª   ª           ª   ª   LogServiceProvider.php
    ª   ª           ª   ª   ParsesLogConfiguration.php
    ª   ª           ª   ª   
    ª   ª           ª   +---Events
    ª   ª           ª           MessageLogged.php
    ª   ª           ª           
    ª   ª           +---Macroable
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   
    ª   ª           ª   +---Traits
    ª   ª           ª           Macroable.php
    ª   ª           ª           
    ª   ª           +---Mail
    ª   ª           ª   ª   Attachment.php
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   Mailable.php
    ª   ª           ª   ª   Mailer.php
    ª   ª           ª   ª   MailManager.php
    ª   ª           ª   ª   MailServiceProvider.php
    ª   ª           ª   ª   Markdown.php
    ª   ª           ª   ª   Message.php
    ª   ª           ª   ª   PendingMail.php
    ª   ª           ª   ª   SendQueuedMailable.php
    ª   ª           ª   ª   SentMessage.php
    ª   ª           ª   ª   TextMessage.php
    ª   ª           ª   ª   
    ª   ª           ª   +---Events
    ª   ª           ª   ª       MessageSending.php
    ª   ª           ª   ª       MessageSent.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Mailables
    ª   ª           ª   ª       Address.php
    ª   ª           ª   ª       Attachment.php
    ª   ª           ª   ª       Content.php
    ª   ª           ª   ª       Envelope.php
    ª   ª           ª   ª       Headers.php
    ª   ª           ª   ª       
    ª   ª           ª   +---resources
    ª   ª           ª   ª   +---views
    ª   ª           ª   ª       +---html
    ª   ª           ª   ª       ª   ª   button.blade.php
    ª   ª           ª   ª       ª   ª   footer.blade.php
    ª   ª           ª   ª       ª   ª   header.blade.php
    ª   ª           ª   ª       ª   ª   layout.blade.php
    ª   ª           ª   ª       ª   ª   message.blade.php
    ª   ª           ª   ª       ª   ª   panel.blade.php
    ª   ª           ª   ª       ª   ª   subcopy.blade.php
    ª   ª           ª   ª       ª   ª   table.blade.php
    ª   ª           ª   ª       ª   ª   
    ª   ª           ª   ª       ª   +---themes
    ª   ª           ª   ª       ª           default.css
    ª   ª           ª   ª       ª           
    ª   ª           ª   ª       +---text
    ª   ª           ª   ª               button.blade.php
    ª   ª           ª   ª               footer.blade.php
    ª   ª           ª   ª               header.blade.php
    ª   ª           ª   ª               layout.blade.php
    ª   ª           ª   ª               message.blade.php
    ª   ª           ª   ª               panel.blade.php
    ª   ª           ª   ª               subcopy.blade.php
    ª   ª           ª   ª               table.blade.php
    ª   ª           ª   ª               
    ª   ª           ª   +---Transport
    ª   ª           ª           ArrayTransport.php
    ª   ª           ª           LogTransport.php
    ª   ª           ª           SesTransport.php
    ª   ª           ª           SesV2Transport.php
    ª   ª           ª           
    ª   ª           +---Notifications
    ª   ª           ª   ª   Action.php
    ª   ª           ª   ª   AnonymousNotifiable.php
    ª   ª           ª   ª   ChannelManager.php
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   DatabaseNotification.php
    ª   ª           ª   ª   DatabaseNotificationCollection.php
    ª   ª           ª   ª   HasDatabaseNotifications.php
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   Notifiable.php
    ª   ª           ª   ª   Notification.php
    ª   ª           ª   ª   NotificationSender.php
    ª   ª           ª   ª   NotificationServiceProvider.php
    ª   ª           ª   ª   RoutesNotifications.php
    ª   ª           ª   ª   SendQueuedNotifications.php
    ª   ª           ª   ª   
    ª   ª           ª   +---Channels
    ª   ª           ª   ª       BroadcastChannel.php
    ª   ª           ª   ª       DatabaseChannel.php
    ª   ª           ª   ª       MailChannel.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Console
    ª   ª           ª   ª   ª   NotificationTableCommand.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---stubs
    ª   ª           ª   ª           notifications.stub
    ª   ª           ª   ª           
    ª   ª           ª   +---Events
    ª   ª           ª   ª       BroadcastNotificationCreated.php
    ª   ª           ª   ª       NotificationFailed.php
    ª   ª           ª   ª       NotificationSending.php
    ª   ª           ª   ª       NotificationSent.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Messages
    ª   ª           ª   ª       BroadcastMessage.php
    ª   ª           ª   ª       DatabaseMessage.php
    ª   ª           ª   ª       MailMessage.php
    ª   ª           ª   ª       SimpleMessage.php
    ª   ª           ª   ª       
    ª   ª           ª   +---resources
    ª   ª           ª       +---views
    ª   ª           ª               email.blade.php
    ª   ª           ª               
    ª   ª           +---Pagination
    ª   ª           ª   ª   AbstractCursorPaginator.php
    ª   ª           ª   ª   AbstractPaginator.php
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   Cursor.php
    ª   ª           ª   ª   CursorPaginator.php
    ª   ª           ª   ª   LengthAwarePaginator.php
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   PaginationServiceProvider.php
    ª   ª           ª   ª   PaginationState.php
    ª   ª           ª   ª   Paginator.php
    ª   ª           ª   ª   UrlWindow.php
    ª   ª           ª   ª   
    ª   ª           ª   +---resources
    ª   ª           ª       +---views
    ª   ª           ª               bootstrap-4.blade.php
    ª   ª           ª               bootstrap-5.blade.php
    ª   ª           ª               default.blade.php
    ª   ª           ª               semantic-ui.blade.php
    ª   ª           ª               simple-bootstrap-4.blade.php
    ª   ª           ª               simple-bootstrap-5.blade.php
    ª   ª           ª               simple-default.blade.php
    ª   ª           ª               simple-tailwind.blade.php
    ª   ª           ª               tailwind.blade.php
    ª   ª           ª               
    ª   ª           +---Pipeline
    ª   ª           ª       composer.json
    ª   ª           ª       Hub.php
    ª   ª           ª       LICENSE.md
    ª   ª           ª       Pipeline.php
    ª   ª           ª       PipelineServiceProvider.php
    ª   ª           ª       
    ª   ª           +---Process
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   Factory.php
    ª   ª           ª   ª   FakeInvokedProcess.php
    ª   ª           ª   ª   FakeProcessDescription.php
    ª   ª           ª   ª   FakeProcessResult.php
    ª   ª           ª   ª   FakeProcessSequence.php
    ª   ª           ª   ª   InvokedProcess.php
    ª   ª           ª   ª   InvokedProcessPool.php
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   PendingProcess.php
    ª   ª           ª   ª   Pipe.php
    ª   ª           ª   ª   Pool.php
    ª   ª           ª   ª   ProcessPoolResults.php
    ª   ª           ª   ª   ProcessResult.php
    ª   ª           ª   ª   
    ª   ª           ª   +---Exceptions
    ª   ª           ª           ProcessFailedException.php
    ª   ª           ª           ProcessTimedOutException.php
    ª   ª           ª           
    ª   ª           +---Queue
    ª   ª           ª   ª   BeanstalkdQueue.php
    ª   ª           ª   ª   CallQueuedClosure.php
    ª   ª           ª   ª   CallQueuedHandler.php
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   DatabaseQueue.php
    ª   ª           ª   ª   InteractsWithQueue.php
    ª   ª           ª   ª   InvalidPayloadException.php
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   Listener.php
    ª   ª           ª   ª   ListenerOptions.php
    ª   ª           ª   ª   LuaScripts.php
    ª   ª           ª   ª   ManuallyFailedException.php
    ª   ª           ª   ª   MaxAttemptsExceededException.php
    ª   ª           ª   ª   NullQueue.php
    ª   ª           ª   ª   Queue.php
    ª   ª           ª   ª   QueueManager.php
    ª   ª           ª   ª   QueueServiceProvider.php
    ª   ª           ª   ª   README.md
    ª   ª           ª   ª   RedisQueue.php
    ª   ª           ª   ª   SerializesAndRestoresModelIdentifiers.php
    ª   ª           ª   ª   SerializesModels.php
    ª   ª           ª   ª   SqsQueue.php
    ª   ª           ª   ª   SyncQueue.php
    ª   ª           ª   ª   TimeoutExceededException.php
    ª   ª           ª   ª   Worker.php
    ª   ª           ª   ª   WorkerOptions.php
    ª   ª           ª   ª   
    ª   ª           ª   +---Attributes
    ª   ª           ª   ª       WithoutRelations.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Capsule
    ª   ª           ª   ª       Manager.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Connectors
    ª   ª           ª   ª       BeanstalkdConnector.php
    ª   ª           ª   ª       ConnectorInterface.php
    ª   ª           ª   ª       DatabaseConnector.php
    ª   ª           ª   ª       NullConnector.php
    ª   ª           ª   ª       RedisConnector.php
    ª   ª           ª   ª       SqsConnector.php
    ª   ª           ª   ª       SyncConnector.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Console
    ª   ª           ª   ª   ª   BatchesTableCommand.php
    ª   ª           ª   ª   ª   ClearCommand.php
    ª   ª           ª   ª   ª   FailedTableCommand.php
    ª   ª           ª   ª   ª   FlushFailedCommand.php
    ª   ª           ª   ª   ª   ForgetFailedCommand.php
    ª   ª           ª   ª   ª   ListenCommand.php
    ª   ª           ª   ª   ª   ListFailedCommand.php
    ª   ª           ª   ª   ª   MonitorCommand.php
    ª   ª           ª   ª   ª   PruneBatchesCommand.php
    ª   ª           ª   ª   ª   PruneFailedJobsCommand.php
    ª   ª           ª   ª   ª   RestartCommand.php
    ª   ª           ª   ª   ª   RetryBatchCommand.php
    ª   ª           ª   ª   ª   RetryCommand.php
    ª   ª           ª   ª   ª   TableCommand.php
    ª   ª           ª   ª   ª   WorkCommand.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---stubs
    ª   ª           ª   ª           batches.stub
    ª   ª           ª   ª           failed_jobs.stub
    ª   ª           ª   ª           jobs.stub
    ª   ª           ª   ª           
    ª   ª           ª   +---Events
    ª   ª           ª   ª       JobExceptionOccurred.php
    ª   ª           ª   ª       JobFailed.php
    ª   ª           ª   ª       JobPopped.php
    ª   ª           ª   ª       JobPopping.php
    ª   ª           ª   ª       JobProcessed.php
    ª   ª           ª   ª       JobProcessing.php
    ª   ª           ª   ª       JobQueued.php
    ª   ª           ª   ª       JobQueueing.php
    ª   ª           ª   ª       JobReleasedAfterException.php
    ª   ª           ª   ª       JobRetryRequested.php
    ª   ª           ª   ª       JobTimedOut.php
    ª   ª           ª   ª       Looping.php
    ª   ª           ª   ª       QueueBusy.php
    ª   ª           ª   ª       WorkerStopping.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Failed
    ª   ª           ª   ª       CountableFailedJobProvider.php
    ª   ª           ª   ª       DatabaseFailedJobProvider.php
    ª   ª           ª   ª       DatabaseUuidFailedJobProvider.php
    ª   ª           ª   ª       DynamoDbFailedJobProvider.php
    ª   ª           ª   ª       FailedJobProviderInterface.php
    ª   ª           ª   ª       FileFailedJobProvider.php
    ª   ª           ª   ª       NullFailedJobProvider.php
    ª   ª           ª   ª       PrunableFailedJobProvider.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Jobs
    ª   ª           ª   ª       BeanstalkdJob.php
    ª   ª           ª   ª       DatabaseJob.php
    ª   ª           ª   ª       DatabaseJobRecord.php
    ª   ª           ª   ª       Job.php
    ª   ª           ª   ª       JobName.php
    ª   ª           ª   ª       RedisJob.php
    ª   ª           ª   ª       SqsJob.php
    ª   ª           ª   ª       SyncJob.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Middleware
    ª   ª           ª           RateLimited.php
    ª   ª           ª           RateLimitedWithRedis.php
    ª   ª           ª           SkipIfBatchCancelled.php
    ª   ª           ª           ThrottlesExceptions.php
    ª   ª           ª           ThrottlesExceptionsWithRedis.php
    ª   ª           ª           WithoutOverlapping.php
    ª   ª           ª           
    ª   ª           +---Redis
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   RedisManager.php
    ª   ª           ª   ª   RedisServiceProvider.php
    ª   ª           ª   ª   
    ª   ª           ª   +---Connections
    ª   ª           ª   ª       Connection.php
    ª   ª           ª   ª       PacksPhpRedisValues.php
    ª   ª           ª   ª       PhpRedisClusterConnection.php
    ª   ª           ª   ª       PhpRedisConnection.php
    ª   ª           ª   ª       PredisClusterConnection.php
    ª   ª           ª   ª       PredisConnection.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Connectors
    ª   ª           ª   ª       PhpRedisConnector.php
    ª   ª           ª   ª       PredisConnector.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Events
    ª   ª           ª   ª       CommandExecuted.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Limiters
    ª   ª           ª           ConcurrencyLimiter.php
    ª   ª           ª           ConcurrencyLimiterBuilder.php
    ª   ª           ª           DurationLimiter.php
    ª   ª           ª           DurationLimiterBuilder.php
    ª   ª           ª           
    ª   ª           +---Routing
    ª   ª           ª   ª   AbstractRouteCollection.php
    ª   ª           ª   ª   CallableDispatcher.php
    ª   ª           ª   ª   CompiledRouteCollection.php
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   Controller.php
    ª   ª           ª   ª   ControllerDispatcher.php
    ª   ª           ª   ª   ControllerMiddlewareOptions.php
    ª   ª           ª   ª   CreatesRegularExpressionRouteConstraints.php
    ª   ª           ª   ª   FiltersControllerMiddleware.php
    ª   ª           ª   ª   ImplicitRouteBinding.php
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   MiddlewareNameResolver.php
    ª   ª           ª   ª   PendingResourceRegistration.php
    ª   ª           ª   ª   PendingSingletonResourceRegistration.php
    ª   ª           ª   ª   Pipeline.php
    ª   ª           ª   ª   RedirectController.php
    ª   ª           ª   ª   Redirector.php
    ª   ª           ª   ª   ResolvesRouteDependencies.php
    ª   ª           ª   ª   ResourceRegistrar.php
    ª   ª           ª   ª   ResponseFactory.php
    ª   ª           ª   ª   Route.php
    ª   ª           ª   ª   RouteAction.php
    ª   ª           ª   ª   RouteBinding.php
    ª   ª           ª   ª   RouteCollection.php
    ª   ª           ª   ª   RouteCollectionInterface.php
    ª   ª           ª   ª   RouteDependencyResolverTrait.php
    ª   ª           ª   ª   RouteFileRegistrar.php
    ª   ª           ª   ª   RouteGroup.php
    ª   ª           ª   ª   RouteParameterBinder.php
    ª   ª           ª   ª   Router.php
    ª   ª           ª   ª   RouteRegistrar.php
    ª   ª           ª   ª   RouteSignatureParameters.php
    ª   ª           ª   ª   RouteUri.php
    ª   ª           ª   ª   RouteUrlGenerator.php
    ª   ª           ª   ª   RoutingServiceProvider.php
    ª   ª           ª   ª   SortedMiddleware.php
    ª   ª           ª   ª   UrlGenerator.php
    ª   ª           ª   ª   ViewController.php
    ª   ª           ª   ª   
    ª   ª           ª   +---Console
    ª   ª           ª   ª   ª   ControllerMakeCommand.php
    ª   ª           ª   ª   ª   MiddlewareMakeCommand.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---stubs
    ª   ª           ª   ª           controller.api.stub
    ª   ª           ª   ª           controller.invokable.stub
    ª   ª           ª   ª           controller.model.api.stub
    ª   ª           ª   ª           controller.model.stub
    ª   ª           ª   ª           controller.nested.api.stub
    ª   ª           ª   ª           controller.nested.singleton.api.stub
    ª   ª           ª   ª           controller.nested.singleton.stub
    ª   ª           ª   ª           controller.nested.stub
    ª   ª           ª   ª           controller.plain.stub
    ª   ª           ª   ª           controller.singleton.api.stub
    ª   ª           ª   ª           controller.singleton.stub
    ª   ª           ª   ª           controller.stub
    ª   ª           ª   ª           middleware.stub
    ª   ª           ª   ª           
    ª   ª           ª   +---Contracts
    ª   ª           ª   ª       CallableDispatcher.php
    ª   ª           ª   ª       ControllerDispatcher.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Controllers
    ª   ª           ª   ª       HasMiddleware.php
    ª   ª           ª   ª       Middleware.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Events
    ª   ª           ª   ª       PreparingResponse.php
    ª   ª           ª   ª       ResponsePrepared.php
    ª   ª           ª   ª       RouteMatched.php
    ª   ª           ª   ª       Routing.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Exceptions
    ª   ª           ª   ª       BackedEnumCaseNotFoundException.php
    ª   ª           ª   ª       InvalidSignatureException.php
    ª   ª           ª   ª       StreamedResponseException.php
    ª   ª           ª   ª       UrlGenerationException.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Matching
    ª   ª           ª   ª       HostValidator.php
    ª   ª           ª   ª       MethodValidator.php
    ª   ª           ª   ª       SchemeValidator.php
    ª   ª           ª   ª       UriValidator.php
    ª   ª           ª   ª       ValidatorInterface.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Middleware
    ª   ª           ª           SubstituteBindings.php
    ª   ª           ª           ThrottleRequests.php
    ª   ª           ª           ThrottleRequestsWithRedis.php
    ª   ª           ª           ValidateSignature.php
    ª   ª           ª           
    ª   ª           +---Session
    ª   ª           ª   ª   ArraySessionHandler.php
    ª   ª           ª   ª   CacheBasedSessionHandler.php
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   CookieSessionHandler.php
    ª   ª           ª   ª   DatabaseSessionHandler.php
    ª   ª           ª   ª   EncryptedStore.php
    ª   ª           ª   ª   ExistenceAwareInterface.php
    ª   ª           ª   ª   FileSessionHandler.php
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   NullSessionHandler.php
    ª   ª           ª   ª   SessionManager.php
    ª   ª           ª   ª   SessionServiceProvider.php
    ª   ª           ª   ª   Store.php
    ª   ª           ª   ª   SymfonySessionDecorator.php
    ª   ª           ª   ª   TokenMismatchException.php
    ª   ª           ª   ª   
    ª   ª           ª   +---Console
    ª   ª           ª   ª   ª   SessionTableCommand.php
    ª   ª           ª   ª   ª   
    ª   ª           ª   ª   +---stubs
    ª   ª           ª   ª           database.stub
    ª   ª           ª   ª           
    ª   ª           ª   +---Middleware
    ª   ª           ª           AuthenticateSession.php
    ª   ª           ª           StartSession.php
    ª   ª           ª           
    ª   ª           +---Support
    ª   ª           ª   ª   AggregateServiceProvider.php
    ª   ª           ª   ª   Benchmark.php
    ª   ª           ª   ª   Carbon.php
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   Composer.php
    ª   ª           ª   ª   ConfigurationUrlParser.php
    ª   ª           ª   ª   DateFactory.php
    ª   ª           ª   ª   DefaultProviders.php
    ª   ª           ª   ª   Env.php
    ª   ª           ª   ª   Fluent.php
    ª   ª           ª   ª   helpers.php
    ª   ª           ª   ª   HigherOrderTapProxy.php
    ª   ª           ª   ª   HtmlString.php
    ª   ª           ª   ª   InteractsWithTime.php
    ª   ª           ª   ª   Js.php
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   Lottery.php
    ª   ª           ª   ª   Manager.php
    ª   ª           ª   ª   MessageBag.php
    ª   ª           ª   ª   MultipleInstanceManager.php
    ª   ª           ª   ª   NamespacedItemResolver.php
    ª   ª           ª   ª   Number.php
    ª   ª           ª   ª   Optional.php
    ª   ª           ª   ª   Pluralizer.php
    ª   ª           ª   ª   ProcessUtils.php
    ª   ª           ª   ª   Reflector.php
    ª   ª           ª   ª   ServiceProvider.php
    ª   ª           ª   ª   Sleep.php
    ª   ª           ª   ª   Str.php
    ª   ª           ª   ª   Stringable.php
    ª   ª           ª   ª   Timebox.php
    ª   ª           ª   ª   ValidatedInput.php
    ª   ª           ª   ª   ViewErrorBag.php
    ª   ª           ª   ª   
    ª   ª           ª   +---Exceptions
    ª   ª           ª   ª       MathException.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Facades
    ª   ª           ª   ª       App.php
    ª   ª           ª   ª       Artisan.php
    ª   ª           ª   ª       Auth.php
    ª   ª           ª   ª       Blade.php
    ª   ª           ª   ª       Broadcast.php
    ª   ª           ª   ª       Bus.php
    ª   ª           ª   ª       Cache.php
    ª   ª           ª   ª       Config.php
    ª   ª           ª   ª       Cookie.php
    ª   ª           ª   ª       Crypt.php
    ª   ª           ª   ª       Date.php
    ª   ª           ª   ª       DB.php
    ª   ª           ª   ª       Event.php
    ª   ª           ª   ª       Facade.php
    ª   ª           ª   ª       File.php
    ª   ª           ª   ª       Gate.php
    ª   ª           ª   ª       Hash.php
    ª   ª           ª   ª       Http.php
    ª   ª           ª   ª       Lang.php
    ª   ª           ª   ª       Log.php
    ª   ª           ª   ª       Mail.php
    ª   ª           ª   ª       Notification.php
    ª   ª           ª   ª       ParallelTesting.php
    ª   ª           ª   ª       Password.php
    ª   ª           ª   ª       Pipeline.php
    ª   ª           ª   ª       Process.php
    ª   ª           ª   ª       Queue.php
    ª   ª           ª   ª       RateLimiter.php
    ª   ª           ª   ª       Redirect.php
    ª   ª           ª   ª       Redis.php
    ª   ª           ª   ª       Request.php
    ª   ª           ª   ª       Response.php
    ª   ª           ª   ª       Route.php
    ª   ª           ª   ª       Schema.php
    ª   ª           ª   ª       Session.php
    ª   ª           ª   ª       Storage.php
    ª   ª           ª   ª       URL.php
    ª   ª           ª   ª       Validator.php
    ª   ª           ª   ª       View.php
    ª   ª           ª   ª       Vite.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Testing
    ª   ª           ª   ª   +---Fakes
    ª   ª           ª   ª           BatchFake.php
    ª   ª           ª   ª           BatchRepositoryFake.php
    ª   ª           ª   ª           BusFake.php
    ª   ª           ª   ª           ChainedBatchTruthTest.php
    ª   ª           ª   ª           EventFake.php
    ª   ª           ª   ª           Fake.php
    ª   ª           ª   ª           MailFake.php
    ª   ª           ª   ª           NotificationFake.php
    ª   ª           ª   ª           PendingBatchFake.php
    ª   ª           ª   ª           PendingChainFake.php
    ª   ª           ª   ª           PendingMailFake.php
    ª   ª           ª   ª           QueueFake.php
    ª   ª           ª   ª           
    ª   ª           ª   +---Traits
    ª   ª           ª           CapsuleManagerTrait.php
    ª   ª           ª           ForwardsCalls.php
    ª   ª           ª           Localizable.php
    ª   ª           ª           ReflectsClosures.php
    ª   ª           ª           Tappable.php
    ª   ª           ª           
    ª   ª           +---Testing
    ª   ª           ª   ª   Assert.php
    ª   ª           ª   ª   AssertableJsonString.php
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   LoggedExceptionCollection.php
    ª   ª           ª   ª   ParallelConsoleOutput.php
    ª   ª           ª   ª   ParallelRunner.php
    ª   ª           ª   ª   ParallelTesting.php
    ª   ª           ª   ª   ParallelTestingServiceProvider.php
    ª   ª           ª   ª   PendingCommand.php
    ª   ª           ª   ª   TestComponent.php
    ª   ª           ª   ª   TestResponse.php
    ª   ª           ª   ª   TestView.php
    ª   ª           ª   ª   
    ª   ª           ª   +---Concerns
    ª   ª           ª   ª       AssertsStatusCodes.php
    ª   ª           ª   ª       RunsInParallel.php
    ª   ª           ª   ª       TestDatabases.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Constraints
    ª   ª           ª   ª       ArraySubset.php
    ª   ª           ª   ª       CountInDatabase.php
    ª   ª           ª   ª       HasInDatabase.php
    ª   ª           ª   ª       NotSoftDeletedInDatabase.php
    ª   ª           ª   ª       SeeInOrder.php
    ª   ª           ª   ª       SoftDeletedInDatabase.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Exceptions
    ª   ª           ª   ª       InvalidArgumentException.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Fluent
    ª   ª           ª       ª   AssertableJson.php
    ª   ª           ª       ª   
    ª   ª           ª       +---Concerns
    ª   ª           ª               Debugging.php
    ª   ª           ª               Has.php
    ª   ª           ª               Interaction.php
    ª   ª           ª               Matching.php
    ª   ª           ª               
    ª   ª           +---Translation
    ª   ª           ª   ª   ArrayLoader.php
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   CreatesPotentiallyTranslatedStrings.php
    ª   ª           ª   ª   FileLoader.php
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   MessageSelector.php
    ª   ª           ª   ª   PotentiallyTranslatedString.php
    ª   ª           ª   ª   TranslationServiceProvider.php
    ª   ª           ª   ª   Translator.php
    ª   ª           ª   ª   
    ª   ª           ª   +---lang
    ª   ª           ª       +---en
    ª   ª           ª               auth.php
    ª   ª           ª               pagination.php
    ª   ª           ª               passwords.php
    ª   ª           ª               validation.php
    ª   ª           ª               
    ª   ª           +---Validation
    ª   ª           ª   ª   ClosureValidationRule.php
    ª   ª           ª   ª   composer.json
    ª   ª           ª   ª   ConditionalRules.php
    ª   ª           ª   ª   DatabasePresenceVerifier.php
    ª   ª           ª   ª   DatabasePresenceVerifierInterface.php
    ª   ª           ª   ª   Factory.php
    ª   ª           ª   ª   InvokableValidationRule.php
    ª   ª           ª   ª   LICENSE.md
    ª   ª           ª   ª   NestedRules.php
    ª   ª           ª   ª   NotPwnedVerifier.php
    ª   ª           ª   ª   PresenceVerifierInterface.php
    ª   ª           ª   ª   Rule.php
    ª   ª           ª   ª   UnauthorizedException.php
    ª   ª           ª   ª   ValidatesWhenResolvedTrait.php
    ª   ª           ª   ª   ValidationData.php
    ª   ª           ª   ª   ValidationException.php
    ª   ª           ª   ª   ValidationRuleParser.php
    ª   ª           ª   ª   ValidationServiceProvider.php
    ª   ª           ª   ª   Validator.php
    ª   ª           ª   ª   
    ª   ª           ª   +---Concerns
    ª   ª           ª   ª       FilterEmailValidation.php
    ª   ª           ª   ª       FormatsMessages.php
    ª   ª           ª   ª       ReplacesAttributes.php
    ª   ª           ª   ª       ValidatesAttributes.php
    ª   ª           ª   ª       
    ª   ª           ª   +---Rules
    ª   ª           ª           Can.php
    ª   ª           ª           DatabaseRule.php
    ª   ª           ª           Dimensions.php
    ª   ª           ª           Enum.php
    ª   ª           ª           ExcludeIf.php
    ª   ª           ª           Exists.php
    ª   ª           ª           File.php
    ª   ª           ª           ImageFile.php
    ª   ª           ª           In.php
    ª   ª           ª           NotIn.php
    ª   ª           ª           Password.php
    ª   ª           ª           ProhibitedIf.php
    ª   ª           ª           RequiredIf.php
    ª   ª           ª           Unique.php
    ª   ª           ª           
    ª   ª           +---View
    ª   ª               ª   AnonymousComponent.php
    ª   ª               ª   AppendableAttributeValue.php
    ª   ª               ª   Component.php
    ª   ª               ª   ComponentAttributeBag.php
    ª   ª               ª   ComponentSlot.php
    ª   ª               ª   composer.json
    ª   ª               ª   DynamicComponent.php
    ª   ª               ª   Factory.php
    ª   ª               ª   FileViewFinder.php
    ª   ª               ª   InvokableComponentVariable.php
    ª   ª               ª   LICENSE.md
    ª   ª               ª   View.php
    ª   ª               ª   ViewException.php
    ª   ª               ª   ViewFinderInterface.php
    ª   ª               ª   ViewName.php
    ª   ª               ª   ViewServiceProvider.php
    ª   ª               ª   
    ª   ª               +---Compilers
    ª   ª               ª   ª   BladeCompiler.php
    ª   ª               ª   ª   Compiler.php
    ª   ª               ª   ª   CompilerInterface.php
    ª   ª               ª   ª   ComponentTagCompiler.php
    ª   ª               ª   ª   
    ª   ª               ª   +---Concerns
    ª   ª               ª           CompilesAuthorizations.php
    ª   ª               ª           CompilesClasses.php
    ª   ª               ª           CompilesComments.php
    ª   ª               ª           CompilesComponents.php
    ª   ª               ª           CompilesConditionals.php
    ª   ª               ª           CompilesEchos.php
    ª   ª               ª           CompilesErrors.php
    ª   ª               ª           CompilesFragments.php
    ª   ª               ª           CompilesHelpers.php
    ª   ª               ª           CompilesIncludes.php
    ª   ª               ª           CompilesInjections.php
    ª   ª               ª           CompilesJs.php
    ª   ª               ª           CompilesJson.php
    ª   ª               ª           CompilesLayouts.php
    ª   ª               ª           CompilesLoops.php
    ª   ª               ª           CompilesRawPhp.php
    ª   ª               ª           CompilesSessions.php
    ª   ª               ª           CompilesStacks.php
    ª   ª               ª           CompilesStyles.php
    ª   ª               ª           CompilesTranslations.php
    ª   ª               ª           CompilesUseStatements.php
    ª   ª               ª           
    ª   ª               +---Concerns
    ª   ª               ª       ManagesComponents.php
    ª   ª               ª       ManagesEvents.php
    ª   ª               ª       ManagesFragments.php
    ª   ª               ª       ManagesLayouts.php
    ª   ª               ª       ManagesLoops.php
    ª   ª               ª       ManagesStacks.php
    ª   ª               ª       ManagesTranslations.php
    ª   ª               ª       
    ª   ª               +---Engines
    ª   ª               ª       CompilerEngine.php
    ª   ª               ª       Engine.php
    ª   ª               ª       EngineResolver.php
    ª   ª               ª       FileEngine.php
    ª   ª               ª       PhpEngine.php
    ª   ª               ª       
    ª   ª               +---Middleware
    ª   ª                       ShareErrorsFromSession.php
    ª   ª                       
    ª   +---pint
    ª   ª   ª   composer.json
    ª   ª   ª   LICENSE.md
    ª   ª   ª   
    ª   ª   +---builds
    ª   ª           pint
    ª   ª           
    ª   +---prompts
    ª   ª   ª   composer.json
    ª   ª   ª   LICENSE.md
    ª   ª   ª   phpunit.xml
    ª   ª   ª   README.md
    ª   ª   ª   
    ª   ª   +---src
    ª   ª       ª   ConfirmPrompt.php
    ª   ª       ª   FormBuilder.php
    ª   ª       ª   FormStep.php
    ª   ª       ª   helpers.php
    ª   ª       ª   Key.php
    ª   ª       ª   MultiSearchPrompt.php
    ª   ª       ª   MultiSelectPrompt.php
    ª   ª       ª   Note.php
    ª   ª       ª   PasswordPrompt.php
    ª   ª       ª   PausePrompt.php
    ª   ª       ª   Progress.php
    ª   ª       ª   Prompt.php
    ª   ª       ª   SearchPrompt.php
    ª   ª       ª   SelectPrompt.php
    ª   ª       ª   Spinner.php
    ª   ª       ª   SuggestPrompt.php
    ª   ª       ª   Table.php
    ª   ª       ª   Terminal.php
    ª   ª       ª   TextareaPrompt.php
    ª   ª       ª   TextPrompt.php
    ª   ª       ª   
    ª   ª       +---Concerns
    ª   ª       ª       Colors.php
    ª   ª       ª       Cursor.php
    ª   ª       ª       Erase.php
    ª   ª       ª       Events.php
    ª   ª       ª       FakesInputOutput.php
    ª   ª       ª       Fallback.php
    ª   ª       ª       Interactivity.php
    ª   ª       ª       Scrolling.php
    ª   ª       ª       Termwind.php
    ª   ª       ª       Themes.php
    ª   ª       ª       Truncation.php
    ª   ª       ª       TypedValue.php
    ª   ª       ª       
    ª   ª       +---Exceptions
    ª   ª       ª       FormRevertedException.php
    ª   ª       ª       NonInteractiveValidationException.php
    ª   ª       ª       
    ª   ª       +---Output
    ª   ª       ª       BufferedConsoleOutput.php
    ª   ª       ª       ConsoleOutput.php
    ª   ª       ª       
    ª   ª       +---Themes
    ª   ª           +---Contracts
    ª   ª           ª       Scrolling.php
    ª   ª           ª       
    ª   ª           +---Default
    ª   ª               ª   ConfirmPromptRenderer.php
    ª   ª               ª   MultiSearchPromptRenderer.php
    ª   ª               ª   MultiSelectPromptRenderer.php
    ª   ª               ª   NoteRenderer.php
    ª   ª               ª   PasswordPromptRenderer.php
    ª   ª               ª   PausePromptRenderer.php
    ª   ª               ª   ProgressRenderer.php
    ª   ª               ª   Renderer.php
    ª   ª               ª   SearchPromptRenderer.php
    ª   ª               ª   SelectPromptRenderer.php
    ª   ª               ª   SpinnerRenderer.php
    ª   ª               ª   SuggestPromptRenderer.php
    ª   ª               ª   TableRenderer.php
    ª   ª               ª   TextareaPromptRenderer.php
    ª   ª               ª   TextPromptRenderer.php
    ª   ª               ª   
    ª   ª               +---Concerns
    ª   ª                       DrawsBoxes.php
    ª   ª                       DrawsScrollbars.php
    ª   ª                       InteractsWithStrings.php
    ª   ª                       
    ª   +---sail
    ª   ª   ª   composer.json
    ª   ª   ª   LICENSE.md
    ª   ª   ª   README.md
    ª   ª   ª   
    ª   ª   +---bin
    ª   ª   ª       sail
    ª   ª   ª       
    ª   ª   +---database
    ª   ª   ª   +---mariadb
    ª   ª   ª   ª       create-testing-database.sh
    ª   ª   ª   ª       
    ª   ª   ª   +---mysql
    ª   ª   ª   ª       create-testing-database.sh
    ª   ª   ª   ª       
    ª   ª   ª   +---pgsql
    ª   ª   ª           create-testing-database.sql
    ª   ª   ª           
    ª   ª   +---runtimes
    ª   ª   ª   +---8.0
    ª   ª   ª   ª       Dockerfile
    ª   ª   ª   ª       php.ini
    ª   ª   ª   ª       start-container
    ª   ª   ª   ª       supervisord.conf
    ª   ª   ª   ª       
    ª   ª   ª   +---8.1
    ª   ª   ª   ª       Dockerfile
    ª   ª   ª   ª       php.ini
    ª   ª   ª   ª       start-container
    ª   ª   ª   ª       supervisord.conf
    ª   ª   ª   ª       
    ª   ª   ª   +---8.2
    ª   ª   ª   ª       Dockerfile
    ª   ª   ª   ª       php.ini
    ª   ª   ª   ª       start-container
    ª   ª   ª   ª       supervisord.conf
    ª   ª   ª   ª       
    ª   ª   ª   +---8.3
    ª   ª   ª   ª       Dockerfile
    ª   ª   ª   ª       php.ini
    ª   ª   ª   ª       start-container
    ª   ª   ª   ª       supervisord.conf
    ª   ª   ª   ª       
    ª   ª   ª   +---8.4
    ª   ª   ª           Dockerfile
    ª   ª   ª           php.ini
    ª   ª   ª           start-container
    ª   ª   ª           supervisord.conf
    ª   ª   ª           
    ª   ª   +---src
    ª   ª   ª   ª   SailServiceProvider.php
    ª   ª   ª   ª   
    ª   ª   ª   +---Console
    ª   ª   ª       ª   AddCommand.php
    ª   ª   ª       ª   InstallCommand.php
    ª   ª   ª       ª   PublishCommand.php
    ª   ª   ª       ª   
    ª   ª   ª       +---Concerns
    ª   ª   ª               InteractsWithDockerComposeServices.php
    ª   ª   ª               
    ª   ª   +---stubs
    ª   ª           devcontainer.stub
    ª   ª           docker-compose.stub
    ª   ª           mailpit.stub
    ª   ª           mariadb.stub
    ª   ª           meilisearch.stub
    ª   ª           memcached.stub
    ª   ª           minio.stub
    ª   ª           mongodb.stub
    ª   ª           mysql.stub
    ª   ª           pgsql.stub
    ª   ª           redis.stub
    ª   ª           selenium.stub
    ª   ª           soketi.stub
    ª   ª           typesense.stub
    ª   ª           valkey.stub
    ª   ª           
    ª   +---sanctum
    ª   ª   ª   composer.json
    ª   ª   ª   LICENSE.md
    ª   ª   ª   README.md
    ª   ª   ª   testbench.yaml
    ª   ª   ª   UPGRADE.md
    ª   ª   ª   
    ª   ª   +---config
    ª   ª   ª       sanctum.php
    ª   ª   ª       
    ª   ª   +---database
    ª   ª   ª   +---migrations
    ª   ª   ª           2019_12_14_000001_create_personal_access_tokens_table.php
    ª   ª   ª           
    ª   ª   +---src
    ª   ª       ª   Guard.php
    ª   ª       ª   HasApiTokens.php
    ª   ª       ª   NewAccessToken.php
    ª   ª       ª   PersonalAccessToken.php
    ª   ª       ª   Sanctum.php
    ª   ª       ª   SanctumServiceProvider.php
    ª   ª       ª   TransientToken.php
    ª   ª       ª   
    ª   ª       +---Console
    ª   ª       ª   +---Commands
    ª   ª       ª           PruneExpired.php
    ª   ª       ª           
    ª   ª       +---Contracts
    ª   ª       ª       HasAbilities.php
    ª   ª       ª       HasApiTokens.php
    ª   ª       ª       
    ª   ª       +---Events
    ª   ª       ª       TokenAuthenticated.php
    ª   ª       ª       
    ª   ª       +---Exceptions
    ª   ª       ª       MissingAbilityException.php
    ª   ª       ª       MissingScopeException.php
    ª   ª       ª       
    ª   ª       +---Http
    ª   ª           +---Controllers
    ª   ª           ª       CsrfCookieController.php
    ª   ª           ª       
    ª   ª           +---Middleware
    ª   ª                   AuthenticateSession.php
    ª   ª                   CheckAbilities.php
    ª   ª                   CheckForAnyAbility.php
    ª   ª                   CheckForAnyScope.php
    ª   ª                   CheckScopes.php
    ª   ª                   EnsureFrontendRequestsAreStateful.php
    ª   ª                   
    ª   +---serializable-closure
    ª   ª   ª   composer.json
    ª   ª   ª   LICENSE.md
    ª   ª   ª   README.md
    ª   ª   ª   
    ª   ª   +---src
    ª   ª       ª   SerializableClosure.php
    ª   ª       ª   UnsignedSerializableClosure.php
    ª   ª       ª   
    ª   ª       +---Contracts
    ª   ª       ª       Serializable.php
    ª   ª       ª       Signer.php
    ª   ª       ª       
    ª   ª       +---Exceptions
    ª   ª       ª       InvalidSignatureException.php
    ª   ª       ª       MissingSecretKeyException.php
    ª   ª       ª       PhpVersionNotSupportedException.php
    ª   ª       ª       
    ª   ª       +---Serializers
    ª   ª       ª       Native.php
    ª   ª       ª       Signed.php
    ª   ª       ª       
    ª   ª       +---Signers
    ª   ª       ª       Hmac.php
    ª   ª       ª       
    ª   ª       +---Support
    ª   ª               ClosureScope.php
    ª   ª               ClosureStream.php
    ª   ª               ReflectionClosure.php
    ª   ª               SelfReference.php
    ª   ª               
    ª   +---tinker
    ª       ª   composer.json
    ª       ª   LICENSE.md
    ª       ª   README.md
    ª       ª   
    ª       +---config
    ª       ª       tinker.php
    ª       ª       
    ª       +---src
    ª           ª   ClassAliasAutoloader.php
    ª           ª   TinkerCaster.php
    ª           ª   TinkerServiceProvider.php
    ª           ª   
    ª           +---Console
    ª                   TinkerCommand.php
    ª                   
    +---league
    ª   +---commonmark
    ª   ª   ª   .phpstorm.meta.php
    ª   ª   ª   CHANGELOG.md
    ª   ª   ª   composer.json
    ª   ª   ª   LICENSE
    ª   ª   ª   README.md
    ª   ª   ª   
    ª   ª   +---src
    ª   ª       ª   CommonMarkConverter.php
    ª   ª       ª   ConverterInterface.php
    ª   ª       ª   GithubFlavoredMarkdownConverter.php
    ª   ª       ª   MarkdownConverter.php
    ª   ª       ª   MarkdownConverterInterface.php
    ª   ª       ª   
    ª   ª       +---Delimiter
    ª   ª       ª   ª   Bracket.php
    ª   ª       ª   ª   Delimiter.php
    ª   ª       ª   ª   DelimiterInterface.php
    ª   ª       ª   ª   DelimiterParser.php
    ª   ª       ª   ª   DelimiterStack.php
    ª   ª       ª   ª   
    ª   ª       ª   +---Processor
    ª   ª       ª           CacheableDelimiterProcessorInterface.php
    ª   ª       ª           DelimiterProcessorCollection.php
    ª   ª       ª           DelimiterProcessorCollectionInterface.php
    ª   ª       ª           DelimiterProcessorInterface.php
    ª   ª       ª           StaggeredDelimiterProcessor.php
    ª   ª       ª           
    ª   ª       +---Environment
    ª   ª       ª       Environment.php
    ª   ª       ª       EnvironmentAwareInterface.php
    ª   ª       ª       EnvironmentBuilderInterface.php
    ª   ª       ª       EnvironmentInterface.php
    ª   ª       ª       
    ª   ª       +---Event
    ª   ª       ª       AbstractEvent.php
    ª   ª       ª       DocumentParsedEvent.php
    ª   ª       ª       DocumentPreParsedEvent.php
    ª   ª       ª       DocumentPreRenderEvent.php
    ª   ª       ª       DocumentRenderedEvent.php
    ª   ª       ª       ListenerData.php
    ª   ª       ª       
    ª   ª       +---Exception
    ª   ª       ª       AlreadyInitializedException.php
    ª   ª       ª       CommonMarkException.php
    ª   ª       ª       InvalidArgumentException.php
    ª   ª       ª       IOException.php
    ª   ª       ª       LogicException.php
    ª   ª       ª       MissingDependencyException.php
    ª   ª       ª       UnexpectedEncodingException.php
    ª   ª       ª       
    ª   ª       +---Extension
    ª   ª       ª   ª   ConfigurableExtensionInterface.php
    ª   ª       ª   ª   ExtensionInterface.php
    ª   ª       ª   ª   GithubFlavoredMarkdownExtension.php
    ª   ª       ª   ª   
    ª   ª       ª   +---Attributes
    ª   ª       ª   ª   ª   AttributesExtension.php
    ª   ª       ª   ª   ª   
    ª   ª       ª   ª   +---Event
    ª   ª       ª   ª   ª       AttributesListener.php
    ª   ª       ª   ª   ª       
    ª   ª       ª   ª   +---Node
    ª   ª       ª   ª   ª       Attributes.php
    ª   ª       ª   ª   ª       AttributesInline.php
    ª   ª       ª   ª   ª       
    ª   ª       ª   ª   +---Parser
    ª   ª       ª   ª   ª       AttributesBlockContinueParser.php
    ª   ª       ª   ª   ª       AttributesBlockStartParser.php
    ª   ª       ª   ª   ª       AttributesInlineParser.php
    ª   ª       ª   ª   ª       
    ª   ª       ª   ª   +---Util
    ª   ª       ª   ª           AttributesHelper.php
    ª   ª       ª   ª           
    ª   ª       ª   +---Autolink
    ª   ª       ª   ª       AutolinkExtension.php
    ª   ª       ª   ª       EmailAutolinkParser.php
    ª   ª       ª   ª       UrlAutolinkParser.php
    ª   ª       ª   ª       
    ª   ª       ª   +---CommonMark
    ª   ª       ª   ª   ª   CommonMarkCoreExtension.php
    ª   ª       ª   ª   ª   
    ª   ª       ª   ª   +---Delimiter
    ª   ª       ª   ª   ª   +---Processor
    ª   ª       ª   ª   ª           EmphasisDelimiterProcessor.php
    ª   ª       ª   ª   ª           
    ª   ª       ª   ª   +---Node
    ª   ª       ª   ª   ª   +---Block
    ª   ª       ª   ª   ª   ª       BlockQuote.php
    ª   ª       ª   ª   ª   ª       FencedCode.php
    ª   ª       ª   ª   ª   ª       Heading.php
    ª   ª       ª   ª   ª   ª       HtmlBlock.php
    ª   ª       ª   ª   ª   ª       IndentedCode.php
    ª   ª       ª   ª   ª   ª       ListBlock.php
    ª   ª       ª   ª   ª   ª       ListData.php
    ª   ª       ª   ª   ª   ª       ListItem.php
    ª   ª       ª   ª   ª   ª       ThematicBreak.php
    ª   ª       ª   ª   ª   ª       
    ª   ª       ª   ª   ª   +---Inline
    ª   ª       ª   ª   ª           AbstractWebResource.php
    ª   ª       ª   ª   ª           Code.php
    ª   ª       ª   ª   ª           Emphasis.php
    ª   ª       ª   ª   ª           HtmlInline.php
    ª   ª       ª   ª   ª           Image.php
    ª   ª       ª   ª   ª           Link.php
    ª   ª       ª   ª   ª           Strong.php
    ª   ª       ª   ª   ª           
    ª   ª       ª   ª   +---Parser
    ª   ª       ª   ª   ª   +---Block
    ª   ª       ª   ª   ª   ª       BlockQuoteParser.php
    ª   ª       ª   ª   ª   ª       BlockQuoteStartParser.php
    ª   ª       ª   ª   ª   ª       FencedCodeParser.php
    ª   ª       ª   ª   ª   ª       FencedCodeStartParser.php
    ª   ª       ª   ª   ª   ª       HeadingParser.php
    ª   ª       ª   ª   ª   ª       HeadingStartParser.php
    ª   ª       ª   ª   ª   ª       HtmlBlockParser.php
    ª   ª       ª   ª   ª   ª       HtmlBlockStartParser.php
    ª   ª       ª   ª   ª   ª       IndentedCodeParser.php
    ª   ª       ª   ª   ª   ª       IndentedCodeStartParser.php
    ª   ª       ª   ª   ª   ª       ListBlockParser.php
    ª   ª       ª   ª   ª   ª       ListBlockStartParser.php
    ª   ª       ª   ª   ª   ª       ListItemParser.php
    ª   ª       ª   ª   ª   ª       ThematicBreakParser.php
    ª   ª       ª   ª   ª   ª       ThematicBreakStartParser.php
    ª   ª       ª   ª   ª   ª       
    ª   ª       ª   ª   ª   +---Inline
    ª   ª       ª   ª   ª           AutolinkParser.php
    ª   ª       ª   ª   ª           BacktickParser.php
    ª   ª       ª   ª   ª           BangParser.php
    ª   ª       ª   ª   ª           CloseBracketParser.php
    ª   ª       ª   ª   ª           EntityParser.php
    ª   ª       ª   ª   ª           EscapableParser.php
    ª   ª       ª   ª   ª           HtmlInlineParser.php
    ª   ª       ª   ª   ª           OpenBracketParser.php
    ª   ª       ª   ª   ª           
    ª   ª       ª   ª   +---Renderer
    ª   ª       ª   ª       +---Block
    ª   ª       ª   ª       ª       BlockQuoteRenderer.php
    ª   ª       ª   ª       ª       FencedCodeRenderer.php
    ª   ª       ª   ª       ª       HeadingRenderer.php
    ª   ª       ª   ª       ª       HtmlBlockRenderer.php
    ª   ª       ª   ª       ª       IndentedCodeRenderer.php
    ª   ª       ª   ª       ª       ListBlockRenderer.php
    ª   ª       ª   ª       ª       ListItemRenderer.php
    ª   ª       ª   ª       ª       ThematicBreakRenderer.php
    ª   ª       ª   ª       ª       
    ª   ª       ª   ª       +---Inline
    ª   ª       ª   ª               CodeRenderer.php
    ª   ª       ª   ª               EmphasisRenderer.php
    ª   ª       ª   ª               HtmlInlineRenderer.php
    ª   ª       ª   ª               ImageRenderer.php
    ª   ª       ª   ª               LinkRenderer.php
    ª   ª       ª   ª               StrongRenderer.php
    ª   ª       ª   ª               
    ª   ª       ª   +---DefaultAttributes
    ª   ª       ª   ª       ApplyDefaultAttributesProcessor.php
    ª   ª       ª   ª       DefaultAttributesExtension.php
    ª   ª       ª   ª       
    ª   ª       ª   +---DescriptionList
    ª   ª       ª   ª   ª   DescriptionListExtension.php
    ª   ª       ª   ª   ª   
    ª   ª       ª   ª   +---Event
    ª   ª       ª   ª   ª       ConsecutiveDescriptionListMerger.php
    ª   ª       ª   ª   ª       LooseDescriptionHandler.php
    ª   ª       ª   ª   ª       
    ª   ª       ª   ª   +---Node
    ª   ª       ª   ª   ª       Description.php
    ª   ª       ª   ª   ª       DescriptionList.php
    ª   ª       ª   ª   ª       DescriptionTerm.php
    ª   ª       ª   ª   ª       
    ª   ª       ª   ª   +---Parser
    ª   ª       ª   ª   ª       DescriptionContinueParser.php
    ª   ª       ª   ª   ª       DescriptionListContinueParser.php
    ª   ª       ª   ª   ª       DescriptionStartParser.php
    ª   ª       ª   ª   ª       DescriptionTermContinueParser.php
    ª   ª       ª   ª   ª       
    ª   ª       ª   ª   +---Renderer
    ª   ª       ª   ª           DescriptionListRenderer.php
    ª   ª       ª   ª           DescriptionRenderer.php
    ª   ª       ª   ª           DescriptionTermRenderer.php
    ª   ª       ª   ª           
    ª   ª       ª   +---DisallowedRawHtml
    ª   ª       ª   ª       DisallowedRawHtmlExtension.php
    ª   ª       ª   ª       DisallowedRawHtmlRenderer.php
    ª   ª       ª   ª       
    ª   ª       ª   +---Embed
    ª   ª       ª   ª   ª   DomainFilteringAdapter.php
    ª   ª       ª   ª   ª   Embed.php
    ª   ª       ª   ª   ª   EmbedAdapterInterface.php
    ª   ª       ª   ª   ª   EmbedExtension.php
    ª   ª       ª   ª   ª   EmbedParser.php
    ª   ª       ª   ª   ª   EmbedProcessor.php
    ª   ª       ª   ª   ª   EmbedRenderer.php
    ª   ª       ª   ª   ª   EmbedStartParser.php
    ª   ª       ª   ª   ª   
    ª   ª       ª   ª   +---Bridge
    ª   ª       ª   ª           OscaroteroEmbedAdapter.php
    ª   ª       ª   ª           
    ª   ª       ª   +---ExternalLink
    ª   ª       ª   ª       ExternalLinkExtension.php
    ª   ª       ª   ª       ExternalLinkProcessor.php
    ª   ª       ª   ª       
    ª   ª       ª   +---Footnote
    ª   ª       ª   ª   ª   FootnoteExtension.php
    ª   ª       ª   ª   ª   
    ª   ª       ª   ª   +---Event
    ª   ª       ª   ª   ª       AnonymousFootnotesListener.php
    ª   ª       ª   ª   ª       FixOrphanedFootnotesAndRefsListener.php
    ª   ª       ª   ª   ª       GatherFootnotesListener.php
    ª   ª       ª   ª   ª       NumberFootnotesListener.php
    ª   ª       ª   ª   ª       
    ª   ª       ª   ª   +---Node
    ª   ª       ª   ª   ª       Footnote.php
    ª   ª       ª   ª   ª       FootnoteBackref.php
    ª   ª       ª   ª   ª       FootnoteContainer.php
    ª   ª       ª   ª   ª       FootnoteRef.php
    ª   ª       ª   ª   ª       
    ª   ª       ª   ª   +---Parser
    ª   ª       ª   ª   ª       AnonymousFootnoteRefParser.php
    ª   ª       ª   ª   ª       FootnoteParser.php
    ª   ª       ª   ª   ª       FootnoteRefParser.php
    ª   ª       ª   ª   ª       FootnoteStartParser.php
    ª   ª       ª   ª   ª       
    ª   ª       ª   ª   +---Renderer
    ª   ª       ª   ª           FootnoteBackrefRenderer.php
    ª   ª       ª   ª           FootnoteContainerRenderer.php
    ª   ª       ª   ª           FootnoteRefRenderer.php
    ª   ª       ª   ª           FootnoteRenderer.php
    ª   ª       ª   ª           
    ª   ª       ª   +---FrontMatter
    ª   ª       ª   ª   ª   FrontMatterExtension.php
    ª   ª       ª   ª   ª   FrontMatterParser.php
    ª   ª       ª   ª   ª   FrontMatterParserInterface.php
    ª   ª       ª   ª   ª   FrontMatterProviderInterface.php
    ª   ª       ª   ª   ª   
    ª   ª       ª   ª   +---Data
    ª   ª       ª   ª   ª       FrontMatterDataParserInterface.php
    ª   ª       ª   ª   ª       LibYamlFrontMatterParser.php
    ª   ª       ª   ª   ª       SymfonyYamlFrontMatterParser.php
    ª   ª       ª   ª   ª       
    ª   ª       ª   ª   +---Exception
    ª   ª       ª   ª   ª       InvalidFrontMatterException.php
    ª   ª       ª   ª   ª       
    ª   ª       ª   ª   +---Input
    ª   ª       ª   ª   ª       MarkdownInputWithFrontMatter.php
    ª   ª       ª   ª   ª       
    ª   ª       ª   ª   +---Listener
    ª   ª       ª   ª   ª       FrontMatterPostRenderListener.php
    ª   ª       ª   ª   ª       FrontMatterPreParser.php
    ª   ª       ª   ª   ª       
    ª   ª       ª   ª   +---Output
    ª   ª       ª   ª           RenderedContentWithFrontMatter.php
    ª   ª       ª   ª           
    ª   ª       ª   +---HeadingPermalink
    ª   ª       ª   ª       HeadingPermalink.php
    ª   ª       ª   ª       HeadingPermalinkExtension.php
    ª   ª       ª   ª       HeadingPermalinkProcessor.php
    ª   ª       ª   ª       HeadingPermalinkRenderer.php
    ª   ª       ª   ª       
    ª   ª       ª   +---InlinesOnly
    ª   ª       ª   ª       ChildRenderer.php
    ª   ª       ª   ª       InlinesOnlyExtension.php
    ª   ª       ª   ª       
    ª   ª       ª   +---Mention
    ª   ª       ª   ª   ª   Mention.php
    ª   ª       ª   ª   ª   MentionExtension.php
    ª   ª       ª   ª   ª   MentionParser.php
    ª   ª       ª   ª   ª   
    ª   ª       ª   ª   +---Generator
    ª   ª       ª   ª           CallbackGenerator.php
    ª   ª       ª   ª           MentionGeneratorInterface.php
    ª   ª       ª   ª           StringTemplateLinkGenerator.php
    ª   ª       ª   ª           
    ª   ª       ª   +---SmartPunct
    ª   ª       ª   ª       DashParser.php
    ª   ª       ª   ª       EllipsesParser.php
    ª   ª       ª   ª       Quote.php
    ª   ª       ª   ª       QuoteParser.php
    ª   ª       ª   ª       QuoteProcessor.php
    ª   ª       ª   ª       ReplaceUnpairedQuotesListener.php
    ª   ª       ª   ª       SmartPunctExtension.php
    ª   ª       ª   ª       
    ª   ª       ª   +---Strikethrough
    ª   ª       ª   ª       Strikethrough.php
    ª   ª       ª   ª       StrikethroughDelimiterProcessor.php
    ª   ª       ª   ª       StrikethroughExtension.php
    ª   ª       ª   ª       StrikethroughRenderer.php
    ª   ª       ª   ª       
    ª   ª       ª   +---Table
    ª   ª       ª   ª       Table.php
    ª   ª       ª   ª       TableCell.php
    ª   ª       ª   ª       TableCellRenderer.php
    ª   ª       ª   ª       TableExtension.php
    ª   ª       ª   ª       TableParser.php
    ª   ª       ª   ª       TableRenderer.php
    ª   ª       ª   ª       TableRow.php
    ª   ª       ª   ª       TableRowRenderer.php
    ª   ª       ª   ª       TableSection.php
    ª   ª       ª   ª       TableSectionRenderer.php
    ª   ª       ª   ª       TableStartParser.php
    ª   ª       ª   ª       
    ª   ª       ª   +---TableOfContents
    ª   ª       ª   ª   ª   TableOfContentsBuilder.php
    ª   ª       ª   ª   ª   TableOfContentsExtension.php
    ª   ª       ª   ª   ª   TableOfContentsGenerator.php
    ª   ª       ª   ª   ª   TableOfContentsGeneratorInterface.php
    ª   ª       ª   ª   ª   TableOfContentsPlaceholderParser.php
    ª   ª       ª   ª   ª   TableOfContentsPlaceholderRenderer.php
    ª   ª       ª   ª   ª   TableOfContentsRenderer.php
    ª   ª       ª   ª   ª   
    ª   ª       ª   ª   +---Node
    ª   ª       ª   ª   ª       TableOfContents.php
    ª   ª       ª   ª   ª       TableOfContentsPlaceholder.php
    ª   ª       ª   ª   ª       
    ª   ª       ª   ª   +---Normalizer
    ª   ª       ª   ª           AsIsNormalizerStrategy.php
    ª   ª       ª   ª           FlatNormalizerStrategy.php
    ª   ª       ª   ª           NormalizerStrategyInterface.php
    ª   ª       ª   ª           RelativeNormalizerStrategy.php
    ª   ª       ª   ª           
    ª   ª       ª   +---TaskList
    ª   ª       ª           TaskListExtension.php
    ª   ª       ª           TaskListItemMarker.php
    ª   ª       ª           TaskListItemMarkerParser.php
    ª   ª       ª           TaskListItemMarkerRenderer.php
    ª   ª       ª           
    ª   ª       +---Input
    ª   ª       ª       MarkdownInput.php
    ª   ª       ª       MarkdownInputInterface.php
    ª   ª       ª       
    ª   ª       +---Node
    ª   ª       ª   ª   Node.php
    ª   ª       ª   ª   NodeIterator.php
    ª   ª       ª   ª   NodeWalker.php
    ª   ª       ª   ª   NodeWalkerEvent.php
    ª   ª       ª   ª   Query.php
    ª   ª       ª   ª   RawMarkupContainerInterface.php
    ª   ª       ª   ª   StringContainerHelper.php
    ª   ª       ª   ª   StringContainerInterface.php
    ª   ª       ª   ª   
    ª   ª       ª   +---Block
    ª   ª       ª   ª       AbstractBlock.php
    ª   ª       ª   ª       Document.php
    ª   ª       ª   ª       Paragraph.php
    ª   ª       ª   ª       TightBlockInterface.php
    ª   ª       ª   ª       
    ª   ª       ª   +---Inline
    ª   ª       ª   ª       AbstractInline.php
    ª   ª       ª   ª       AbstractStringContainer.php
    ª   ª       ª   ª       AdjacentTextMerger.php
    ª   ª       ª   ª       DelimitedInterface.php
    ª   ª       ª   ª       Newline.php
    ª   ª       ª   ª       Text.php
    ª   ª       ª   ª       
    ª   ª       ª   +---Query
    ª   ª       ª           AndExpr.php
    ª   ª       ª           ExpressionInterface.php
    ª   ª       ª           OrExpr.php
    ª   ª       ª           
    ª   ª       +---Normalizer
    ª   ª       ª       SlugNormalizer.php
    ª   ª       ª       TextNormalizer.php
    ª   ª       ª       TextNormalizerInterface.php
    ª   ª       ª       UniqueSlugNormalizer.php
    ª   ª       ª       UniqueSlugNormalizerInterface.php
    ª   ª       ª       
    ª   ª       +---Output
    ª   ª       ª       RenderedContent.php
    ª   ª       ª       RenderedContentInterface.php
    ª   ª       ª       
    ª   ª       +---Parser
    ª   ª       ª   ª   Cursor.php
    ª   ª       ª   ª   CursorState.php
    ª   ª       ª   ª   InlineParserContext.php
    ª   ª       ª   ª   InlineParserEngine.php
    ª   ª       ª   ª   InlineParserEngineInterface.php
    ª   ª       ª   ª   MarkdownParser.php
    ª   ª       ª   ª   MarkdownParserInterface.php
    ª   ª       ª   ª   MarkdownParserState.php
    ª   ª       ª   ª   MarkdownParserStateInterface.php
    ª   ª       ª   ª   ParserLogicException.php
    ª   ª       ª   ª   
    ª   ª       ª   +---Block
    ª   ª       ª   ª       AbstractBlockContinueParser.php
    ª   ª       ª   ª       BlockContinue.php
    ª   ª       ª   ª       BlockContinueParserInterface.php
    ª   ª       ª   ª       BlockContinueParserWithInlinesInterface.php
    ª   ª       ª   ª       BlockStart.php
    ª   ª       ª   ª       BlockStartParserInterface.php
    ª   ª       ª   ª       DocumentBlockParser.php
    ª   ª       ª   ª       ParagraphParser.php
    ª   ª       ª   ª       SkipLinesStartingWithLettersParser.php
    ª   ª       ª   ª       
    ª   ª       ª   +---Inline
    ª   ª       ª           InlineParserInterface.php
    ª   ª       ª           InlineParserMatch.php
    ª   ª       ª           NewlineParser.php
    ª   ª       ª           
    ª   ª       +---Reference
    ª   ª       ª       MemoryLimitedReferenceMap.php
    ª   ª       ª       Reference.php
    ª   ª       ª       ReferenceableInterface.php
    ª   ª       ª       ReferenceInterface.php
    ª   ª       ª       ReferenceMap.php
    ª   ª       ª       ReferenceMapInterface.php
    ª   ª       ª       ReferenceParser.php
    ª   ª       ª       
    ª   ª       +---Renderer
    ª   ª       ª   ª   ChildNodeRendererInterface.php
    ª   ª       ª   ª   DocumentRendererInterface.php
    ª   ª       ª   ª   HtmlDecorator.php
    ª   ª       ª   ª   HtmlRenderer.php
    ª   ª       ª   ª   MarkdownRendererInterface.php
    ª   ª       ª   ª   NodeRendererInterface.php
    ª   ª       ª   ª   NoMatchingRendererException.php
    ª   ª       ª   ª   
    ª   ª       ª   +---Block
    ª   ª       ª   ª       DocumentRenderer.php
    ª   ª       ª   ª       ParagraphRenderer.php
    ª   ª       ª   ª       
    ª   ª       ª   +---Inline
    ª   ª       ª           NewlineRenderer.php
    ª   ª       ª           TextRenderer.php
    ª   ª       ª           
    ª   ª       +---Util
    ª   ª       ª       ArrayCollection.php
    ª   ª       ª       Html5EntityDecoder.php
    ª   ª       ª       HtmlElement.php
    ª   ª       ª       HtmlFilter.php
    ª   ª       ª       LinkParserHelper.php
    ª   ª       ª       PrioritizedList.php
    ª   ª       ª       RegexHelper.php
    ª   ª       ª       SpecReader.php
    ª   ª       ª       UrlEncoder.php
    ª   ª       ª       Xml.php
    ª   ª       ª       
    ª   ª       +---Xml
    ª   ª               FallbackNodeXmlRenderer.php
    ª   ª               MarkdownToXmlConverter.php
    ª   ª               XmlNodeRendererInterface.php
    ª   ª               XmlRenderer.php
    ª   ª               
    ª   +---config
    ª   ª   ª   CHANGELOG.md
    ª   ª   ª   composer.json
    ª   ª   ª   LICENSE.md
    ª   ª   ª   README.md
    ª   ª   ª   
    ª   ª   +---src
    ª   ª       ª   Configuration.php
    ª   ª       ª   ConfigurationAwareInterface.php
    ª   ª       ª   ConfigurationBuilderInterface.php
    ª   ª       ª   ConfigurationInterface.php
    ª   ª       ª   ConfigurationProviderInterface.php
    ª   ª       ª   MutableConfigurationInterface.php
    ª   ª       ª   ReadOnlyConfiguration.php
    ª   ª       ª   SchemaBuilderInterface.php
    ª   ª       ª   
    ª   ª       +---Exception
    ª   ª               ConfigurationExceptionInterface.php
    ª   ª               InvalidConfigurationException.php
    ª   ª               UnknownOptionException.php
    ª   ª               ValidationException.php
    ª   ª               
    ª   +---flysystem
    ª   ª   ª   composer.json
    ª   ª   ª   INFO.md
    ª   ª   ª   LICENSE
    ª   ª   ª   readme.md
    ª   ª   ª   
    ª   ª   +---src
    ª   ª       ª   CalculateChecksumFromStream.php
    ª   ª       ª   ChecksumAlgoIsNotSupported.php
    ª   ª       ª   ChecksumProvider.php
    ª   ª       ª   Config.php
    ª   ª       ª   CorruptedPathDetected.php
    ª   ª       ª   DecoratedAdapter.php
    ª   ª       ª   DirectoryAttributes.php
    ª   ª       ª   DirectoryListing.php
    ª   ª       ª   FileAttributes.php
    ª   ª       ª   Filesystem.php
    ª   ª       ª   FilesystemAdapter.php
    ª   ª       ª   FilesystemException.php
    ª   ª       ª   FilesystemOperationFailed.php
    ª   ª       ª   FilesystemOperator.php
    ª   ª       ª   FilesystemReader.php
    ª   ª       ª   FilesystemWriter.php
    ª   ª       ª   InvalidStreamProvided.php
    ª   ª       ª   InvalidVisibilityProvided.php
    ª   ª       ª   MountManager.php
    ª   ª       ª   PathNormalizer.php
    ª   ª       ª   PathPrefixer.php
    ª   ª       ª   PathTraversalDetected.php
    ª   ª       ª   PortableVisibilityGuard.php
    ª   ª       ª   ProxyArrayAccessToProperties.php
    ª   ª       ª   ResolveIdenticalPathConflict.php
    ª   ª       ª   StorageAttributes.php
    ª   ª       ª   SymbolicLinkEncountered.php
    ª   ª       ª   UnableToCheckDirectoryExistence.php
    ª   ª       ª   UnableToCheckExistence.php
    ª   ª       ª   UnableToCheckFileExistence.php
    ª   ª       ª   UnableToCopyFile.php
    ª   ª       ª   UnableToCreateDirectory.php
    ª   ª       ª   UnableToDeleteDirectory.php
    ª   ª       ª   UnableToDeleteFile.php
    ª   ª       ª   UnableToGeneratePublicUrl.php
    ª   ª       ª   UnableToGenerateTemporaryUrl.php
    ª   ª       ª   UnableToListContents.php
    ª   ª       ª   UnableToMountFilesystem.php
    ª   ª       ª   UnableToMoveFile.php
    ª   ª       ª   UnableToProvideChecksum.php
    ª   ª       ª   UnableToReadFile.php
    ª   ª       ª   UnableToResolveFilesystemMount.php
    ª   ª       ª   UnableToRetrieveMetadata.php
    ª   ª       ª   UnableToSetVisibility.php
    ª   ª       ª   UnableToWriteFile.php
    ª   ª       ª   UnreadableFileEncountered.php
    ª   ª       ª   Visibility.php
    ª   ª       ª   WhitespacePathNormalizer.php
    ª   ª       ª   
    ª   ª       +---UnixVisibility
    ª   ª       ª       PortableVisibilityConverter.php
    ª   ª       ª       VisibilityConverter.php
    ª   ª       ª       
    ª   ª       +---UrlGeneration
    ª   ª               ChainedPublicUrlGenerator.php
    ª   ª               PrefixPublicUrlGenerator.php
    ª   ª               PublicUrlGenerator.php
    ª   ª               ShardedPrefixPublicUrlGenerator.php
    ª   ª               TemporaryUrlGenerator.php
    ª   ª               
    ª   +---flysystem-local
    ª   ª       composer.json
    ª   ª       FallbackMimeTypeDetector.php
    ª   ª       LICENSE
    ª   ª       LocalFilesystemAdapter.php
    ª   ª       
    ª   +---mime-type-detection
    ª       ª   CHANGELOG.md
    ª       ª   composer.json
    ª       ª   LICENSE
    ª       ª   
    ª       +---src
    ª               EmptyExtensionToMimeTypeMap.php
    ª               ExtensionLookup.php
    ª               ExtensionMimeTypeDetector.php
    ª               ExtensionToMimeTypeMap.php
    ª               FinfoMimeTypeDetector.php
    ª               GeneratedExtensionToMimeTypeMap.php
    ª               MimeTypeDetector.php
    ª               OverridingExtensionToMimeTypeMap.php
    ª               
    +---mockery
    ª   +---mockery
    ª       ª   .phpstorm.meta.php
    ª       ª   .readthedocs.yml
    ª       ª   CHANGELOG.md
    ª       ª   composer.json
    ª       ª   composer.lock
    ª       ª   CONTRIBUTING.md
    ª       ª   COPYRIGHT.md
    ª       ª   LICENSE
    ª       ª   README.md
    ª       ª   SECURITY.md
    ª       ª   
    ª       +---docs
    ª       ª   ª   .gitignore
    ª       ª   ª   conf.py
    ª       ª   ª   index.rst
    ª       ª   ª   Makefile
    ª       ª   ª   README.md
    ª       ª   ª   requirements.txt
    ª       ª   ª   
    ª       ª   +---cookbook
    ª       ª   ª       big_parent_class.rst
    ª       ª   ª       class_constants.rst
    ª       ª   ª       default_expectations.rst
    ª       ª   ª       detecting_mock_objects.rst
    ª       ª   ª       index.rst
    ª       ª   ª       map.rst.inc
    ª       ª   ª       mockery_on.rst
    ª       ª   ª       mocking_class_within_class.rst
    ª       ª   ª       mocking_hard_dependencies.rst
    ª       ª   ª       not_calling_the_constructor.rst
    ª       ª   ª       
    ª       ª   +---getting_started
    ª       ª   ª       index.rst
    ª       ª   ª       installation.rst
    ª       ª   ª       map.rst.inc
    ª       ª   ª       quick_reference.rst
    ª       ª   ª       simple_example.rst
    ª       ª   ª       upgrading.rst
    ª       ª   ª       
    ª       ª   +---mockery
    ª       ª   ª       configuration.rst
    ª       ª   ª       exceptions.rst
    ª       ª   ª       gotchas.rst
    ª       ª   ª       index.rst
    ª       ª   ª       map.rst.inc
    ª       ª   ª       reserved_method_names.rst
    ª       ª   ª       
    ª       ª   +---reference
    ª       ª   ª       alternative_should_receive_syntax.rst
    ª       ª   ª       argument_validation.rst
    ª       ª   ª       creating_test_doubles.rst
    ª       ª   ª       demeter_chains.rst
    ª       ª   ª       expectations.rst
    ª       ª   ª       final_methods_classes.rst
    ª       ª   ª       index.rst
    ª       ª   ª       instance_mocking.rst
    ª       ª   ª       magic_methods.rst
    ª       ª   ª       map.rst.inc
    ª       ª   ª       partial_mocks.rst
    ª       ª   ª       pass_by_reference_behaviours.rst
    ª       ª   ª       phpunit_integration.rst
    ª       ª   ª       protected_methods.rst
    ª       ª   ª       public_properties.rst
    ª       ª   ª       public_static_properties.rst
    ª       ª   ª       spies.rst
    ª       ª   ª       
    ª       ª   +---_static
    ª       ª           .gitkeep
    ª       ª           
    ª       +---library
    ª           ª   helpers.php
    ª           ª   Mockery.php
    ª           ª   
    ª           +---Mockery
    ª               ª   ClosureWrapper.php
    ª               ª   CompositeExpectation.php
    ª               ª   Configuration.php
    ª               ª   Container.php
    ª               ª   Exception.php
    ª               ª   Expectation.php
    ª               ª   ExpectationDirector.php
    ª               ª   ExpectationInterface.php
    ª               ª   ExpectsHigherOrderMessage.php
    ª               ª   HigherOrderMessage.php
    ª               ª   Instantiator.php
    ª               ª   LegacyMockInterface.php
    ª               ª   MethodCall.php
    ª               ª   Mock.php
    ª               ª   MockInterface.php
    ª               ª   QuickDefinitionsConfiguration.php
    ª               ª   ReceivedMethodCalls.php
    ª               ª   Reflector.php
    ª               ª   Undefined.php
    ª               ª   VerificationDirector.php
    ª               ª   VerificationExpectation.php
    ª               ª   
    ª               +---Adapter
    ª               ª   +---Phpunit
    ª               ª           MockeryPHPUnitIntegration.php
    ª               ª           MockeryPHPUnitIntegrationAssertPostConditions.php
    ª               ª           MockeryTestCase.php
    ª               ª           MockeryTestCaseSetUp.php
    ª               ª           TestListener.php
    ª               ª           TestListenerTrait.php
    ª               ª           
    ª               +---CountValidator
    ª               ª       AtLeast.php
    ª               ª       AtMost.php
    ª               ª       CountValidatorAbstract.php
    ª               ª       CountValidatorInterface.php
    ª               ª       Exact.php
    ª               ª       Exception.php
    ª               ª       
    ª               +---Exception
    ª               ª       BadMethodCallException.php
    ª               ª       InvalidArgumentException.php
    ª               ª       InvalidCountException.php
    ª               ª       InvalidOrderException.php
    ª               ª       MockeryExceptionInterface.php
    ª               ª       NoMatchingExpectationException.php
    ª               ª       RuntimeException.php
    ª               ª       
    ª               +---Generator
    ª               ª   ª   CachingGenerator.php
    ª               ª   ª   DefinedTargetClass.php
    ª               ª   ª   Generator.php
    ª               ª   ª   Method.php
    ª               ª   ª   MockConfiguration.php
    ª               ª   ª   MockConfigurationBuilder.php
    ª               ª   ª   MockDefinition.php
    ª               ª   ª   MockNameBuilder.php
    ª               ª   ª   Parameter.php
    ª               ª   ª   StringManipulationGenerator.php
    ª               ª   ª   TargetClassInterface.php
    ª               ª   ª   UndefinedTargetClass.php
    ª               ª   ª   
    ª               ª   +---StringManipulation
    ª               ª       +---Pass
    ª               ª               AvoidMethodClashPass.php
    ª               ª               CallTypeHintPass.php
    ª               ª               ClassAttributesPass.php
    ª               ª               ClassNamePass.php
    ª               ª               ClassPass.php
    ª               ª               ConstantsPass.php
    ª               ª               InstanceMockPass.php
    ª               ª               InterfacePass.php
    ª               ª               MagicMethodTypeHintsPass.php
    ª               ª               MethodDefinitionPass.php
    ª               ª               Pass.php
    ª               ª               RemoveBuiltinMethodsThatAreFinalPass.php
    ª               ª               RemoveDestructorPass.php
    ª               ª               RemoveUnserializeForInternalSerializableClassesPass.php
    ª               ª               TraitPass.php
    ª               ª               
    ª               +---Loader
    ª               ª       EvalLoader.php
    ª               ª       Loader.php
    ª               ª       RequireLoader.php
    ª               ª       
    ª               +---Matcher
    ª                       AndAnyOtherArgs.php
    ª                       Any.php
    ª                       AnyArgs.php
    ª                       AnyOf.php
    ª                       ArgumentListMatcher.php
    ª                       Closure.php
    ª                       Contains.php
    ª                       Ducktype.php
    ª                       HasKey.php
    ª                       HasValue.php
    ª                       IsEqual.php
    ª                       IsSame.php
    ª                       MatcherAbstract.php
    ª                       MatcherInterface.php
    ª                       MultiArgumentClosure.php
    ª                       MustBe.php
    ª                       NoArgs.php
    ª                       Not.php
    ª                       NotAnyOf.php
    ª                       Pattern.php
    ª                       Subset.php
    ª                       Type.php
    ª                       
    +---monolog
    ª   +---monolog
    ª       ª   CHANGELOG.md
    ª       ª   composer.json
    ª       ª   LICENSE
    ª       ª   README.md
    ª       ª   
    ª       +---src
    ª           +---Monolog
    ª               ª   DateTimeImmutable.php
    ª               ª   ErrorHandler.php
    ª               ª   JsonSerializableDateTimeImmutable.php
    ª               ª   Level.php
    ª               ª   Logger.php
    ª               ª   LogRecord.php
    ª               ª   Registry.php
    ª               ª   ResettableInterface.php
    ª               ª   SignalHandler.php
    ª               ª   Utils.php
    ª               ª   
    ª               +---Attribute
    ª               ª       AsMonologProcessor.php
    ª               ª       WithMonologChannel.php
    ª               ª       
    ª               +---Formatter
    ª               ª       ChromePHPFormatter.php
    ª               ª       ElasticaFormatter.php
    ª               ª       ElasticsearchFormatter.php
    ª               ª       FlowdockFormatter.php
    ª               ª       FluentdFormatter.php
    ª               ª       FormatterInterface.php
    ª               ª       GelfMessageFormatter.php
    ª               ª       GoogleCloudLoggingFormatter.php
    ª               ª       HtmlFormatter.php
    ª               ª       JsonFormatter.php
    ª               ª       LineFormatter.php
    ª               ª       LogglyFormatter.php
    ª               ª       LogmaticFormatter.php
    ª               ª       LogstashFormatter.php
    ª               ª       MongoDBFormatter.php
    ª               ª       NormalizerFormatter.php
    ª               ª       ScalarFormatter.php
    ª               ª       SyslogFormatter.php
    ª               ª       WildfireFormatter.php
    ª               ª       
    ª               +---Handler
    ª               ª   ª   AbstractHandler.php
    ª               ª   ª   AbstractProcessingHandler.php
    ª               ª   ª   AbstractSyslogHandler.php
    ª               ª   ª   AmqpHandler.php
    ª               ª   ª   BrowserConsoleHandler.php
    ª               ª   ª   BufferHandler.php
    ª               ª   ª   ChromePHPHandler.php
    ª               ª   ª   CouchDBHandler.php
    ª               ª   ª   CubeHandler.php
    ª               ª   ª   DeduplicationHandler.php
    ª               ª   ª   DoctrineCouchDBHandler.php
    ª               ª   ª   DynamoDbHandler.php
    ª               ª   ª   ElasticaHandler.php
    ª               ª   ª   ElasticsearchHandler.php
    ª               ª   ª   ErrorLogHandler.php
    ª               ª   ª   FallbackGroupHandler.php
    ª               ª   ª   FilterHandler.php
    ª               ª   ª   FingersCrossedHandler.php
    ª               ª   ª   FirePHPHandler.php
    ª               ª   ª   FleepHookHandler.php
    ª               ª   ª   FlowdockHandler.php
    ª               ª   ª   FormattableHandlerInterface.php
    ª               ª   ª   FormattableHandlerTrait.php
    ª               ª   ª   GelfHandler.php
    ª               ª   ª   GroupHandler.php
    ª               ª   ª   Handler.php
    ª               ª   ª   HandlerInterface.php
    ª               ª   ª   HandlerWrapper.php
    ª               ª   ª   IFTTTHandler.php
    ª               ª   ª   InsightOpsHandler.php
    ª               ª   ª   LogEntriesHandler.php
    ª               ª   ª   LogglyHandler.php
    ª               ª   ª   LogmaticHandler.php
    ª               ª   ª   MailHandler.php
    ª               ª   ª   MandrillHandler.php
    ª               ª   ª   MissingExtensionException.php
    ª               ª   ª   MongoDBHandler.php
    ª               ª   ª   NativeMailerHandler.php
    ª               ª   ª   NewRelicHandler.php
    ª               ª   ª   NoopHandler.php
    ª               ª   ª   NullHandler.php
    ª               ª   ª   OverflowHandler.php
    ª               ª   ª   PHPConsoleHandler.php
    ª               ª   ª   ProcessableHandlerInterface.php
    ª               ª   ª   ProcessableHandlerTrait.php
    ª               ª   ª   ProcessHandler.php
    ª               ª   ª   PsrHandler.php
    ª               ª   ª   PushoverHandler.php
    ª               ª   ª   RedisHandler.php
    ª               ª   ª   RedisPubSubHandler.php
    ª               ª   ª   RollbarHandler.php
    ª               ª   ª   RotatingFileHandler.php
    ª               ª   ª   SamplingHandler.php
    ª               ª   ª   SendGridHandler.php
    ª               ª   ª   SlackHandler.php
    ª               ª   ª   SlackWebhookHandler.php
    ª               ª   ª   SocketHandler.php
    ª               ª   ª   SqsHandler.php
    ª               ª   ª   StreamHandler.php
    ª               ª   ª   SymfonyMailerHandler.php
    ª               ª   ª   SyslogHandler.php
    ª               ª   ª   SyslogUdpHandler.php
    ª               ª   ª   TelegramBotHandler.php
    ª               ª   ª   TestHandler.php
    ª               ª   ª   WebRequestRecognizerTrait.php
    ª               ª   ª   WhatFailureGroupHandler.php
    ª               ª   ª   ZendMonitorHandler.php
    ª               ª   ª   
    ª               ª   +---Curl
    ª               ª   ª       Util.php
    ª               ª   ª       
    ª               ª   +---FingersCrossed
    ª               ª   ª       ActivationStrategyInterface.php
    ª               ª   ª       ChannelLevelActivationStrategy.php
    ª               ª   ª       ErrorLevelActivationStrategy.php
    ª               ª   ª       
    ª               ª   +---Slack
    ª               ª   ª       SlackRecord.php
    ª               ª   ª       
    ª               ª   +---SyslogUdp
    ª               ª           UdpSocket.php
    ª               ª           
    ª               +---Processor
    ª               ª       ClosureContextProcessor.php
    ª               ª       GitProcessor.php
    ª               ª       HostnameProcessor.php
    ª               ª       IntrospectionProcessor.php
    ª               ª       LoadAverageProcessor.php
    ª               ª       MemoryPeakUsageProcessor.php
    ª               ª       MemoryProcessor.php
    ª               ª       MemoryUsageProcessor.php
    ª               ª       MercurialProcessor.php
    ª               ª       ProcessIdProcessor.php
    ª               ª       ProcessorInterface.php
    ª               ª       PsrLogMessageProcessor.php
    ª               ª       TagProcessor.php
    ª               ª       UidProcessor.php
    ª               ª       WebProcessor.php
    ª               ª       
    ª               +---Test
    ª                       TestCase.php
    ª                       
    +---myclabs
    ª   +---deep-copy
    ª       ª   composer.json
    ª       ª   LICENSE
    ª       ª   README.md
    ª       ª   
    ª       +---src
    ª           +---DeepCopy
    ª               ª   DeepCopy.php
    ª               ª   deep_copy.php
    ª               ª   
    ª               +---Exception
    ª               ª       CloneException.php
    ª               ª       PropertyException.php
    ª               ª       
    ª               +---Filter
    ª               ª   ª   ChainableFilter.php
    ª               ª   ª   Filter.php
    ª               ª   ª   KeepFilter.php
    ª               ª   ª   ReplaceFilter.php
    ª               ª   ª   SetNullFilter.php
    ª               ª   ª   
    ª               ª   +---Doctrine
    ª               ª           DoctrineCollectionFilter.php
    ª               ª           DoctrineEmptyCollectionFilter.php
    ª               ª           DoctrineProxyFilter.php
    ª               ª           
    ª               +---Matcher
    ª               ª   ª   Matcher.php
    ª               ª   ª   PropertyMatcher.php
    ª               ª   ª   PropertyNameMatcher.php
    ª               ª   ª   PropertyTypeMatcher.php
    ª               ª   ª   
    ª               ª   +---Doctrine
    ª               ª           DoctrineProxyMatcher.php
    ª               ª           
    ª               +---Reflection
    ª               ª       ReflectionHelper.php
    ª               ª       
    ª               +---TypeFilter
    ª               ª   ª   ReplaceFilter.php
    ª               ª   ª   ShallowCopyFilter.php
    ª               ª   ª   TypeFilter.php
    ª               ª   ª   
    ª               ª   +---Date
    ª               ª   ª       DateIntervalFilter.php
    ª               ª   ª       DatePeriodFilter.php
    ª               ª   ª       
    ª               ª   +---Spl
    ª               ª           ArrayObjectFilter.php
    ª               ª           SplDoublyLinkedList.php
    ª               ª           SplDoublyLinkedListFilter.php
    ª               ª           
    ª               +---TypeMatcher
    ª                       TypeMatcher.php
    ª                       
    +---nesbot
    ª   +---carbon
    ª       ª   .phpstorm.meta.php
    ª       ª   composer.json
    ª       ª   extension.neon
    ª       ª   LICENSE
    ª       ª   readme.md
    ª       ª   sponsors.php
    ª       ª   
    ª       +---bin
    ª       ª       carbon
    ª       ª       carbon.bat
    ª       ª       
    ª       +---lazy
    ª       ª   +---Carbon
    ª       ª       ª   TranslatorStrongType.php
    ª       ª       ª   TranslatorWeakType.php
    ª       ª       ª   
    ª       ª       +---MessageFormatter
    ª       ª       ª       MessageFormatterMapperStrongType.php
    ª       ª       ª       MessageFormatterMapperWeakType.php
    ª       ª       ª       
    ª       ª       +---PHPStan
    ª       ª               AbstractMacroBuiltin.php
    ª       ª               AbstractMacroStatic.php
    ª       ª               MacroStrongType.php
    ª       ª               MacroWeakType.php
    ª       ª               
    ª       +---src
    ª           +---Carbon
    ª               ª   AbstractTranslator.php
    ª               ª   Carbon.php
    ª               ª   CarbonConverterInterface.php
    ª               ª   CarbonImmutable.php
    ª               ª   CarbonInterface.php
    ª               ª   CarbonInterval.php
    ª               ª   CarbonPeriod.php
    ª               ª   CarbonPeriodImmutable.php
    ª               ª   CarbonTimeZone.php
    ª               ª   Factory.php
    ª               ª   FactoryImmutable.php
    ª               ª   Language.php
    ª               ª   Translator.php
    ª               ª   TranslatorImmutable.php
    ª               ª   TranslatorStrongTypeInterface.php
    ª               ª   
    ª               +---Cli
    ª               ª       Invoker.php
    ª               ª       
    ª               +---Exceptions
    ª               ª       BadComparisonUnitException.php
    ª               ª       BadFluentConstructorException.php
    ª               ª       BadFluentSetterException.php
    ª               ª       BadMethodCallException.php
    ª               ª       EndLessPeriodException.php
    ª               ª       Exception.php
    ª               ª       ImmutableException.php
    ª               ª       InvalidArgumentException.php
    ª               ª       InvalidCastException.php
    ª               ª       InvalidDateException.php
    ª               ª       InvalidFormatException.php
    ª               ª       InvalidIntervalException.php
    ª               ª       InvalidPeriodDateException.php
    ª               ª       InvalidPeriodParameterException.php
    ª               ª       InvalidTimeZoneException.php
    ª               ª       InvalidTypeException.php
    ª               ª       NotACarbonClassException.php
    ª               ª       NotAPeriodException.php
    ª               ª       NotLocaleAwareException.php
    ª               ª       OutOfRangeException.php
    ª               ª       ParseErrorException.php
    ª               ª       RuntimeException.php
    ª               ª       UnitException.php
    ª               ª       UnitNotConfiguredException.php
    ª               ª       UnknownGetterException.php
    ª               ª       UnknownMethodException.php
    ª               ª       UnknownSetterException.php
    ª               ª       UnknownUnitException.php
    ª               ª       UnreachableException.php
    ª               ª       
    ª               +---Lang
    ª               ª       aa.php
    ª               ª       aa_DJ.php
    ª               ª       aa_ER.php
    ª               ª       aa_ER@saaho.php
    ª               ª       aa_ET.php
    ª               ª       af.php
    ª               ª       af_NA.php
    ª               ª       af_ZA.php
    ª               ª       agq.php
    ª               ª       agr.php
    ª               ª       agr_PE.php
    ª               ª       ak.php
    ª               ª       ak_GH.php
    ª               ª       am.php
    ª               ª       am_ET.php
    ª               ª       an.php
    ª               ª       anp.php
    ª               ª       anp_IN.php
    ª               ª       an_ES.php
    ª               ª       ar.php
    ª               ª       ar_AE.php
    ª               ª       ar_BH.php
    ª               ª       ar_DJ.php
    ª               ª       ar_DZ.php
    ª               ª       ar_EG.php
    ª               ª       ar_EH.php
    ª               ª       ar_ER.php
    ª               ª       ar_IL.php
    ª               ª       ar_IN.php
    ª               ª       ar_IQ.php
    ª               ª       ar_JO.php
    ª               ª       ar_KM.php
    ª               ª       ar_KW.php
    ª               ª       ar_LB.php
    ª               ª       ar_LY.php
    ª               ª       ar_MA.php
    ª               ª       ar_MR.php
    ª               ª       ar_OM.php
    ª               ª       ar_PS.php
    ª               ª       ar_QA.php
    ª               ª       ar_SA.php
    ª               ª       ar_SD.php
    ª               ª       ar_Shakl.php
    ª               ª       ar_SO.php
    ª               ª       ar_SS.php
    ª               ª       ar_SY.php
    ª               ª       ar_TD.php
    ª               ª       ar_TN.php
    ª               ª       ar_YE.php
    ª               ª       as.php
    ª               ª       asa.php
    ª               ª       ast.php
    ª               ª       ast_ES.php
    ª               ª       as_IN.php
    ª               ª       ayc.php
    ª               ª       ayc_PE.php
    ª               ª       az.php
    ª               ª       az_AZ.php
    ª               ª       az_Cyrl.php
    ª               ª       az_IR.php
    ª               ª       az_Latn.php
    ª               ª       bas.php
    ª               ª       be.php
    ª               ª       bem.php
    ª               ª       bem_ZM.php
    ª               ª       ber.php
    ª               ª       ber_DZ.php
    ª               ª       ber_MA.php
    ª               ª       bez.php
    ª               ª       be_BY.php
    ª               ª       be_BY@latin.php
    ª               ª       bg.php
    ª               ª       bg_BG.php
    ª               ª       bhb.php
    ª               ª       bhb_IN.php
    ª               ª       bho.php
    ª               ª       bho_IN.php
    ª               ª       bi.php
    ª               ª       bi_VU.php
    ª               ª       bm.php
    ª               ª       bn.php
    ª               ª       bn_BD.php
    ª               ª       bn_IN.php
    ª               ª       bo.php
    ª               ª       bo_CN.php
    ª               ª       bo_IN.php
    ª               ª       br.php
    ª               ª       brx.php
    ª               ª       brx_IN.php
    ª               ª       br_FR.php
    ª               ª       bs.php
    ª               ª       bs_BA.php
    ª               ª       bs_Cyrl.php
    ª               ª       bs_Latn.php
    ª               ª       byn.php
    ª               ª       byn_ER.php
    ª               ª       ca.php
    ª               ª       ca_AD.php
    ª               ª       ca_ES.php
    ª               ª       ca_ES_Valencia.php
    ª               ª       ca_FR.php
    ª               ª       ca_IT.php
    ª               ª       ccp.php
    ª               ª       ccp_IN.php
    ª               ª       ce.php
    ª               ª       ce_RU.php
    ª               ª       cgg.php
    ª               ª       chr.php
    ª               ª       chr_US.php
    ª               ª       ckb.php
    ª               ª       cmn.php
    ª               ª       cmn_TW.php
    ª               ª       crh.php
    ª               ª       crh_UA.php
    ª               ª       cs.php
    ª               ª       csb.php
    ª               ª       csb_PL.php
    ª               ª       cs_CZ.php
    ª               ª       cu.php
    ª               ª       cv.php
    ª               ª       cv_RU.php
    ª               ª       cy.php
    ª               ª       cy_GB.php
    ª               ª       da.php
    ª               ª       dav.php
    ª               ª       da_DK.php
    ª               ª       da_GL.php
    ª               ª       de.php
    ª               ª       de_AT.php
    ª               ª       de_BE.php
    ª               ª       de_CH.php
    ª               ª       de_DE.php
    ª               ª       de_IT.php
    ª               ª       de_LI.php
    ª               ª       de_LU.php
    ª               ª       dje.php
    ª               ª       doi.php
    ª               ª       doi_IN.php
    ª               ª       dsb.php
    ª               ª       dsb_DE.php
    ª               ª       dua.php
    ª               ª       dv.php
    ª               ª       dv_MV.php
    ª               ª       dyo.php
    ª               ª       dz.php
    ª               ª       dz_BT.php
    ª               ª       ebu.php
    ª               ª       ee.php
    ª               ª       ee_TG.php
    ª               ª       el.php
    ª               ª       el_CY.php
    ª               ª       el_GR.php
    ª               ª       en.php
    ª               ª       en_001.php
    ª               ª       en_150.php
    ª               ª       en_AG.php
    ª               ª       en_AI.php
    ª               ª       en_AS.php
    ª               ª       en_AT.php
    ª               ª       en_AU.php
    ª               ª       en_BB.php
    ª               ª       en_BE.php
    ª               ª       en_BI.php
    ª               ª       en_BM.php
    ª               ª       en_BS.php
    ª               ª       en_BW.php
    ª               ª       en_BZ.php
    ª               ª       en_CA.php
    ª               ª       en_CC.php
    ª               ª       en_CH.php
    ª               ª       en_CK.php
    ª               ª       en_CM.php
    ª               ª       en_CX.php
    ª               ª       en_CY.php
    ª               ª       en_DE.php
    ª               ª       en_DG.php
    ª               ª       en_DK.php
    ª               ª       en_DM.php
    ª               ª       en_ER.php
    ª               ª       en_FI.php
    ª               ª       en_FJ.php
    ª               ª       en_FK.php
    ª               ª       en_FM.php
    ª               ª       en_GB.php
    ª               ª       en_GD.php
    ª               ª       en_GG.php
    ª               ª       en_GH.php
    ª               ª       en_GI.php
    ª               ª       en_GM.php
    ª               ª       en_GU.php
    ª               ª       en_GY.php
    ª               ª       en_HK.php
    ª               ª       en_IE.php
    ª               ª       en_IL.php
    ª               ª       en_IM.php
    ª               ª       en_IN.php
    ª               ª       en_IO.php
    ª               ª       en_ISO.php
    ª               ª       en_JE.php
    ª               ª       en_JM.php
    ª               ª       en_KE.php
    ª               ª       en_KI.php
    ª               ª       en_KN.php
    ª               ª       en_KY.php
    ª               ª       en_LC.php
    ª               ª       en_LR.php
    ª               ª       en_LS.php
    ª               ª       en_MG.php
    ª               ª       en_MH.php
    ª               ª       en_MO.php
    ª               ª       en_MP.php
    ª               ª       en_MS.php
    ª               ª       en_MT.php
    ª               ª       en_MU.php
    ª               ª       en_MW.php
    ª               ª       en_MY.php
    ª               ª       en_NA.php
    ª               ª       en_NF.php
    ª               ª       en_NG.php
    ª               ª       en_NL.php
    ª               ª       en_NR.php
    ª               ª       en_NU.php
    ª               ª       en_NZ.php
    ª               ª       en_PG.php
    ª               ª       en_PH.php
    ª               ª       en_PK.php
    ª               ª       en_PN.php
    ª               ª       en_PR.php
    ª               ª       en_PW.php
    ª               ª       en_RW.php
    ª               ª       en_SB.php
    ª               ª       en_SC.php
    ª               ª       en_SD.php
    ª               ª       en_SE.php
    ª               ª       en_SG.php
    ª               ª       en_SH.php
    ª               ª       en_SI.php
    ª               ª       en_SL.php
    ª               ª       en_SS.php
    ª               ª       en_SX.php
    ª               ª       en_SZ.php
    ª               ª       en_TC.php
    ª               ª       en_TK.php
    ª               ª       en_TO.php
    ª               ª       en_TT.php
    ª               ª       en_TV.php
    ª               ª       en_TZ.php
    ª               ª       en_UG.php
    ª               ª       en_UM.php
    ª               ª       en_US.php
    ª               ª       en_US_Posix.php
    ª               ª       en_VC.php
    ª               ª       en_VG.php
    ª               ª       en_VI.php
    ª               ª       en_VU.php
    ª               ª       en_WS.php
    ª               ª       en_ZA.php
    ª               ª       en_ZM.php
    ª               ª       en_ZW.php
    ª               ª       eo.php
    ª               ª       es.php
    ª               ª       es_419.php
    ª               ª       es_AR.php
    ª               ª       es_BO.php
    ª               ª       es_BR.php
    ª               ª       es_BZ.php
    ª               ª       es_CL.php
    ª               ª       es_CO.php
    ª               ª       es_CR.php
    ª               ª       es_CU.php
    ª               ª       es_DO.php
    ª               ª       es_EA.php
    ª               ª       es_EC.php
    ª               ª       es_ES.php
    ª               ª       es_GQ.php
    ª               ª       es_GT.php
    ª               ª       es_HN.php
    ª               ª       es_IC.php
    ª               ª       es_MX.php
    ª               ª       es_NI.php
    ª               ª       es_PA.php
    ª               ª       es_PE.php
    ª               ª       es_PH.php
    ª               ª       es_PR.php
    ª               ª       es_PY.php
    ª               ª       es_SV.php
    ª               ª       es_US.php
    ª               ª       es_UY.php
    ª               ª       es_VE.php
    ª               ª       et.php
    ª               ª       et_EE.php
    ª               ª       eu.php
    ª               ª       eu_ES.php
    ª               ª       ewo.php
    ª               ª       fa.php
    ª               ª       fa_AF.php
    ª               ª       fa_IR.php
    ª               ª       ff.php
    ª               ª       ff_CM.php
    ª               ª       ff_GN.php
    ª               ª       ff_MR.php
    ª               ª       ff_SN.php
    ª               ª       fi.php
    ª               ª       fil.php
    ª               ª       fil_PH.php
    ª               ª       fi_FI.php
    ª               ª       fo.php
    ª               ª       fo_DK.php
    ª               ª       fo_FO.php
    ª               ª       fr.php
    ª               ª       fr_BE.php
    ª               ª       fr_BF.php
    ª               ª       fr_BI.php
    ª               ª       fr_BJ.php
    ª               ª       fr_BL.php
    ª               ª       fr_CA.php
    ª               ª       fr_CD.php
    ª               ª       fr_CF.php
    ª               ª       fr_CG.php
    ª               ª       fr_CH.php
    ª               ª       fr_CI.php
    ª               ª       fr_CM.php
    ª               ª       fr_DJ.php
    ª               ª       fr_DZ.php
    ª               ª       fr_FR.php
    ª               ª       fr_GA.php
    ª               ª       fr_GF.php
    ª               ª       fr_GN.php
    ª               ª       fr_GP.php
    ª               ª       fr_GQ.php
    ª               ª       fr_HT.php
    ª               ª       fr_KM.php
    ª               ª       fr_LU.php
    ª               ª       fr_MA.php
    ª               ª       fr_MC.php
    ª               ª       fr_MF.php
    ª               ª       fr_MG.php
    ª               ª       fr_ML.php
    ª               ª       fr_MQ.php
    ª               ª       fr_MR.php
    ª               ª       fr_MU.php
    ª               ª       fr_NC.php
    ª               ª       fr_NE.php
    ª               ª       fr_PF.php
    ª               ª       fr_PM.php
    ª               ª       fr_RE.php
    ª               ª       fr_RW.php
    ª               ª       fr_SC.php
    ª               ª       fr_SN.php
    ª               ª       fr_SY.php
    ª               ª       fr_TD.php
    ª               ª       fr_TG.php
    ª               ª       fr_TN.php
    ª               ª       fr_VU.php
    ª               ª       fr_WF.php
    ª               ª       fr_YT.php
    ª               ª       fur.php
    ª               ª       fur_IT.php
    ª               ª       fy.php
    ª               ª       fy_DE.php
    ª               ª       fy_NL.php
    ª               ª       ga.php
    ª               ª       ga_IE.php
    ª               ª       gd.php
    ª               ª       gd_GB.php
    ª               ª       gez.php
    ª               ª       gez_ER.php
    ª               ª       gez_ET.php
    ª               ª       gl.php
    ª               ª       gl_ES.php
    ª               ª       gom.php
    ª               ª       gom_Latn.php
    ª               ª       gsw.php
    ª               ª       gsw_CH.php
    ª               ª       gsw_FR.php
    ª               ª       gsw_LI.php
    ª               ª       gu.php
    ª               ª       guz.php
    ª               ª       gu_IN.php
    ª               ª       gv.php
    ª               ª       gv_GB.php
    ª               ª       ha.php
    ª               ª       hak.php
    ª               ª       hak_TW.php
    ª               ª       haw.php
    ª               ª       ha_GH.php
    ª               ª       ha_NE.php
    ª               ª       ha_NG.php
    ª               ª       he.php
    ª               ª       he_IL.php
    ª               ª       hi.php
    ª               ª       hif.php
    ª               ª       hif_FJ.php
    ª               ª       hi_IN.php
    ª               ª       hne.php
    ª               ª       hne_IN.php
    ª               ª       hr.php
    ª               ª       hr_BA.php
    ª               ª       hr_HR.php
    ª               ª       hsb.php
    ª               ª       hsb_DE.php
    ª               ª       ht.php
    ª               ª       ht_HT.php
    ª               ª       hu.php
    ª               ª       hu_HU.php
    ª               ª       hy.php
    ª               ª       hy_AM.php
    ª               ª       i18n.php
    ª               ª       ia.php
    ª               ª       ia_FR.php
    ª               ª       id.php
    ª               ª       id_ID.php
    ª               ª       ig.php
    ª               ª       ig_NG.php
    ª               ª       ii.php
    ª               ª       ik.php
    ª               ª       ik_CA.php
    ª               ª       in.php
    ª               ª       is.php
    ª               ª       is_IS.php
    ª               ª       it.php
    ª               ª       it_CH.php
    ª               ª       it_IT.php
    ª               ª       it_SM.php
    ª               ª       it_VA.php
    ª               ª       iu.php
    ª               ª       iu_CA.php
    ª               ª       iw.php
    ª               ª       ja.php
    ª               ª       ja_JP.php
    ª               ª       jgo.php
    ª               ª       jmc.php
    ª               ª       jv.php
    ª               ª       ka.php
    ª               ª       kab.php
    ª               ª       kab_DZ.php
    ª               ª       kam.php
    ª               ª       ka_GE.php
    ª               ª       kde.php
    ª               ª       kea.php
    ª               ª       khq.php
    ª               ª       ki.php
    ª               ª       kk.php
    ª               ª       kkj.php
    ª               ª       kk_KZ.php
    ª               ª       kl.php
    ª               ª       kln.php
    ª               ª       kl_GL.php
    ª               ª       km.php
    ª               ª       km_KH.php
    ª               ª       kn.php
    ª               ª       kn_IN.php
    ª               ª       ko.php
    ª               ª       kok.php
    ª               ª       kok_IN.php
    ª               ª       ko_KP.php
    ª               ª       ko_KR.php
    ª               ª       ks.php
    ª               ª       ksb.php
    ª               ª       ksf.php
    ª               ª       ksh.php
    ª               ª       ks_IN.php
    ª               ª       ks_IN@devanagari.php
    ª               ª       ku.php
    ª               ª       ku_TR.php
    ª               ª       kw.php
    ª               ª       kw_GB.php
    ª               ª       ky.php
    ª               ª       ky_KG.php
    ª               ª       lag.php
    ª               ª       lb.php
    ª               ª       lb_LU.php
    ª               ª       lg.php
    ª               ª       lg_UG.php
    ª               ª       li.php
    ª               ª       lij.php
    ª               ª       lij_IT.php
    ª               ª       li_NL.php
    ª               ª       lkt.php
    ª               ª       ln.php
    ª               ª       ln_AO.php
    ª               ª       ln_CD.php
    ª               ª       ln_CF.php
    ª               ª       ln_CG.php
    ª               ª       lo.php
    ª               ª       lo_LA.php
    ª               ª       lrc.php
    ª               ª       lrc_IQ.php
    ª               ª       lt.php
    ª               ª       lt_LT.php
    ª               ª       lu.php
    ª               ª       luo.php
    ª               ª       luy.php
    ª               ª       lv.php
    ª               ª       lv_LV.php
    ª               ª       lzh.php
    ª               ª       lzh_TW.php
    ª               ª       mag.php
    ª               ª       mag_IN.php
    ª               ª       mai.php
    ª               ª       mai_IN.php
    ª               ª       mas.php
    ª               ª       mas_TZ.php
    ª               ª       mer.php
    ª               ª       mfe.php
    ª               ª       mfe_MU.php
    ª               ª       mg.php
    ª               ª       mgh.php
    ª               ª       mgo.php
    ª               ª       mg_MG.php
    ª               ª       mhr.php
    ª               ª       mhr_RU.php
    ª               ª       mi.php
    ª               ª       miq.php
    ª               ª       miq_NI.php
    ª               ª       mi_NZ.php
    ª               ª       mjw.php
    ª               ª       mjw_IN.php
    ª               ª       mk.php
    ª               ª       mk_MK.php
    ª               ª       ml.php
    ª               ª       ml_IN.php
    ª               ª       mn.php
    ª               ª       mni.php
    ª               ª       mni_IN.php
    ª               ª       mn_MN.php
    ª               ª       mo.php
    ª               ª       mr.php
    ª               ª       mr_IN.php
    ª               ª       ms.php
    ª               ª       ms_BN.php
    ª               ª       ms_MY.php
    ª               ª       ms_SG.php
    ª               ª       mt.php
    ª               ª       mt_MT.php
    ª               ª       mua.php
    ª               ª       my.php
    ª               ª       my_MM.php
    ª               ª       mzn.php
    ª               ª       nan.php
    ª               ª       nan_TW.php
    ª               ª       nan_TW@latin.php
    ª               ª       naq.php
    ª               ª       nb.php
    ª               ª       nb_NO.php
    ª               ª       nb_SJ.php
    ª               ª       nd.php
    ª               ª       nds.php
    ª               ª       nds_DE.php
    ª               ª       nds_NL.php
    ª               ª       ne.php
    ª               ª       ne_IN.php
    ª               ª       ne_NP.php
    ª               ª       nhn.php
    ª               ª       nhn_MX.php
    ª               ª       niu.php
    ª               ª       niu_NU.php
    ª               ª       nl.php
    ª               ª       nl_AW.php
    ª               ª       nl_BE.php
    ª               ª       nl_BQ.php
    ª               ª       nl_CW.php
    ª               ª       nl_NL.php
    ª               ª       nl_SR.php
    ª               ª       nl_SX.php
    ª               ª       nmg.php
    ª               ª       nn.php
    ª               ª       nnh.php
    ª               ª       nn_NO.php
    ª               ª       no.php
    ª               ª       nr.php
    ª               ª       nr_ZA.php
    ª               ª       nso.php
    ª               ª       nso_ZA.php
   

## Authentication (To Be Implemented)

### Requirements

Ok. . based on what you know about it, what would be the best approach for authentication considering we want to have:
1) Roles (agent/manager/admin)
2) The ability for agents to register but not choose agency (either by a URL value orsome other flag to tell the system what the agency the agent is attached to.
3) Password reset mechanism
4) Add 2FA in the future
5) Add alt login (Google, etc) in the future.
6) Enforce password length

Perfect — you’re asking all the right questions early. Given your goals and Laravel as your stack, here’s a **solid plan for authentication** that balances *built-in ease* with *future flexibility*.

---

## 🔐 Recommended Approach: Use **Laravel Breeze + Laravel Fortify + Laravel Socialite**

Each tool covers your needs:

| Need | Tool |
|------|------|
| Basic Auth | **Laravel Breeze** (simple, Blade-based auth scaffolding) |
| Password resets, enforcement, 2FA | **Laravel Fortify** (customizable auth backend) |
| Social logins (Google, etc.) | **Laravel Socialite** |
| Roles | Use a package like **Spatie Laravel Permission** or a simple custom role setup |
| Custom agent registration logic | Handle in controller (pass agency by URL, code, etc.) |

---

## ✅ Step-by-Step Plan

---

### **1. Install Laravel Breeze (or Jetstream if you want a more Vue/Livewire UI)**

For a Blade-based app:

```bash
composer require laravel/breeze --dev
php artisan breeze:install
npm install && npm run dev
php artisan migrate
```

Breeze gives:
- Registration
- Login
- Logout
- Password reset (with email)
- Email verification
- Fully customizable Blade templates

---

### **2. Add Laravel Fortify (for 2FA, password rules, etc.)**

```bash
composer require laravel/fortify
php artisan vendor:publish --provider="Laravel\Fortify\FortifyServiceProvider"
```

Then configure:

- `config/fortify.php`: enable 2FA, password rules, etc.
- Add routes/middleware if needed

**Password enforcement**:
```php
// In App\Actions\Fortify\CreateNewUser.php
use Illuminate\Validation\Rules\Password;

$request->validate([
    'password' => ['required', 'confirmed', Password::min(12)->mixedCase()->numbers()->symbols()],
]);
```

---

### **3. Add Social Logins (Google etc.) with Laravel Socialite**

```bash
composer require laravel/socialite
```

Then:
- Set up Google OAuth app
- Configure `.env`:
```env
GOOGLE_CLIENT_ID=your_id
GOOGLE_CLIENT_SECRET=your_secret
GOOGLE_REDIRECT_URI=http://your-app.com/login/google/callback
```

- Add logic in controller to:
  - Redirect to Google
  - Handle callback
  - Create or log in user
  - Assign role if needed

---

### **4. Add Role Management (Spatie or DIY)**

The [Spatie Laravel Permission package](https://spatie.be/docs/laravel-permission/v5/introduction) is clean and powerful.

Install:

```bash
composer require spatie/laravel-permission
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider"
php artisan migrate
```

Then:

- Create roles: `Agent`, `Manager`, `Admin`
- Assign via `$user->assignRole('Agent')`
- Check via `$user->hasRole('Manager')`

---

### **5. Custom Agent Registration (with auto-agency link)**

Agents can register via a **custom route like**:

```
/register?agency=blueco
```

Then:
- In `RegisterController` (or Fortify `CreateNewUser` class):
  - Grab the `agency` value from the query string or hidden field
  - Validate it
  - Look up agency ID
  - Attach it to the user record

Example:

```php
$agencyCode = $request->query('agency');
$agency = Agency::where('slug', $agencyCode)->firstOrFail();
$user->agency_id = $agency->id;
```

You can even generate unique registration links per agency:
```
https://yourapp.com/register?agency=acme-union
```

---

### **6. Password Reset is Already Covered**
Both **Breeze** and **Fortify** give you secure password reset with email + token.

---

### **7. Future-Proofing: 2FA and Beyond**
With Fortify you can enable 2FA now or later:

```php
'features' => [
    Features::twoFactorAuthentication([
        'confirmPassword' => true,
    ]),
],
```

It will handle the 2FA logic, backup codes, etc.

---

## 📦 Bonus: Put It All Together in Middleware

To protect certain routes by role:

```php
Route::middleware(['auth', 'role:Agent'])->group(function () {
    // Agent-only stuff
});
```

Or for admins:
```php
Route::middleware(['auth', 'role:Admin'])->group(function () {
    // Admin panel
});
```

---

## ✅ Summary

| Feature | Covered By |
|--------|-------------|
| Login/Registration | Laravel Breeze (UI) + Fortify (logic) |
| Role-based access | Spatie Laravel Permission |
| Password rules | Fortify `CreateNewUser` |
| Reset Passwords | Breeze/Fortify built-in |
| 2FA (optional now) | Fortify |
| Social Login (future) | Laravel Socialite |
| Agency-linked registration | Custom route param or token |

---

## 🔐 Authentication & User Management

Script Assist uses Laravel’s authentication system with enhancements to support roles, agency-specific registration, and future features like two-factor authentication and social login.

### Current Stack
- **Laravel Breeze** for authentication scaffolding (login, registration, password reset)
- **Laravel Fortify** for advanced auth features like 2FA, password rules, and email verification
- **Spatie Laravel Permission** for role-based access control

### Roles
Users are assigned one of the following roles:
- `agent` - an individual employee/member who can create presentations, view clients/referrals that they have created/assigned to them/ and change their own profile
- `manager` - an individual that can view/change info for any agent attached to the same agency as them.
- `admin`- superuser can see/access everything to the limit of the system.

**We may add a a system admin in the future that can impact **some** of the systems but not all.

These roles control access to views, routes, and admin functions.

### Agency-Linked Registration
Agents can register without selecting an agency. Instead, agency assignment is handled by:

- A query string parameter (e.g., `/register?agency=blueco`)
- A hidden field or token from a unique agency-specific registration link

On registration:
- The agency slug/code is validated
- The agent is attached to the correct agency ID in the database
- The agent is assigned the `agent` role automatically

### Password Rules
Passwords must meet the following criteria:
- Minimum length: 12 characters
- At least one uppercase letter, one lowercase letter, one number, and one symbol

These rules are enforced in Laravel Fortify’s `CreateNewUser` class.

### Password Reset
Laravel’s built-in password reset system is enabled, with:
- Email-based reset tokens
- Secure hashed storage
- Configurable expiration

### Planned Enhancements
- **Two-Factor Authentication** (TOTP + backup codes) using Laravel Fortify
- **Social Login** with Google (and others) via Laravel Socialite
- **Optional Email Verification**
- **Admin UI** for managing roles and users (pending)

---

## Slide sync (detailed)

Slide sync will (as of 4/5/25) work of the following logic.

Each scriptContent line has an associated slideID
That determines what slide is displayed in both the thumbnail viewer and the customer view.
Slide change occurs *only when* the scriptContent line that is "active" has a *different* slideID.
SlideID in the scriptContent is *always* determines what slide is currently showing.


## Placeholders (To be added)

We will use dynamic Placeholders {{Client_firstname}}, {{Client_lastname}}, {{Agent_Name}} etc that will be pulled out of

1) the Agent table for the entry of the current logged in agent.
2) the Client information pulled from the client table (if one exists) if no entry is available for placeholder we will show alternate text (Greetings and Saluatations!  vs "Hello Dave!" as an example.
3) We will always denote dynamic placeholders with dual curved brackets {{placeholderName}} in appropriate places.

## Testing (to be looked at and this *will* change 

We will test, once we have full implementation with live user testing in mock situations at first (to both test functionality and usability).  We will also have to undergo some amount of testing on the routes/and database pulls but that's a future problem.

---

## Logging (Future Problem)

Before live launch we will need to implement logic/database strucutres to log.
1) agent activity
2) Presentation activity
3) Any system issues/messages
4) Whatever else we decide to add.

---

## 🤖 AI Integration (Planned)

ScriptAssist will incorporate targeted AI features to support sales agents during live presentations, reduce mental load, and personalize the experience without breaking script flow.

### 🔑 Planned AI Use Cases

- **Rebuttal Generator** – AI suggests 1–3 phrased responses to common objections, based on script section or keyword input.
- **Script Rephraser** – Allows agents to simplify or rewrite script lines with clearer or more natural phrasing.
- **Script Summary / Section Summary** – Quick agent-facing review of a script or section before a presentation.
- **Post-Call Summary Generator** – Summarizes what was covered and suggests follow-up actions or notes.
- **Training Coach (future)** – Contextual assistant that explains purpose or intent of each section.

These features are designed to enhance usability, agent confidence, and reduce pre-call prep time — especially for new hires.

---

## 🧰 AI Tech Stack Additions

| Component | Description |
|----------|-------------|
| **OpenAI GPT-4 Turbo** | Primary LLM for structured prompting (via API + function calling optional) |
| **Laravel HTTP Client** | Handles outbound requests to OpenAI |
| **Laravel Queue** (recommended) | For async or delayed AI task processing |
| **Secure API Route** | Authenticated POST endpoint for AI tool requests |

---

# 💬 Prompt Structure: Rebuttal Generator

> This is the structure used to call OpenAI and generate 1–3 rebuttals.

### 🧠 Prompt Template

```text
You are a sales assistant helping a phone agent respond to customer objections in a respectful and persuasive way.

Context:
- Script Section: {{section_name}}
- Objection Type or Keyword: {{objection_type}}
- Tone Preference: {{tone}} (e.g., friendly, firm, logical, empathetic)

Please provide 2–3 phrased rebuttals that the agent could say naturally during the call. Each should be clear, non-aggressive, and concise.
```
---

#### Sample Call (Laravel)

```php

$response = Http::withToken(config('services.openai.key'))
    ->post('https://api.openai.com/v1/chat/completions', [
        'model' => 'gpt-4-1106-preview',
        'messages' => [
            ['role' => 'system', 'content' => 'You are a sales assistant helping phone agents craft persuasive rebuttals.'],
            ['role' => 'user', 'content' => $prompt],
        ],
        'temperature' => 0.7,
    ]);


```

---

#### Laravel Controller Stub

```php

<?php
// routes/api.php
Route::post('/rebuttal', [AIRebuttalController::class, 'generate'])->middleware('auth:sanctum');

// app/Http/Controllers/AIRebuttalController.php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Http;

class AIRebuttalController extends Controller
{
    public function generate(Request $request)
    {
        $request->validate([
            'section' => 'required|string',
            'objection' => 'required|string',
            'tone' => 'nullable|string',
        ]);

        $prompt = "You are a sales assistant helping a phone agent respond to customer objections in a respectful and persuasive way.\n\n" .
                  "Context:\n" .
                  "- Script Section: {$request->section}\n" .
                  "- Objection Type or Keyword: {$request->objection}\n" .
                  "- Tone Preference: {$request->tone ?? 'friendly'}\n\n" .
                  "Please provide 2–3 phrased rebuttals that the agent could say naturally during the call. Each should be clear, non-aggressive, and concise.";

        $response = Http::withToken(config('services.openai.key'))
            ->post('https://api.openai.com/v1/chat/completions', [
                'model' => 'gpt-4-1106-preview',
                'messages' => [
                    ['role' => 'system', 'content' => 'You are a sales assistant helping phone agents craft persuasive rebuttals.'],
                    ['role' => 'user', 'content' => $prompt],
                ],
                'temperature' => 0.7,
            ]);

        return response()->json([
            'rebuttals' => $response['choices'][0]['message']['content'] ?? 'Sorry, no suggestions available right now.',
        ]);
    }
}


```


---
Absolutely — here's a clean markdown write-up summarizing what we built today. You can drop this at the bottom of your [`ScriptAssistProject.md`](https://github.com/hyberion/projectsDocumentation/blob/main/ScriptAssistProject.md) file:

---

### ✅ 2025-04-22: Modular Build – Script Section Selector

---

#### 🔧 Functionality:
- **Script Dropdown**: Lists all active scripts for `agencyID = 1`
- **Section Checkbox List**:
  - Hidden until a script is selected
  - Populated dynamically with all sections tied to the selected script (`scriptId`)
  - Default checkbox logic:
    - Sections with `sectionType = 'opening'` are **unchecked**
    - Sections with `sectionType = 'standard'` are **checked**
- **Query Result Output**:
  - Upon form submission, selected section IDs are passed to the backend
  - A query is executed to retrieve section details
  - Results are displayed in a formatted list on the page

---

#### 🗃️ Files Created:
- `routes/web.php`: Added new GET and POST routes for `/script-sections`
- `app/Http/Controllers/ScriptSectionController.php`: New controller handling both dropdown rendering and section submission
- `resources/views/script-section-selector.blade.php`: Blade file with:
  - Script dropdown
  - Conditional section checkboxes
  - Query result display block

---

#### 🛠️ Notes:
- Database uses `script` (not `scripts`) and `agencyID` (not `agencyId`)
- The `script` table uses `name` for the script label, not `scriptName`
- `section` table uses `scriptId` as the foreign key
- Variables passed to the view are consistently named `scripts`, `sections`, and `selectedScriptId` for clarity

---

#### 🧱 Outcome:
This module is now a fully testable and self-contained feature that will plug directly into the script-builder flow. It can also be reused for administrative tools, QA previews, or advanced filtering logic in later stages.

---






