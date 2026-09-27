# Ex-libris: plan técnico

> **Paso 6 — elección de stack y plan técnico.** Este documento traduce la
> especificación funcional (`docs/04-spec.md`) en decisiones tecnológicas
> concretas: qué herramientas, cómo se organizan, dónde viven los secretos,
> cuánto cuesta y qué partes van a doler.
>
> Precedido por: `docs/00-problema.md`, `docs/01-historias.md`,
> `docs/03-legal.md` y `docs/04-spec.md`.

---

## 1. TRES OPCIONES DE STACK

### Opción A — Next.js + Supabase → Vercel

| Capa | Tecnología |
|---|---|
| Frontend + SSR | Next.js 15 (App Router) + React + TypeScript |
| Base de datos | PostgreSQL en Supabase (región Frankfurt, UE) |
| Autenticación | Supabase Auth (email + contraseña, Google OAuth, Apple OAuth) |
| Recomendador | pgvector (extensión de Postgres, similitud coseno por SQL) |
| Almacenamiento | Supabase Storage (imágenes de portadas) |
| Despliegue | Vercel (integración con GitHub) |
| Notificaciones | Web Push nativo (VAPID) + FCM como fallback Android |
| PWA | next-pwa (service worker, instalación en pantalla de inicio) |

### Opción B — PocketBase → Railway

| Capa | Tecnología |
|---|---|
| Backend completo | PocketBase (Go, un solo binario con auth + BD + API REST) |
| Base de datos | SQLite embebido |
| Frontend | React + Vite como PWA (htmx opcional para menos JS) |
| Recomendador | Embeddings precalculados + búsqueda por similitud local |
| Despliegue | Railway (container Docker) o VPS propio |
| Notificaciones | Web Push nativo |

### Opción C — Next.js + Firebase → Vercel

| Capa | Tecnología |
|---|---|
| Frontend + SSR | Next.js 15 (App Router) + React + TypeScript |
| Base de datos | Firestore (NoSQL, Google) |
| Autenticación | Firebase Auth (email + Google + Apple) |
| Recomendador | Cloud Function (Python) + Pinecone (vector store externo) |
| Almacenamiento | Firebase Storage |
| Despliegue | Vercel (integración con GitHub) |
| Notificaciones | Firebase Cloud Messaging (FCM) |

### Tabla comparativa

| Criterio | A: Supabase + Next.js | B: PocketBase | C: Firebase + Next.js |
|---|---|---|---|
| **Modelo de datos** | Relacional (10+ entidades con FK) — encaja perfecto | Relacional (SQLite) — encaja bien | NoSQL (Firestore) — obliga a desnormalizar |
| **Recomendador vectorial** | pgvector incluido, consulta SQL nativa | Embeddings precalculados, búsqueda local | Cloud Function + Pinecone externo |
| **Datos en la UE (RV-9)** | ✅ Región Frankfurt configurable | ✅ Tú eliges servidor | ⚠️ No garantizado por defecto |
| **Row-Level Security** | ✅ Nativo en Supabase | Manual (registros de dueño) | Reglas de seguridad Firestore |
| **Aislamiento entre perfiles (RN-23)** | ✅ RLS por `reader_profile_id` | ✅ Política manual | ✅ Regla por `request.auth.uid` |
| **Curva de aprendizaje** | Media — muchos tutoriales, ecosistema grande | Baja — un solo binario, todo "en casa" | Media-Alta — NoSQL + Cloud Functions distintos |
| **Notificaciones push** | Web Push + FCM | Web Push | FCM nativo (más fácil) |
| **Coste a 500 usuarias** | ~0-5 €/mes | ~5 €/mes (Railway) o ~0 € (VPS) | ~0-10 €/mes (Cloud Functions) |
| **Coste a 5.000 usuarias** | ~25 €/mes | ~20 €/mes | ~30-50 €/mes |
| **Cuándo algo se rompe y estás sola** | Supabase tiene logs claros y dashboard web. La comunidad es enorme y cualquier error tiene respuesta en Stack Overflow. Con `vercel logs` ves el error del frontend al instante. | Todo vive en un proceso: `journalctl -u pocketbase` o `docker logs` te dicen qué pasa. No hay dashboard bonito, pero es un solo lugar donde mirar. Si SQLite se corrompe (raro), restauras el backup automático. | Firestore y Cloud Functions son "cajas negras". Depurar un error de permisos en NoSQL es como buscar una aguja en un pajar. Los logs de Cloud Functions tardan en aparecer y el plan gratuito los limita a 24 h. |

---

## 2. RECOMENDACIÓN

**Opción A: Next.js + Supabase → Vercel.**

Razones:

1. **Modelo relacional nativo.** La spec define 10+ entidades con relaciones claras (cuenta → perfiles → libros → progreso → reseñas). PostgreSQL está hecho para esto; Firestore te obliga a desnormalizar y replicar datos.

2. **pgvector incluido.** El recomendador por parecido vectorial (regla de negocio 6) se resuelve con una consulta SQL: `SELECT * FROM books ORDER BY embedding <=> query_embedding LIMIT 10`. Sin depender de servicios externos.

3. **Row-Level Security (RLS).** Supabase protege los datos a nivel de fila con políticas SQL. El aislamiento entre perfiles (RN-23) y la separación de datos de menores salen gratis: si la política no lo permite, la BD lo bloquea.

4. **Datos en la UE configurable.** Supabase permite elegir región (Frankfurt). Cumple RV-9 sin esfuerzo.

5. **Ecosistema Vercel.** Next.js tiene el mejor soporte en Vercel: SSR, Edge Functions, deploy automático desde git. Aprendes un ecosistema, no tres.

6. **Referencia existente.** El repo `desfasado-ex-libris` ya usaba Supabase + React. Su esquema de base de datos y lógica sirven de inspiración para no empezar de cero.

7. **Escala sin dolor.** Cuando lleguen miles de usuarias, Supabase escala con read replicas y Supavisor (pooler de conexiones). No necesitas cambiar de stack.

---

## 3. PLAN TÉCNICO

### 3.1 Estructura de carpetas

```
ex-libris/
├── .gitignore
├── .env.example              # Variables de entorno de ejemplo (sin valores reales)
├── package.json
├── next.config.ts
├── tsconfig.json
├── tailwind.config.ts
├── supabase/
│   ├── config.toml           # Configuración del proyecto Supabase
│   ├── migrations/           # Migraciones SQL versionadas
│   │   ├── 001_initial_schema.sql
│   │   ├── 002_rls_policies.sql
│   │   ├── 003_seed_catalog.sql
│   │   └── 004_recommender_setup.sql
│   └── seed.sql              # Datos iniciales (curadora, libros ejemplo)
├── src/
│   ├── app/                  # Next.js App Router
│   │   ├── (auth)/           # Grupo: autenticación (sin layout principal)
│   │   │   ├── login/page.tsx
│   │   │   ├── register/page.tsx
│   │   │   ├── forgot-password/page.tsx
│   │   │   └── reset-password/page.tsx
│   │   ├── (onboarding)/     # Grupo: primera vez
│   │   │   └── questionnaire/page.tsx
│   │   ├── (main)/           # Grupo: app principal (con layout)
│   │   │   ├── layout.tsx    # Layout con navegación inferior (móvil)
│   │   │   ├── page.tsx      # Dashboard: libro actual + progreso + racha
│   │   │   ├── streak/page.tsx       # Pantalla de racha
│   │   │   └── history/page.tsx      # Historial de libros
│   │   ├── (book)/           # Grupo: libros
│   │   │   ├── recommendations/page.tsx  # Lista de recomendaciones
│   │   │   ├── book/[id]/page.tsx        # Ficha del libro
│   │   │   └── book/[id]/review/page.tsx # Reseña post-lectura
│   │   ├── settings/         # Ajustes
│   │   │   ├── page.tsx               # Ajustes generales
│   │   │   ├── profile/page.tsx        # Editar gustos (cuestionario)
│   │   │   ├── minors/page.tsx         # Gestión de perfiles de menores
│   │   │   ├── privacy/page.tsx        # Privacidad y datos
│   │   │   └── legal/page.tsx          # Política de privacidad y términos
│   │   ├── curator/          # Panel de curadora (protegido por rol)
│   │   │   ├── layout.tsx
│   │   │   ├── page.tsx               # Dashboard de curación
│   │   │   ├── books/page.tsx          # CRUD de libros
│   │   │   └── balance/page.tsx        # Reparto catálogo por nivel/género
│   │   ├── api/               # API routes (Server Actions alternativas)
│   │   │   ├── push/subscribe/route.ts   # Suscribirse a push notifications
│   │   │   └── push/send/route.ts        # Enviar recordatorio (Cron)
│   │   └── share/[id]/page.tsx          # Página pública para compartir logro
│   ├── lib/                   # Utilidades y servicios
│   │   ├── supabase/
│   │   │   ├── client.ts      # Cliente Supabase (lado cliente)
│   │   │   ├── server.ts      # Cliente Supabase (lado servidor)
│   │   │   ├── middleware.ts   # Middleware de autenticación
│   │   │   └── types.ts        # Tipos generados desde la BD
│   │   ├── recommender/
│   │   │   ├── vector.ts      # Consultas pgvector
│   │   │   └── affinity.ts    # Cálculo de afinidad con gustos
│   │   ├── streak/
│   │   │   └── calculator.ts  # Lógica de racha (días, mejor racha, reanudación)
│   │   ├── notifications/
│   │   │   ├── push.ts        # Web Push (VAPID)
│   │   │   └── schedule.ts    # Programación de recordatorios
│   │   └── utils/
│   │       ├── validation.ts  # Validaciones de formularios
│   │       ├── timezone.ts    # Gestión de zona horaria y corte de día
│   │       └── date.ts        # Utilidades de fecha
│   ├── components/            # Componentes React reutilizables
│   │   ├── ui/                # Componentes base (botones, inputs, cards)
│   │   ├── layout/            # Layouts (nav inferior, header)
│   │   ├── auth/              # Formularios de auth
│   │   ├── book/              # Ficha de libro, lista de recomendaciones
│   │   ├── progress/          # Barra de progreso, racha visual
│   │   ├── review/            # Emociones, estrellas, texto
│   │   ├── onboarding/        # Cuestionario de gustos
│   │   ├── curator/           # Panel de curadora
│   │   └── settings/          # Formularios de ajustes
│   ├── hooks/                 # Custom hooks
│   │   ├── useAuth.ts
│   │   ├── useStreak.ts
│   │   ├── useBookProgress.ts
│   │   └── useRecommendations.ts
│   ├── styles/
│   │   └── globals.css        # Tailwind + variables de diseño
│   ├── middleware.ts           # Next.js middleware (redirects de auth)
│   └── types/                  # Tipos TypeScript globales
│       ├── user.ts
│       ├── book.ts
│       ├── progress.ts
│       └── review.ts
├── public/                    # Assets estáticos
│   ├── manifest.json          # PWA manifest
│   ├── sw.js                  # Service worker (generado por next-pwa)
│   └── icons/                 # Iconos de la app
├── prompts/                   # System prompts (Paso 11)
│   └── system.md
├── evals/                     # Evals de IA (Paso 14)
│   ├── casos-dificiles.md
│   └── historial.md
├── seguridad/                 # Seguridad (Paso 15-16)
│   └── red-team.md
├── docs/                      # Documentación del método
└── README.md
```

### 3.2 Modelo de datos

**Glosario técnico:**
- **Tabla:** una "hoja de cálculo" dentro de la base de datos. Cada fila es un registro, cada columna un campo.
- **Clave primaria (PK):** el identificador único de cada fila. Como el DNI de una persona.
- **Clave foránea (FK):** un campo que apunta a la PK de otra tabla. Es como decir "este libro pertenece a esta usuaria".
- **Row-Level Security (RLS):** política que filtra qué filas puede ver o editar cada usuaria. Si la política dice "solo tus datos", la base de datos bloquea el acceso al resto.

#### Tablas principales

```sql
-- CUENTA ADULTA
CREATE TABLE accounts (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email           TEXT UNIQUE NOT NULL,
    auth_provider   TEXT NOT NULL DEFAULT 'email',  -- 'email', 'google', 'apple'
    auth_provider_id TEXT,                          -- ID externo de Google/Apple
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now(),
    status          TEXT NOT NULL DEFAULT 'active', -- 'active', 'pending_deletion'
    deletion_requested_at TIMESTAMPTZ,
    consent_privacy_accepted BOOLEAN DEFAULT FALSE,
    consent_privacy_accepted_at TIMESTAMPTZ
);

-- PERFIL LECTOR (titular o menor)
CREATE TABLE reader_profiles (
    id                  UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    account_id          UUID NOT NULL REFERENCES accounts(id) ON DELETE CASCADE,
    name                TEXT NOT NULL,
    date_of_birth       DATE NOT NULL,
    profile_type        TEXT NOT NULL DEFAULT 'owner',  -- 'owner', 'minor'
    current_level       INT NOT NULL DEFAULT 1,         -- 1-10
    daily_goal_pages    NUMERIC(3,1) NOT NULL DEFAULT 1, -- 0.5 a 4
    reminder_time       TIME NOT NULL DEFAULT '21:00',
    notifications_enabled BOOLEAN DEFAULT FALSE,
    personalization_enabled BOOLEAN DEFAULT TRUE,
    preferred_language  TEXT NOT NULL DEFAULT 'es',
    created_at          TIMESTAMPTZ NOT NULL DEFAULT now(),
    CONSTRAINT valid_level CHECK (current_level BETWEEN 1 AND 10),
    CONSTRAINT valid_goal CHECK (daily_goal_pages BETWEEN 0.5 AND 4)
);

-- RESPUESTAS DEL CUESTIONARIO
CREATE TABLE questionnaire_responses (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    profile_id      UUID NOT NULL UNIQUE REFERENCES reader_profiles(id) ON DELETE CASCADE,
    question_1      TEXT,       -- Cine
    question_2      TEXT,       -- Música
    question_3      TEXT,       -- Series
    question_4      TEXT,       -- Personalidad
    question_5      TEXT,       -- Energía
    question_6      TEXT,       -- Momento del día
    question_7      TEXT,       -- Texto libre
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT now()
);

-- LIBRO (catálogo)
CREATE TABLE books (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    title           TEXT NOT NULL,
    author          TEXT NOT NULL,
    language        TEXT NOT NULL DEFAULT 'es',  -- es, ca, gl, eu
    pages           INT NOT NULL,
    genres          TEXT[] NOT NULL DEFAULT '{}',
    synopsis        TEXT NOT NULL DEFAULT '',
    hook_fragment   TEXT NOT NULL DEFAULT '',
    positive_review TEXT NOT NULL DEFAULT '',
    assigned_level  INT NOT NULL,
    status          TEXT NOT NULL DEFAULT 'active',  -- 'active', 'retired'
    embedding       VECTOR(384),  -- Embedding de pgvector para recomendaciones
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT now(),
    CONSTRAINT valid_level CHECK (assigned_level BETWEEN 1 AND 10)
);

-- LIBRO EN LA ESTANTERÍA (relación perfil-libro)
CREATE TABLE shelf_books (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    profile_id      UUID NOT NULL REFERENCES reader_profiles(id) ON DELETE CASCADE,
    book_id         UUID NOT NULL REFERENCES books(id) ON DELETE CASCADE,
    status          TEXT NOT NULL DEFAULT 'recommended',
        -- 'recommended', 'in_progress', 'pending' (descartado), 'completed'
    started_at      TIMESTAMPTZ,
    finished_at     TIMESTAMPTZ,
    current_page    INT NOT NULL DEFAULT 0,
    final_rating    INT,          -- 1-5 estrellas
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT now(),
    UNIQUE(profile_id, book_id),
    CONSTRAINT valid_rating CHECK (final_rating BETWEEN 1 AND 5)
);

-- REGISTRO DE PROGRESO
CREATE TABLE progress_logs (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    profile_id      UUID NOT NULL REFERENCES reader_profiles(id) ON DELETE CASCADE,
    shelf_book_id   UUID NOT NULL REFERENCES shelf_books(id) ON DELETE CASCADE,
    log_date        DATE NOT NULL,
    log_type        TEXT NOT NULL DEFAULT 'page',  -- 'page', 'micro'
    page_reached    INT,          -- NULL si es micro-registro
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now(),
    UNIQUE(profile_id, log_date, shelf_book_id)
);

-- RACHA
CREATE TABLE streaks (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    profile_id      UUID NOT NULL UNIQUE REFERENCES reader_profiles(id) ON DELETE CASCADE,
    current_streak  INT NOT NULL DEFAULT 0,
    best_streak     INT NOT NULL DEFAULT 0,
    last_active_date DATE,
    interrupted     BOOLEAN DEFAULT FALSE,
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT now()
);

-- RESEÑA
CREATE TABLE reviews (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    profile_id      UUID NOT NULL REFERENCES reader_profiles(id) ON DELETE CASCADE,
    shelf_book_id   UUID NOT NULL REFERENCES shelf_books(id) ON DELETE CASCADE,
    emotions        TEXT[] NOT NULL DEFAULT '{}',  -- 3 emociones
    stars           INT NOT NULL,
    text            TEXT DEFAULT '',
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now(),
    anonymized      BOOLEAN DEFAULT FALSE,
    CONSTRAINT valid_stars CHECK (stars BETWEEN 1 AND 5),
    CONSTRAINT valid_emotions CHECK (array_length(emotions, 1) <= 3)
);

-- META DE LECTURA (redundante con reader_profiles pero separada para historial)
CREATE TABLE reading_goals (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    profile_id      UUID NOT NULL REFERENCES reader_profiles(id) ON DELETE CASCADE,
    pages_per_day   NUMERIC(3,1) NOT NULL,
    reminder_time   TIME NOT NULL,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now()
);

-- TÍTULOS DE LECTOR
CREATE TABLE reader_titles (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    profile_id      UUID NOT NULL REFERENCES reader_profiles(id) ON DELETE CASCADE,
    title_key       TEXT NOT NULL,  -- 'punto_y_aparte', 'tercera_orilla', etc.
    earned_at       TIMESTAMPTZ NOT NULL DEFAULT now()
);

-- HISTORIAL DE HITOS Y CONSENTIMIENTOS
CREATE TABLE milestones_log (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    profile_id      UUID NOT NULL REFERENCES reader_profiles(id) ON DELETE CASCADE,
    milestone_type  TEXT NOT NULL,  -- 'book_completed', 'title_earned', 'consent_given', 'notification_permitted'
    milestone_data  JSONB,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT now()
);
```

#### Relaciones

```
accounts (1) ────< reader_profiles (N)
  │                     │
  │                     ├──< questionnaire_responses (1:1)
  │                     ├──< shelf_books (N) ────> books (N)
  │                     ├──< progress_logs (N)
  │                     ├──< streaks (1:1)
  │                     ├──< reviews (N)
  │                     ├──< reader_titles (N)
  │                     └───< milestones_log (N)
  │
  └── curator_profiles (N)  (tabla separada para el rol de curadora)
```

### 3.3 Gestión de usuarios y permisos

**Supabase Auth + Row-Level Security (RLS).**

#### Roles y acceso

| Rol | Cómo se asigna | Qué puede hacer |
|---|---|---|
| **Usuaria (titular)** | Registro por email o OAuth | Ver/editar su perfil, cuestionario, progreso, reseñas, ajustes, perfiles de menores |
| **Perfil de menor** | Creado por la titular desde ajustes | Leer libros, registrar progreso, reseñar — todo aislado del perfil de la titular |
| **Curadora interna** | Asignada manualmente en la tabla `curator_profiles` | CRUD del catálogo, consulta de reparto por nivel/género. No ve datos de usuarias |
| **Admin (tú)** | Acceso directo al dashboard de Supabase | Todo, incluyendo borrar datos de usuarias que pidan supresión (RV-5) |

#### Políticas RLS (ejemplos clave)

```sql
-- Cada perfil solo ve sus propios datos de progreso
CREATE POLICY "profiles_see_own_progress"
    ON progress_logs FOR ALL
    USING (profile_id = auth.uid()::uuid  -- simplificado: en la práctica se usa un claim del JWT
           OR profile_id IN (
               SELECT id FROM reader_profiles
               WHERE account_id = (SELECT account_id FROM reader_profiles WHERE id = auth.uid())
           ));

-- La curadora solo puede editar libros, nunca perfiles de usuarias
CREATE POLICY "curator_manage_books"
    ON books FOR ALL
    USING (auth.uid() IN (SELECT user_id FROM curator_profiles));

-- Las reseñas anonimizadas son visibles públicamente; las demás, solo de su perfil
CREATE POLICY "reviews_visibility"
    ON reviews FOR SELECT
    USING (anonymized = TRUE OR profile_id = auth.uid());
```

**Nota importante:** En la práctica, Supabase usa el JWT (token de autenticación) para identificar a la usuaria. Las políticas RLS se escriben con `auth.uid()` que devuelve el UUID del usuario autenticado. Para el caso de perfiles de menores (que no tienen auth propio), la política verifica que el perfil pertenezca a la misma `account_id` que el perfil titular.

### 3.4 Recomendador vectorial

**Fase 1 (inmediata): pgvector directo.**

El recomendador funciona así:

1. **Cada libro tiene un embedding** (vector de 384 dimensiones) precalculado. Se genera una vez al dar de alta el libro en el catálogo, usando un modelo de embeddings como `all-MiniLM-L6-v2` (gratuito, funciona offline).
2. **Cada perfil tiene un "embedding de gustos"** calculado a partir de sus respuestas al cuestionario y sus reseñas previas.
3. **La recomendación es una consulta SQL:**

```sql
SELECT id, title, author, assigned_level,
       1 - (embedding <=> profile_embedding) AS similarity
FROM books
WHERE status = 'active'
  AND assigned_level <= current_level
  AND id NOT IN (SELECT book_id FROM shelf_books WHERE profile_id = $1 AND status IN ('completed', 'pending'))
ORDER BY embedding <=> $2  -- $2 = embedding del perfil
LIMIT 10;
```

4. **Reparto equilibrado:** se post-filtra para que haya al menos un libro de cada género presente en los gustos del perfil.

**Fase 2 (post-lanzamiento): Agente RAG como complemento.**

Cuando haya datos reales de qué libros terminan las usuarias:

1. Se crea un agente de IA (n8n o backend propio) con un RAG sobre el catálogo.
2. El agente analiza patrones reales de finalización, abandonos y reseñas.
3. El agente **no reemplaza** pgvector: genera un "boost" por libro (un multiplicador de afinidad) que se aplica como peso extra en la consulta SQL.
4. El LLM solo se invoca cuando la usuaria lleva un rato sin leer y necesita un "reinicio de motivación" con una recomendación más contextual.

**Por qué no empezar con el RAG:** la spec exige que las recomendaciones aparezcan en **menos de 1 segundo** (sección 7.2). Un LLM tarda 2-5 segundos en responder. Además, cada llamada cuesta dinero: a 500 usuarias activas, 2 recomendaciones/día cada una = 1.000 llamadas × ~0,01 € = **10 €/día solo en recomendaciones**. Con pgvector: **0 €**.

### 3.5 Cómo se publica

```
Desarrollo local → Git push → Vercel → Producción
```

1. **Local:** `npm run dev` en tu máquina. Supabase local con `supabase start` (Docker).
2. **Git:** cada commit a `main` dispara un deploy automático en Vercel.
3. **Vercel:** compila Next.js, inyecta las variables de entorno, publica. La preview URL se genera por cada pull request.
4. **Dominio propio:** se configura en Vercel (`ex-libris.app` o similar) con HTTPS automático.
5. **Base de datos:** las migraciones se aplican con `supabase db push` desde tu máquina o desde un GitHub Action.
6. **PWA:** la usuaria añade la app a su pantalla de inicio desde el navegador. Funciona como una app nativa: icono propio, pantalla completa, notificaciones push.

---

## 4. GESTIÓN DE SECRETOS

**Regla de oro:** nunca, jamás, una clave real dentro del código ni en git. Ni siquiera en un commit que planees borrar después.

### Dónde vive cada secreto

| Secreto | Dónde se configura | Archivo local (no en git) |
|---|---|---|
| `NEXT_PUBLIC_SUPABASE_URL` | Vercel → Settings → Environment Variables | `.env.local` |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Vercel → Settings → Environment Variables | `.env.local` |
| `SUPABASE_SERVICE_ROLE_KEY` | Vercel → Settings → Environment Variables | **Solo en Vercel**, nunca en `.env.local` |
| `DATABASE_URL` (Postgres) | Supabase → Settings → Database | `.env.local` |
| Google OAuth `CLIENT_SECRET` | Vercel → Settings → Environment Variables | `.env.local` |
| Apple OAuth `KEY_ID`, `TEAM_ID`, `PRIVATE_KEY` | Vercel → Settings → Environment Variables | `.env.local` |
| VAPID keys (Web Push) | Vercel → Settings → Environment Variables | `.env.local` |
| Supabase JWT secret | Supabase → Settings → API | **Solo en Supabase**, nunca en local |

### El `.env.example` (sí va a git, sin valores reales)

```env
# Supabase
NEXT_PUBLIC_SUPABASE_URL=https://tu-proyecto.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=tu-clave-anon-aqui
SUPABASE_SERVICE_ROLE_KEY=tu-service-role-key-aqui
DATABASE_URL=postgresql://usuario:password@db.tu-proyecto.supabase.co:5432/postgres

# OAuth
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
APPLE_KEY_ID=
APPLE_TEAM_ID=
APPLE_PRIVATE_KEY=

# Web Push
VAPID_PUBLIC_KEY=
VAPID_PRIVATE_KEY=
```

### Flujo seguro

```
1. Creas el proyecto en Supabase → copias las claves
2. Las pones en Vercel (Settings → Env Variables) → se inyectan en producción
3. Las pones en .env.local → se usan en desarrollo
4. .env.local está en .gitignore → nunca se sube a git
5. .env.example está en git → sirve de plantilla para saber qué variables hacen falta
```

---

## 5. COSTES ESTIMADOS

| Servicio | Plan | Coste mensual estimado |
|---|---|---|
| **Vercel** | Hobby (gratis) | **0 €** |
| **Supabase** | Free (500 MB DB, 1 GB storage, 50.000 MAU) | **0 €** |
| **Supabase** (si se supera free tier) | Pro | **25 $/mes** |
| **Dominio** (.app o .dev) | Registro anual | **~1-2 €/mes** (12-24 €/año) |
| **Modelo de embeddings** | all-MiniLM-L6-v2 (local, open-source) | **0 €** |
| **Total MVP / inicio** | | **~1-2 €/mes** (solo dominio) |
| **Total a 500 usuarias activas** | | **0-25 €/mes** |
| **Total a 5.000 usuarias activas** | | **25-50 €/mes** |

---

## 6. LO QUE VA A DOLER

### 1. El recomendador vectorial y su embedding

**Por qué duele:** Calcular embeddings (vectores numéricos que representan el "significado" de un texto) es un concepto nuevo. Entender qué modelo usar, cómo generar los vectores de cada libro y del perfil, y cómo funciona la similitud coseno requiere aprender algo de machine learning básico.

**Analogía:** Un embedding es como convertir un libro a un código GPS. En vez de comparar palabras una a una, comparas coordenadas: dos libros "cerca" en el espacio de embeddings son similares. La similitud coseno mide el ángulo entre dos coordenadas: cuanto menor, más parecidos.

**Mitigación:**
- Usar `all-MiniLM-L6-v2` de Sentence Transformers: es gratuito, funciona local y genera embeddings de 384 dimensiones.
- Generar los embeddings una sola vez al dar de alta cada libro (script Python local).
- La consulta SQL es una línea: `ORDER BY embedding <=> profile_embedding`.
- Fase 2 (RAG) cuando ya haya datos reales de lectura.

### 2. Row-Level Security (RLS) y el aislamiento entre perfiles

**Por qué duele:** Si una política RLS está mal escrita, o bien las usuarias ven datos de otras (catastrófico legalmente) o bien no ven sus propios datos (app rota). Los perfiles de menores complican el modelo porque no tienen auth propio: comparten la cuenta de la adulta.

**Analogía:** RLS es como poner un cristal esmerilado entre las oficinas de un edificio. Cada persona ve solo su despacho. Si el cristal está mal instalado, se ven los papeles de la vecina. Si es demasiado opaco, ni tú ves los tuyos.

**Mitigación:**
- Escribir las políticas una a una, no todas de golpe.
- Probar cada política con el SQL Editor de Supabase: `SET ROLE authenticated; SELECT * FROM progress_logs;` — debería devolver solo tus registros.
- Usar claims personalizados en el JWT de Supabase para incluir el `account_id` y poder verificar perfiles de menores sin auth separada.
- Añadir tests automatizados (Paso 13) que verifiquen que una usuaria NO puede acceder a los datos de otra.

### 3. Notificaciones push en PWA, especialmente en iOS

**Por qué duele:** Safari en iOS añadió soporte para Web Push en iOS 16.4, pero tiene limitaciones: la usuaria debe añadir la app a la pantalla de inicio primero, los permisos son caprichosos, y si no los concede, no hay recordatorio. Sin notificaciones, la racha se pierde — y la racha es el corazón del producto.

**Analogía:** Las notificaciones push son como llamar al timbre de alguien. Si te abre la puerta (da permiso), le entregas el mensaje. Si no, tienes que dejar una nota en el buzón (notificación dentro de la app) y esperar a que pase a recogerla.

**Mitigación:**
- **MVP:** notificaciones dentro de la app. Un badge en el icono de la pantalla principal ("¡Hoy no has leído!") y un recordatorio visible al abrir la app.
- **Push real como mejora post-MVP:** Web Push con VAPID para Android y desktop. En iOS, guiar a la usuaria a añadir a pantalla de inicio para habilitar push.
- Nunca enviar notificaciones fuera de la hora configurada por la usuaria (RN-18).
- Si la usuaria desactiva la personalización (RN-22), se respetan las notificaciones mínimas de racha pero no las de recomendaciones.

---

## 7. SIGUIENTE PASO

El Paso 7 decide qué papel juega la IA en el producto. Con este plan técnico, la IA aparece en dos momentos:

1. **Recomendador (Fase 2):** agente RAG que complementa pgvector con análisis de patrones reales.
2. **System prompt (Paso 11):** la IA no conversa con la usuaria (no es un chat), pero puede haber un system prompt para generar el fragmento gancho de los libros o ayudar a la curadora con sugerencias de nivel.

El Paso 8 trocea este plan en tareas pequeñas y verificables.
