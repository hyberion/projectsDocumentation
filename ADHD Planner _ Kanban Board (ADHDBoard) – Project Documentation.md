# 🧠 ADHD Planner / Kanban Board (ADHDBoard) – Project Documentation

## 📘 Overview

A neurodivergent-friendly productivity tool designed to reduce overwhelm and improve focus. Combines visual Kanban-style task management with a planner, routine scheduler, and habit-building tools. Designed for web first, with future expansion to desktop.

---

## 🧱 Core Features (Initial Scope)

### 📌 Task Board (Kanban/Scrum Style)

- Columns:
  - [ ] To Do
  - [ ] In Progress
  - [ ] Waiting
  - [ ] On Hold
  - [ ] Done
- Drag-and-drop task movement
- Tasks belong to:
  - [ ] A project (color-coded)
  - [ ] A single board state
  - [ ] One stage within a project
- Projects belong to a category
- Priority levels (color coded)
- Deadlines with reminders
- Pomodoro timer integration
- Action prompts to encourage progress
- Structure should be user selectable (Vertical | Horizontal)
- Default to vertical
- Task notes/comments
- Optional color coding for projects/stages/priorities

---

## 📆 Daily Routine & Scheduler (Planned)

A separate interface for managing time-based habits and self-care routines.

### 🔄 Routine Features

- Repeating tasks (daily, weekly, etc.)
- Timeline-style scheduling view
- Drag/drop adjustments
- One-time edits vs full-series edits
- Visual differentiation for self-care tasks

### 📅 Planner Mode

- Daily/weekly calendar views
- Integration with Kanban tasks (optional)
- Timeline interface or block scheduler

---

## 🧩 Technical Overview

- Platform: Web (initial), Desktop (future)
- Hosting: DigitalOcean
- Containerized: Docker (likely)
- Frontend: TBD (React, Vue, or other)
- Backend: TBD (Laravel, Node, etc.)

---

## 🔮 Future Features / Wishlist

- [ ] Gamification (streaks, rewards)
- [ ] “Focus mode” with simplified UI
- [ ] Distraction blocker or soft lockout timer
- [ ] Daily review / rescheduling helper
- [ ] AI-generated summaries or nudges
- [ ] Calendar sync (Google, iCal, etc.)
- [ ] Export to CSV / JSON
- [ ] Mobile app (post-launch)

---

## 🎯 Design Philosophy

- Designed with ADHD and executive dysfunction in mind
- Minimize cognitive load
- Clear visual organization (color, motion, category)
- Low-friction entry points for new thoughts/tasks
- Flexible enough for chaos, structured enough for routine
- Extendable either through future development or an open framework to allow others to add functions/capeabilies.

---

## 🗓️ Development Status

- [ ] Planning
- [ ] Wireframes
- [ ] Database schema
- [ ] Task system prototype
- [ ] Routine scheduler prototype
- [ ] MVP build

## Technology Stack

- Tailwind with flexbox to govern layout/responsiveness.
- Laravel
- PostgreSQL for back end database
- Redis or laravel Octana (sesson and caching)
- Websocket for real-time updates (Laravel Echo)
- Pusher (or Socketi) for live board updates.
- API resources for lean structured data to front end.
- Lazy Loading and client-side hydration for heavy UI's
- Combine localStorage and backend sync for user preferences/state storage/retrieval.
- Docker for containerization
- OpenAi GPT-4 Turbo
- Twilio- SMS and messaging/ user input
- Mailgun/Sendgrid- email parsing
- Laravel HTTP Client _call the openAI API
- Laravel Sancturm - To authenticae API calls
- Job Queue- process input sync.

## Hosting Environment

📦 Managed PostgreSQL (DigitalOcean DB)
📦 Managed Redis (optional) OR Redis container in main app
🌐 Nginx + Laravel App (Docker container)
📮 Laravel Queue Worker (separate Docker container)
🧠 AI Processing Worker (optional container/service)
📶 Laravel Horizon (optional, for managing queues)
🔁 Load Balancer (DO-native or Traefik/HAProxy)
🔐 HTTPS via Certbot (if self-hosted Nginx)

---

```markdown
## 🤖 AI Integration

The ADHD Planner will include **natural language task input** via AI to reduce friction and support neurodivergent workflows. This feature enables users to create, update, or manage tasks using plain language — via the app, SMS, or email.

### 🧠 Purpose

Instead of navigating UI manually, users can type or say things like:
- “Add ‘Buy dog food’ to Home project, due Saturday”
- “Move ‘Finish report’ to Done”
- “Remind me to stretch every day at 9am”

This reduces cognitive load and makes the tool feel more like an executive assistant than a traditional planner.

---

### 🔁 Use Cases

- **Smart Add Input (Web)**: A textbox that accepts natural task descriptions and creates structured tasks automatically.
- **Inbox via SMS or Email**: Send a text or email and have it create tasks in the user’s account (using NLP + backend API).
- **Recurring Task Suggestions**: AI helps structure repeating tasks from natural phrases (“every Friday”).
- **Daily Summary Prompting** *(future)*: “What should I focus on today?” returns priority tasks, suggestions, or nudges.
- **Gentle prompts**: “You haven’t updated ‘Health’ in a while — want to add something?”

---

### ⚙️ Technical Architecture

#### 🔧 Input Methods
- **Web input field**
- **SMS integration** (via [Twilio](https://www.twilio.com/))
- **Email-to-task inbox** (via [Mailgun](https://www.mailgun.com/) or [SendGrid Inbound Parse](https://docs.sendgrid.com/for-developers/parsing-email/setting-up-the-inbound-parse-webhook))

All input is routed to the same processing endpoint:
```
POST /api/incoming-task
```

---

#### 🧠 OpenAI Integration (Function Calling)

The input is sent to **OpenAI GPT-4 Turbo** using [function calling](https://platform.openai.com/docs/guides/function-calling) to produce structured task data.

**Function Definition Example**:
```json
{
  "name": "createTask",
  "description": "Create a new task on the user's board",
  "parameters": {
    "type": "object",
    "properties": {
      "taskName": { "type": "string" },
      "project": { "type": "string" },
      "boardState": { "type": "string" },
      "dueDate": { "type": "string", "format": "date" },
      "priority": { "type": "string", "enum": ["low", "normal", "high"] }
    },
    "required": ["taskName"]
  }
}
```

**AI Output**:
```json
{
  "taskName": "Schedule car inspection",
  "project": "Life Admin",
  "boardState": "To Do",
  "dueDate": "2025-05-10",
  "priority": "normal"
}
```

---

#### 🧰 Laravel Backend Integration

##### 📥 Incoming Task Controller (example stub)
```php
// routes/api.php
Route::post('/incoming-task', [AIController::class, 'handleIncoming'])->middleware('auth:sanctum');

// app/Http/Controllers/AIController.php
public function handleIncoming(Request $request)
{
    $user = $request->user();
    $inputText = $request->input('input_text');

    // 1. Send text to OpenAI API with function calling
    $structured = app(OpenAIAssistant::class)->parseInput($inputText);

    // 2. Match or create related models
    $project = Project::firstOrCreate(['name' => $structured['project'], 'user_id' => $user->id]);
    $boardState = BoardState::where('name', $structured['boardState'])->first();

    // 3. Create the task
    $task = Task::create([
        'user_id' => $user->id,
        'project_id' => $project->id,
        'name' => $structured['taskName'],
        'due_date' => $structured['dueDate'],
        'board_state_id' => $boardState->id,
        'priority' => $structured['priority'],
    ]);

    return response()->json(['status' => 'success', 'task' => $task]);
}
```

---

### 📦 Stack Requirements (Additional)

| Component | Description |
|----------|-------------|
| **OpenAI GPT-4 Turbo** | For parsing user input into structured task data |
| **Twilio (optional)** | Receive SMS input from users |
| **Mailgun / SendGrid** | Inbound email parsing (create tasks via email) |
| **Laravel HTTP Client** | To call the OpenAI API |
| **Laravel Sanctum** | To authenticate API calls securely |
| **Job Queue** (optional) | For processing input async if needed later |

---

### ✅ Example Prompt

> “Add ‘Schedule therapy session’ to Health project in Waiting. Make it high priority and due next Thursday.”

**Result**:
- Project: Health
- Task: Schedule therapy session
- Priority: High
- State: Waiting
- Due: next Thursday

---

###Additonal Docker/Enviornment Setup
**Heck yes.** You’re thinking about this like a future-scaling pro — build it to run lean now, but smart enough to scale horizontally without re-architecting later.

Here’s a full-blown **production-ready hosting environment setup** using:

✅ Docker  
✅ DigitalOcean Droplet  
✅ Managed PostgreSQL  
✅ Load balancer and container scaling compatibility  
✅ Laravel queue support  
✅ Redis cache + session handling  
✅ AI/Webhook flexibility  
✅ Let’s Encrypt SSL  

---

## 🧱 PRODUCTION HOSTING ARCHITECTURE (Docker + DO)

```plaintext
📦 Managed PostgreSQL (DigitalOcean DB)
📦 Managed Redis (optional) OR Redis container in main app
🌐 Nginx + Laravel App (Docker container)
📮 Laravel Queue Worker (separate Docker container)
🧠 AI Processing Worker (optional container/service)
📶 Laravel Horizon (optional, for managing queues)
🔁 Load Balancer (DO-native or Traefik/HAProxy)
🔐 HTTPS via Certbot (if self-hosted Nginx)
```

---

## 🧰 Docker-Based Project Structure

### `docker-compose.yml` (simplified, scalable-ready)

```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: adhdplanner-app
    restart: unless-stopped
    env_file: .env
    ports:
      - "80:80"
    depends_on:
      - redis
    volumes:
      - .:/var/www/html
    networks:
      - backend

  queue:
    build:
      context: .
    container_name: adhdplanner-queue
    command: php artisan queue:work --tries=3 --timeout=60
    depends_on:
      - app
    env_file: .env
    volumes:
      - .:/var/www/html
    networks:
      - backend

  redis:
    image: redis:alpine
    container_name: adhdplanner-redis
    restart: unless-stopped
    networks:
      - backend

networks:
  backend:
    driver: bridge
```

---

### 🔐 `.env` (Key Configs)

```env
APP_ENV=production
APP_KEY=base64:...
APP_DEBUG=false
APP_URL=https://yourdomain.com

DB_CONNECTION=pgsql
DB_HOST=your-managed-db-do.internal
DB_PORT=25060
DB_DATABASE=adhd_planner
DB_USERNAME=yourusername
DB_PASSWORD=yourpassword

REDIS_HOST=redis
REDIS_PORT=6379

QUEUE_CONNECTION=redis
```

---

### 🛡️ Nginx Config (Inside App Container or Host)

```nginx
server {
    listen 80;
    server_name yourdomain.com;

    root /var/www/html/public;

    index index.php index.html;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass php:9000;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        fastcgi_param DOCUMENT_ROOT $realpath_root;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

## ☁️ DigitalOcean Setup

### 🖥️ Droplet
- **Size**: 2vCPU / 4–8GB RAM to start
- **OS**: Ubuntu 22.04 LTS
- **Docker / Docker Compose** installed
- **Swap file** enabled (for queue + caching headroom)

### 🗄️ Managed DB (PostgreSQL)
- Select “Managed Database” in DO
- Add your droplet’s private IP to the trusted sources
- Use connection string in your `.env`

### 🧠 Redis (Pick one)
- **Option A**: Let the Redis container run inside Docker (simpler, fast)
- **Option B**: Use DigitalOcean’s **Managed Redis** if you expect high session or queue load

---

## 🔄 Load Balancing + Scaling

### For Scaling App Containers
- Add more `app` containers under a common `docker-compose.scale.yml`
- Or split into swarm/k8s later (optional — not needed early)

### For Load Balancing
- Use DigitalOcean’s native Load Balancer (HTTPS termination + sticky sessions)
- OR use **Traefik**/HAProxy in front of your containers (more control)

---

## 🧠 Extra Considerations

| Component | Tool | Notes |
|----------|------|-------|
| HTTPS | Certbot (Let's Encrypt) | Inside Nginx or Traefik |
| Auto Deploy | GitHub Actions or Laravel Forge | Optional |
| Logging | Laravel Log → File or external | Use `docker logs` or mount volume |
| Health Checks | Laravel `up` route or `/health` | Useful for load balancer pings |
| Monitoring | Laravel Telescope (local) or Sentry/LogRocket | Optional |
| Mail | Mailgun/SendGrid | For password resets, task confirmations |
| AI API | OpenAI or local model | Called from Laravel worker queue |

---

## ✅ TL;DR: Final Stack Checklist

| Component | Where/How |
|----------|-----------|
| Laravel App | Docker container (Nginx + PHP-FPM) |
| Queue Worker | Separate container (`php artisan queue:work`) |
| Redis | Container or DO-managed |
| PostgreSQL | DO-managed |
| AI/API logic | Laravel services using OpenAI |
| Load Balancer | DO native LB or Traefik |
| HTTPS | Certbot or DO LB |
| Deployment | GitHub Actions or manual via SSH/Docker |
| Scaling | Add containers → connect to LB → done |

---