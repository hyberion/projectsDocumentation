# 🧘 Path: Stoic / Path: Tao - Project Writeup

A modular mobile-first app platform for personal growth and philosophical practice. Users choose a "path" (e.g., Stoicism or Taoism) and engage with curated content, journaling, quotes, lessons, and reflections designed to support daily mindfulness and deeper philosophical understanding.

----------

## 🧑‍🤝‍🧑 User Roles

### 1. **User**

-   Browse daily quotes
-   Respond to journal prompts
-   View lessons and core concepts
-   Customize interface and settings
-   Set daily reminders or nudges

### 2. **Admin**

-   Manage content (quotes, prompts, lessons, categories)
-   Upload audio versions of texts
-   Schedule quote/prompt rotation
-   Moderate flagged journal entries (if applicable)

----------

## 🧱 Core Architecture

-   **Shared Codebase** for both Stoic and Taoist apps
    -   Shared backend (auth, database, content modules)
    -   Modular frontends with separate skins/branding per path
    -   Each path can be built/deployed as its own app (App Store/Google Play)
-   **Web App**
    -   Accessible standalone
    -   Can default to single-path view or offer multi-path access
    -   Easier for initial testing/iteration

----------

## 📦 Tech Stack

### Frontend:

-   **React Native (with Expo)** for iOS & Android apps
-   **React (Next.js or Vite)** for Web app
-   **Tailwind CSS** for styling (web only)

### Backend:

-   **Supabase** (preferred)
    -   Auth (magic link, Google, Apple)
    -   Postgres DB
    -   Edge functions (for reminders, journaling triggers)
-   **Firebase** (alt option)

### Deployment:

-   **App Store & Google Play**
    -   Expo Managed Workflow
    -   EAS Build + OTA updates
-   **Web**: Vercel, Netlify, or self-hosted (Docker, DigitalOcean droplet)

### Optional Add-ons:

-   **ElevenLabs API** for realistic voice readings of public domain texts
-   **Synesthesia / D-ID / HeyGen** for AI avatar lectures (optional YouTube channel content)
-   **Notion or Markdown-based CMS layer** (for admin content editing)

----------

## 📚 Core Features

### 🔹 Content Modules

-   Quote of the Day (with interpretation)
-   Daily Journal Prompt
-   Philosophical Lessons (bite-sized concepts)
-   Text Library (Tao Te Ching, Meditations, etc.)
-   Audio Versions of Key Texts

### 🔹 Interaction Tools

-   Journaling interface (with history)
-   Quote bookmarking
-   Daily reflection streak tracker
-   UI customization (themes, font size, tone)
-   Email reminder integration
-   Optional reply-to-email journaling (future dev)

### 🔹 Admin Tools

-   Content dashboard to add/edit:
    -   Quotes (path-tagged)
    -   Prompts
    -   Lessons & text entries
    -   Audio content links
-   Schedule push notifications/email nudges

----------

## 🧪 MVP Feature List

1.  User login / onboarding
2.  Select path (Stoic or Tao)
3.  Daily quote + journal prompt
4.  Basic journal entry saving
5.  10+ core concepts per path (with lesson pages)
6.  Web CMS for admin to upload/edit prompts, quotes, etc.
7.  Web App + iOS + Android builds

----------

## 🔔 Notifications / Reminders

-   Daily mobile push notification with journal prompt
-   Optional daily email with quote and reply-to-journal feature (v2)
-   User-customizable notification time (e.g., morning/evening)

----------

## 🌐 Hosting / Server Setup

-   **Supabase** project for database/auth/content
-   **Vercel or Netlify** for web
-   **Expo** for mobile apps
-   Optional Docker container for local dev/testing

----------

## 🧭 Dev Roadmap

### Phase 1: Infrastructure & Content (Weeks 1–3)

-   Set up Supabase project
-   Build shared DB schema
-   Build basic React Native + Web scaffold
-   Load seed quotes and concepts for both paths

### Phase 2: MVP (Weeks 4–6)

-   Journal UI
-   Daily quote + prompt rotation logic
-   Admin dashboard (basic)
-   Push notification scheduling
-   Submit builds to App Store & Play Store (alpha)

### Phase 3: Feature Polish (Weeks 7–8)

-   Add theming/customization
-   Enable audio readings
-   Smooth out CMS interface
-   Create landing page for app

### Phase 4: Optional Enhancements

-   Synesthesia integration for lectures
-   Reply-to-email journaling
-   Add user streaks, social share, etc.
-   Expand content types (video, quizzes, AI insights)

----------

## 📝 Other Notes

-   All Stoic & Taoist source texts should be public domain (e.g., Tao Te Ching, Meditations)
-   Branding tone should match the path (minimal for Stoicism, organic/flowing for Taoism)
-   Project name could evolve ("Path"? "Way"? "Practice"? Flexible to name around core idea)
-   Could be released open source for transparency/philosophy alignment

----------

Let me know if you'd like:

-   Sample DB schema
-   Branding names/concepts
-   First wireframe idea or screen flow
-   AI avatar script prompts for YouTube-style lessons

## 📚 Conceptual Database Schema (Supabase/Postgres)

### Tables:

#### `users`

-   id (UUID, PK)
-   email
-   display_name
-   path_selected (enum: 'stoic', 'tao')
-   notification_time

#### `quotes`

-   id (PK)
-   content (text)
-   author
-   path (enum)
-   tags (array)
-   interpretation (text)

#### `prompts`

-   id
-   content
-   path
-   category (e.g. reflection, gratitude, awareness)

#### `journal_entries`

-   id
-   user_id (FK)
-   date
-   entry_text
-   linked_prompt_id (nullable)

#### `lessons`

-   id
-   title
-   body (markdown/text)
-   path
-   category
-   order

#### `texts`

-   id
-   title
-   section_number
-   content (text)
-   path
-   audio_url (optional)

#### `notifications`

-   id
-   user_id (FK)
-   scheduled_time
-   type (e.g. push, email)
-   status

#### `admin_users`

-   id (FK to users)
-   role (e.g., editor, superadmin)

#### `media_assets`

-   id
-   title
-   type (audio, video)
-   url
-   associated_lesson_id

----------

## 💎 Branding / Naming Concepts

**App Name Candidates:**

-   **Path** (clean, expandable)
-   **Way** (simple, Taoist-leaning)
-   **Daily Praxis**
-   **Practice**
-   **Echo** (a reflection/journaling metaphor)
-   **Stillness** / **Flow** (based on mode)

**Brand Family Approach:**

-   Path: Stoic
-   Path: Tao
-   Path: Zen (future)

----------

## 🎤 AI Avatar Script Prompts (YouTube-style Lectures)

> These can be delivered with ElevenLabs audio + Synesthesia avatar for visual appeal on YouTube.

### Sample Topics & Script Intros:

**"Introduction to Stoicism"**  
"Welcome. Today, we begin a journey into Stoicism — a philosophy of resilience, clarity, and virtue. We'll explore what it means to live in accordance with nature, and how to train our minds to find stillness even in chaos."

**"What is the Tao?"**  
"The Tao is the way — not a path to follow, but a current to join. In this lesson, we reflect on Wu Wei, the art of non-doing, and what it means to flow with life rather than force it."

**"Daily Stoic Journal Ritual"**  
"Before your day begins, take a breath. Ask: What is in my control today? What virtue can I cultivate? Let's take 90 seconds to reset."

**"Zhuangzi's Butterfly Dream"**  
"Am I a man who dreamed he was a butterfly, or a butterfly dreaming he is a man? What can this paradox teach us about the nature of identity, perception, and letting go?"

**"Seneca on Time"**  
"Seneca wrote: 'It is not that we have a short time to live, but that we waste much of it.' Today's reflection invites us to honor time by giving our attention to what matters."

----------

Let me know when you want to start sketching:

-   User onboarding flows
-   Admin CMS dashboard
-   Audio integration
-   Or wireframes for journal + quote UI

Let's keep it flowing!

> Written with [StackEdit](https://stackedit.io/).