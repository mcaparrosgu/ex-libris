# Ex-libris: lista de tareas

> **Paso 8 — trocear la spec en tareas.** Cada tarea se implementa y se
> comprueba de forma aislada, en menos de una hora, y la comprobación la
> puedes hacer **tú**, sin saber programar.
>
> Fuentes: `docs/04-spec.md`, `docs/05-plan-tecnico.md`, `docs/06-ia.md`,
> `docs/03-legal.md` y `docs/bitacora.md`.
>
> **Cómo se usa este documento.** Una tarea por sesión. Antes de empezar:
> `/plan` sobre la tarea. Al terminar: marcar la casilla y `/clear`. Las
> obligaciones legales de `docs/03-legal.md` (RV-1 a RV-10) no son «una
> tarea de cumplir la ley»: cada una es su propia tarea, más abajo.

**Leyenda:** `[ ]` pendiente · `[x]` hecha.
**Qué NO está aquí:** el Paso 9 (terreno: carpetas, git, .gitignore,
`.env.example`, README) se ejecuta aparte, antes de empezar T01. El Hito 0 lo
lista solo para no perderlo de vista.

---

## HITO 0 · La app arranca y se ve en pantalla

**Al terminar verás:** una página con el nombre Ex-libris abierta en el
navegador del móvil y añadible a la pantalla de inicio.

- [ ] **T01 · Crear el proyecto Next.js con TypeScript y Tailwind**
  - **Archivos:** `package.json`, `next.config.ts`, `tsconfig.json`, `tailwind.config.ts`, `src/app/layout.tsx`, `src/app/page.tsx`
  - **Cómo lo compruebo:** escribo `npm run dev` en la terminal y abro `http://localhost:3000`; veo una página con el título «Ex-libris».
  - **Depende de:** —

- [ ] **T02 · Crear la estructura de carpetas del plan técnico**
  - **Archivos:** carpetas vacías de `src/app/(auth)`, `src/app/(main)`, `src/lib/supabase`, `src/components`, `supabase/migrations`
  - **Cómo lo compruebo:** abro la carpeta del proyecto y veo que existen las carpetas `src/app`, `src/lib`, `src/components` y `supabase/migrations`.
  - **Depende de:** T01

- [ ] **T03 · Preparar `.gitignore` y `.env.example` (sin valores reales)**
  - **Archivos:** `.gitignore`, `.env.example`
  - **Cómo lo compruebo:** escribo `git status`; NO aparece ningún archivo `.env.local`, y sí aparece `.env.example`. Abro `.env.example` y solo veo nombres de variables y textos de ejemplo, ninguna clave real.
  - **Depende de:** T02

- [ ] **T04 · Crear el proyecto de Supabase en región de la Unión Europea**
  - **Archivos:** `.env.local` (fuera de git), `supabase/config.toml`
  - **Cómo lo compruebo:** en el panel de Supabase, en *Settings → General*, la región dice Frankfurt (`eu-central-1`). *(RV-9.)*
  - **Depende de:** T03
  - **Aviso:** plan gratuito. Si se supera, Supabase Pro son 25 $/mes.

- [ ] **T05 · Levantar Supabase en local y comprobar la conexión**
  - **Archivos:** `supabase/config.toml`
  - **Cómo lo compruebo:** ejecuto `supabase start` y `supabase status`; veo la URL de la API y la de la base de datos sin error.
  - **Depende de:** T04

- [ ] **T06 · Añadir el manifest de PWA y los iconos**
  - **Archivos:** `public/manifest.json`, `public/icons/`, `src/app/layout.tsx`
  - **Cómo lo compruebo:** abro la app en Chrome del móvil, menú → «Añadir a pantalla de inicio»; aparece el icono de Ex-libris y al abrirlo se ve a pantalla completa.
  - **Depende de:** T01

---

## HITO 1 · La base de datos existe con mis tablas

**Al terminar verás:** en el panel de Supabase, las tablas del plan técnico
con filas de prueba y los permisos de aislamiento puestos.

- [ ] **T07 · Migración `001a`: cuentas y perfiles**
  - **Archivos:** `supabase/migrations/001_initial_schema.sql`
  - **Cómo lo compruebo:** en Supabase Studio (Table Editor) veo las tablas `accounts`, `reader_profiles` y `questionnaire_responses`, con sus columnas.
  - **Depende de:** T05

- [ ] **T08 · Migración `001b`: catálogo y estantería**
  - **Archivos:** `supabase/migrations/001_initial_schema.sql`
  - **Cómo lo compruebo:** veo las tablas `books`, `shelf_books` y `progress_logs`.
  - **Depende de:** T07

- [ ] **T09 · Migración `001c`: racha, reseñas y títulos**
  - **Archivos:** `supabase/migrations/001_initial_schema.sql`
  - **Cómo lo compruebo:** veo las tablas `streaks`, `reviews`, `reading_goals`, `reader_titles` y `milestones_log`.
  - **Depende de:** T07

- [ ] **T10 · Migración `002`: activar RLS y escribir las políticas clave**
  - **Archivos:** `supabase/migrations/002_rls_policies.sql`
  - **Cómo lo compruebo:** en Studio, cada tabla muestra el candado de «RLS enabled».
  - **Depende de:** T08, T09

- [ ] **T11 · Probar el aislamiento entre dos cuentas**
  - **Archivos:** `supabase/migrations/002_rls_policies.sql`
  - **Cómo lo compruebo:** con dos usuarias de prueba creadas, la segunda NO ve ningún dato de la primera. Si lo ve, la política está mal. *(Regla de negocio 23.)*
  - **Depende de:** T10

- [ ] **T12 · Migración `003`: sembrar el catálogo con 20-30 libros de nivel 1-3**
  - **Archivos:** `supabase/migrations/003_seed_catalog.sql`
  - **Cómo lo compruebo:** la tabla `books` tiene al menos 20 filas con título, autor, páginas y géneros rellenos.
  - **Depende de:** T08

- [ ] **T13 · Migración `004`: activar `pgvector` y preparar la tabla de boosts**
  - **Archivos:** `supabase/migrations/004_recommender_setup.sql`
  - **Cómo lo compruebo:** la tabla `books` tiene la columna `embedding` y existe la tabla vacía `book_boosts` (reservada para la Fase 2).
  - **Depende de:** T12

- [ ] **T14 · Crear la tabla de curadoras y la primera curadora**
  - **Archivos:** `supabase/migrations/001_initial_schema.sql`
  - **Cómo lo compruebo:** la tabla `curator_profiles` existe y tiene una fila con la curadora de prueba.
  - **Depende de:** T08

- [ ] **T15 · Script de Python que genera los embeddings de los libros**
  - **Archivos:** `scripts/generate_embeddings.py`
  - **Cómo lo compruebo:** lo ejecuto una vez y, en la tabla `books`, la columna `embedding` deja de estar vacía.
  - **Depende de:** T13
  - **Coste:** 0 €. El modelo `all-MiniLM-L6-v2` corre en tu máquina, ninguna sinopsis sale a internet. *(IA-1.)*

---

## HITO 2 · Puedo registrarme y entrar

**Al terminar verás:** una pantalla de registro y otra de acceso que
funcionan de verdad, y las páginas legales enlazadas.

- [ ] **T16 · Pantalla de registro con correo y contraseña**
  - **Archivos:** `src/app/(auth)/register/page.tsx`, `src/components/auth/`
  - **Cómo lo compruebo:** abro `/register`, relleno correo y contraseña y, al enviar, se crea la cuenta.
  - **Depende de:** T10

- [ ] **T17 · Validaciones del registro (contraseña ≥6, correo válido y no repetido)**
  - **Archivos:** `src/lib/utils/validation.ts`, `src/app/(auth)/register/page.tsx`
  - **Cómo lo compruebo:** pruebo tres casos: contraseña de 5 caracteres → aviso; correo sin arroba → aviso; correo ya usado → aviso. *(Reglas 2 y caso límite «datos inválidos».)*
  - **Depende de:** T16

- [ ] **T18 · Fecha de nacimiento y bloqueo de menores de 18 como titulares**
  - **Archivos:** `src/app/(auth)/register/page.tsx`, `src/lib/utils/date.ts`
  - **Cómo lo compruebo:** me registro con una fecha de hace 15 años; la app NO crea la cuenta y me explica que necesito un perfil dentro de la cuenta de una persona adulta. *(Regla 25.)*
  - **Depende de:** T16

- [ ] **T19 · Casilla de consentimiento no premarcada, con enlaces a los documentos (RV-2, RV-3)**
  - **Archivos:** `src/components/auth/`, `src/app/(auth)/register/page.tsx`
  - **Cómo lo compruebo:** abro el registro; la casilla está **vacía** por defecto y si intento registrarme sin marcarla, no me deja. Los enlaces a privacidad y términos se leen bien.
  - **Depende de:** T16

- [ ] **T20 · Páginas de política de privacidad y términos de uso**
  - **Archivos:** `src/app/settings/legal/page.tsx`
  - **Cómo lo compruebo:** abro los dos enlaces desde el registro; cada uno lleva a un texto legible, sin ser un muro ilegible. *(Sección 6 de `03-legal.md`.)*
  - **Depende de:** T19

- [ ] **T21 · Registro con Google**
  - **Archivos:** `src/app/(auth)/register/page.tsx`, `src/lib/supabase/client.ts`
  - **Cómo lo compruebo:** elijo «Continuar con Google», me identifica y termina el registro.
  - **Depende de:** T16

- [ ] **T22 · Inicio de sesión con mensaje genérico y cerrar sesión**
  - **Archivos:** `src/app/(auth)/login/page.tsx`, `src/components/settings/`
  - **Cómo lo compruebo:** meto mal la contraseña y el mensaje NO dice si falló el correo o la contraseña. Cierro sesión y vuelvo a la pantalla de acceso. *(Reglas 3.)*
  - **Depende de:** T16

- [ ] **T23 · Recuperar contraseña con enlace que caduca en 24 h**
  - **Archivos:** `src/app/(auth)/forgot-password/page.tsx`, `src/app/(auth)/reset-password/page.tsx`
  - **Cómo lo compruebo:** pido el enlace, lo abro y cambio la contraseña; con uno caducado, la app me pide solicitar otro.
  - **Depende de:** T22

- [ ] **T24 · Proteger las rutas privadas**
  - **Archivos:** `src/middleware.ts`, `src/lib/supabase/middleware.ts`
  - **Cómo lo compruebo:** sin sesión, intento abrir `/` (la pantalla principal) y me lleva al acceso; con sesión, entro.
  - **Depende de:** T22

---

## HITO 3 · Hago el cuestionario y veo mis recomendaciones

**Al terminar verás:** 7 preguntas, y al enviarlas, una lista de libros
ordenada por tus gustos con el aviso de IA visible.

- [ ] **T25 · Cuestionario de 7 preguntas**
  - **Archivos:** `src/app/(onboarding)/questionnaire/page.tsx`, `src/components/onboarding/`
  - **Cómo lo compruebo:** recorro las 7 preguntas una a una; la última tiene un campo de texto libre.
  - **Depende de:** T24

- [ ] **T26 · Validaciones del cuestionario (máximo 3 opciones, no enviar a medias)**
  - **Archivos:** `src/components/onboarding/`, `src/lib/utils/validation.ts`
  - **Cómo lo compruebo:** intento marcar una cuarta opción y no me deja; intento enviar sin responder todo y me avisa. *(Caso límite «datos inválidos».)*
  - **Depende de:** T25

- [ ] **T27 · Guardar las respuestas y calcular el embedding de gustos**
  - **Archivos:** `src/lib/recommender/affinity.ts`, `scripts/generate_embeddings.py`
  - **Cómo lo compruebo:** tras enviar el cuestionario, en la tabla `questionnaire_responses` aparecen mis 7 respuestas.
  - **Depende de:** T15, T25

- [ ] **T28 · Pantalla de recomendaciones con el aviso de IA automatizada (RV-1)**
  - **Archivos:** `src/app/(book)/recommendations/page.tsx`, `src/components/book/`
  - **Cómo lo compruebo:** en la pantalla leo, de forma visible: *«Estas recomendaciones las genera un sistema automatizado basado en tus gustos.»* *(RV-1.)*
  - **Depende de:** T27

- [ ] **T29 · Consulta del recomendador por parecido vectorial**
  - **Archivos:** `src/lib/recommender/vector.ts`
  - **Cómo lo compruebo:** la lista muestra libros de mi nivel ordenados por afinidad y tarda menos de 1 segundo en cargar. *(Regla 6.)*
  - **Depende de:** T28

- [ ] **T30 · Botón «este no me engancha» con efecto en los parecidos y lista de pendientes**
  - **Archivos:** `src/app/(book)/recommendations/page.tsx`, `src/lib/recommender/vector.ts`
  - **Cómo lo compruebo:** descarto un libro, veo que desaparece y que ya no vuelve a recomendarse; lo encuentro en mi lista de pendientes y puedo recuperarlo. *(Regla 9.)*
  - **Depende de:** T29

- [ ] **T31 · Reparto equilibrado entre géneros**
  - **Archivos:** `src/lib/recommender/vector.ts`
  - **Cómo lo compruebo:** la lista incluye al menos un libro de cada género que marqué en el cuestionario, no todo del mismo tipo.
  - **Depende de:** T29

- [ ] **T32 · Caso «el catálogo se queda corto» con salida clara**
  - **Archivos:** `src/app/(book)/recommendations/page.tsx`
  - **Cómo lo compruebo:** descarto todos los libros de mi nivel y género; la app nunca me deja una pantalla vacía: me ofrece recuperar pendientes, subir de nivel o ampliar afinidad. *(Casos límite.)*
  - **Depende de:** T30

---

## HITO 4 · Elijo un libro y pongo mi meta

**Al terminar verás:** la ficha de un libro, la meta diaria elegida y ese
libro apuntado como «en curso».

- [ ] **T33 · Ficha de libro**
  - **Archivos:** `src/app/(book)/book/[id]/page.tsx`, `src/components/book/`
  - **Cómo lo compruebo:** abro un libro y veo título, autor/a, páginas, géneros, sinopsis breve, fragmento gancho y una reseña positiva.
  - **Depende de:** T29

- [ ] **T34 · Elegir meta diaria (0,5-4 páginas) y hora del recordatorio**
  - **Archivos:** `src/components/progress/`, `src/app/(main)/page.tsx`
  - **Cómo lo compruebo:** elijo media página al día y las 22:30; al reabrir la app, la meta y la hora siguen puestas. *(Reglas 15 y data `daily_goal_pages`.)*
  - **Depende de:** T33

- [ ] **T35 · Empezar un libro y permitir varios «en curso» a la vez**
  - **Archivos:** `src/app/(book)/book/[id]/page.tsx`, `src/lib/supabase/server.ts`
  - **Cómo lo compruebo:** empiezo dos libros distintos y los dos aparecen como «en curso», sin límite. *(Regla 7.)*
  - **Depende de:** T34

- [ ] **T36 · Panel principal con el libro actual, el progreso y la racha**
  - **Archivos:** `src/app/(main)/page.tsx`, `src/app/(main)/layout.tsx`, `src/components/layout/`
  - **Cómo lo compruebo:** abro la pantalla principal y veo, en móvil, el libro en curso, mi barra de progreso y el contador de racha, con la navegación inferior.
  - **Depende de:** T35

---

## HITO 5 · Registro mi lectura y veo mi racha

**Al terminar verás:** la pregunta «¿por qué página vas?», el día apuntado y
la racha subiendo de 1 en 1.

- [ ] **T37 · Registrar «¿por qué página vas?» con validaciones**
  - **Archivos:** `src/app/(main)/page.tsx`, `src/lib/utils/validation.ts`
  - **Cómo lo compruebo:** pruebo a poner una página menor que la anterior, una mayor que el total y una con letras; las tres se rechazan con un mensaje claro. *(Regla 10.)*
  - **Depende de:** T36

- [ ] **T38 · Micro-registro «leí un poco», sin pedir página**
  - **Archivos:** `src/app/(main)/page.tsx`, `src/components/progress/`
  - **Cómo lo compruebo:** pulso «leí un poco» y el día cuenta como leído sin pedirme número de página. *(Regla 12.)*
  - **Depende de:** T37

- [ ] **T39 · Un solo registro por día (no se duplica)**
  - **Archivos:** `src/lib/supabase/server.ts`, `src/hooks/useBookProgress.ts`
  - **Cómo lo compruebo:** registro la página dos veces el mismo día; el día cuenta una sola vez y la racha no se infla. *(Casos límite «dos acciones a la vez».)*
  - **Depende de:** T37

- [ ] **T40 · Registro retroactivo hasta 7 días atrás**
  - **Archivos:** `src/components/progress/`, `src/lib/utils/date.ts`
  - **Cómo lo compruebo:** apunto lectura de anteayer y lo acepta; pruebo con hace 9 días y lo rechaza con aviso. *(Regla 11.)*
  - **Depende de:** T39

- [ ] **T41 · Cálculo de la racha con reanudación**
  - **Archivos:** `src/lib/streak/calculator.ts`, `src/hooks/useStreak.ts`
  - **Cómo lo compruebo:** leo 3 días seguidos y veo racha 3; dejo pasar un día y veo «interrumpida»; al volver a leer, se reanuda sin mensajes de culpa y conserva mi mejor racha. *(Regla 13.)*
  - **Depende de:** T39

- [ ] **T42 · Corte del día a medianoche local del dispositivo**
  - **Archivos:** `src/lib/utils/timezone.ts`, `src/lib/utils/date.ts`
  - **Cómo lo compruebo:** cambio la hora del móvil de un día al siguiente y el día de corte se mueve con la hora local, sin regalarme ni quitarme un día de racha. *(Regla 12 y casos límite de la racha.)*
  - **Depende de:** T41

- [ ] **T43 · Pantalla de racha**
  - **Archivos:** `src/app/(main)/streak/page.tsx`, `src/components/progress/`
  - **Cómo lo compruebo:** la racha se distingue por forma o texto, no solo por color, y se entiende sin explicación.
  - **Depende de:** T41

- [ ] **T44 · Registro de progreso sin conexión (no se pierde)**
  - **Archivos:** `public/sw.js`, `src/hooks/useBookProgress.ts`
  - **Cómo lo compruebo:** pongo el móvil en modo avión, registro una página, recupero la conexión y el registro se envía solo. *(Caso límite «falla la conexión».)*
  - **Depende de:** T39

---

## HITO 6 · Termino un libro, reseño y recibo mi título

**Al terminar verás:** la celebración sobria, la reseña guardada y un título
de lector en tu perfil.

- [ ] **T45 · Marcar el libro como terminado (una sola vez)**
  - **Archivos:** `src/app/(book)/book/[id]/page.tsx`, `src/lib/supabase/server.ts`
  - **Cómo lo compruebo:** al llegar a la última página, el libro pasa a «completado»; si pulso «terminar» dos veces, no se duplica nada. *(Casos límite «dos acciones a la vez».)*
  - **Depende de:** T37

- [ ] **T46 · Pantalla de reseña: 3 emociones, 1-5 estrellas, frase opcional**
  - **Archivos:** `src/app/(book)/book/[id]/review/page.tsx`, `src/components/review/`
  - **Cómo lo compruebo:** solo marcando emociones y estrellas (sin escribir frase) la reseña se guarda igual. Con 4 emociones, no me deja. *(Regla 14.)*
  - **Depende de:** T45

- [ ] **T47 · La reseña alimenta las recomendaciones siguientes**
  - **Archivos:** `src/lib/recommender/affinity.ts`, `src/lib/recommender/vector.ts`
  - **Cómo lo compruebo:** reseño un libro con emoción «intriga» y veo que las siguientes recomendaciones se acercan a ese tipo.
  - **Depende de:** T46

- [ ] **T48 · Títulos de lector en los hitos (D-1)**
  - **Archivos:** `src/lib/`, `src/components/settings/`, `src/app/(main)/page.tsx`
  - **Cómo lo compruebo:** al reseñar mi primer libro aparece «Punto y aparte»; al llegar a 3 libros, «La tercera orilla». Se ven en mi perfil y no se pierden por dejar de leer. *(R7.)*
  - **Depende de:** T46

- [ ] **T49 · Sugerencia voluntaria de subir de nivel (3 libros) con los rangos D-4**
  - **Archivos:** `src/lib/`, `src/components/progress/`, `src/app/(book)/recommendations/page.tsx`
  - **Cómo lo compruebo:** al terminar el tercer libro de nivel 1 aparece la sugerencia; puedo aceptarla o rechazarla sin castigo y volver atrás cuando quiera. *(Regla 15.)*
  - **Depende de:** T45

- [ ] **T50 · Desbloqueo del «catálogo experto» (nivel ≥6 y 3 meses × 4 días/semana)**
  - **Archivos:** `src/lib/`, `src/components/progress/`
  - **Cómo lo compruebo:** con datos de prueba que simulan 3 meses leyendo 4 días por semana, aparece el aviso de desbloqueo; sin ellos, no. *(Reglas 16 y D-5.)*
  - **Depende de:** T49

- [ ] **T51 · Compartir el logro, siempre opcional**
  - **Archivos:** `src/app/share/[id]/page.tsx`, `src/components/review/`
  - **Cómo lo compruebo:** en la celebración aparece la opción de compartir; si no la toco, no se comparte nada. La imagen dice «Terminé [Libro]» y es sobria. *(R8.)*
  - **Depende de:** T45

---

## HITO 7 · Recordatorios, ajustes y todos mis derechos legales

**Al terminar verás:** la pantalla de ajustes desde la que puedes ver, editar,
descargar y borrar tus datos sin escribir a nadie.

- [ ] **T52 · Pedir permiso y suscribir a notificaciones push (RV-7)**
  - **Archivos:** `src/app/api/push/subscribe/route.ts`, `src/lib/notifications/push.ts`
  - **Cómo lo compruebo:** al activar el recordatorio aparece el diálogo del sistema pidiendo permiso; si digo que no, no llega ninguna notificación. *(RV-7.)*
  - **Depende de:** T34

- [ ] **T53 · Enviar el recordatorio a la hora elegida**
  - **Archivos:** `src/app/api/push/send/route.ts`, `src/lib/notifications/schedule.ts`
  - **Cómo lo compruebo:** pongo el recordatorio a una hora cercana y esa hora recibo la notificación en el móvil.
  - **Depende de:** T52

- [ ] **T54 · Pantalla de ajustes**
  - **Archivos:** `src/app/settings/page.tsx`
  - **Cómo lo compruebo:** entro en ajustes y veo, en una sola pantalla: perfil, gustos, menores, privacidad, documentos legales y cerrar sesión. *(R9.)*
  - **Depende de:** T24

- [ ] **T55 · Derecho de acceso: ver todos mis datos (RV-4)**
  - **Archivos:** `src/app/settings/privacy/page.tsx`
  - **Cómo lo compruebo:** pulso «ver mis datos» y aparecen mi perfil, mis 7 respuestas, mi progreso, mis reseñas, mis títulos y mi racha.
  - **Depende de:** T54

- [ ] **T56 · Derecho de rectificación: editar perfil, gustos y reseñas (RV-4)**
  - **Archivos:** `src/app/settings/profile/page.tsx`, `src/components/onboarding/`
  - **Cómo lo compruebo:** edito una respuesta del cuestionario y una reseña, y al volver a mirar están cambiadas.
  - **Depende de:** T54

- [ ] **T57 · Derecho de supresión: botón «Eliminar mi cuenta» con corte inmediato (RV-5)**
  - **Archivos:** `src/app/settings/privacy/page.tsx`
  - **Cómo lo compruebo:** pulso eliminar, confirmo, y mi cuenta queda inaccesible al instante. No hay que escribir ningún correo a nadie.
  - **Depende de:** T54

- [ ] **T58 · Borrar los datos personales en 30 días**
  - **Archivos:** `supabase/migrations/`, tarea programada
  - **Cómo lo compruebo:** con una cuenta de prueba marcada como «pendiente de borrado», el proceso programado la elimina; en la base de datos ya no quedan sus datos personales. *(Conservación, `03-legal.md` 4.4.)*
  - **Depende de:** T57

- [ ] **T59 · Anonimizar las reseñas al eliminar la cuenta**
  - **Archivos:** `supabase/migrations/`, tarea programada
  - **Cómo lo compruebo:** tras eliminar la cuenta de prueba, su reseña sigue existiendo pero ya no está vinculada a ninguna persona.
  - **Depende de:** T58

- [ ] **T60 · Derecho de oposición: desactivar la personalización**
  - **Archivos:** `src/app/settings/privacy/page.tsx`, `src/lib/recommender/affinity.ts`
  - **Cómo lo compruebo:** apago la personalización y las recomendaciones dejan de usar mi perfil; las vuelvo a encender y vuelven. *(Regla 22.)*
  - **Depende de:** T54

- [ ] **T61 · Derecho de limitación: congelar los datos sin borrarlos**
  - **Archivos:** `src/app/settings/privacy/page.tsx`
  - **Cómo lo compruebo:** en ajustes encuentro la forma de solicitarlo y se explica claramente en qué consiste.
  - **Depende de:** T54

- [ ] **T62 · Derecho de portabilidad: descargar todos mis datos (RV-6)**
  - **Archivos:** `src/app/settings/privacy/page.tsx`
  - **Cómo lo compruebo:** pulso «descargar mis datos» y obtengo un archivo (JSON o CSV) que al abrirlo contiene mi perfil, respuestas, progreso y reseñas. *(RV-6.)*
  - **Depende de:** T55

- [ ] **T63 · Registro de actividades de tratamiento (documento interno)**
  - **Archivos:** `docs/08-registro-tratamiento.md`
  - **Cómo lo compruebo:** existe el documento y en él figuran las finalidades, los datos tratados, la base legal y los plazos, sacados de `docs/03-legal.md`. No es público.
  - **Depende de:** —

- [ ] **T64 · Verificar que los datos viven en la Unión Europea (RV-9)**
  - **Archivos:** `docs/bitacora.md` (anotación)
  - **Cómo lo compruebo:** en el panel de Supabase la región sigue siendo UE y ningún servicio externo recibe datos personales. *(RV-9.)*
  - **Depende de:** T58

- [ ] **T65 · Verificar que no se recogen datos especiales (RV-10)**
  - **Archivos:** `src/components/onboarding/`
  - **Cómo lo compruebo:** releo las 7 preguntas del cuestionario y compruebo que ninguna pregunta por salud, ideología, religión, orientación sexual, origen étnico ni biometría. *(RV-10.)*
  - **Depende de:** T25

---

## HITO 8 · Perfiles de menores

**Al terminar verás:** un perfil de menor dentro de tu cuenta, con su propio
recorrido y sus datos totalmente separados de los tuyos.

- [ ] **T66 · Crear un perfil de menor con aviso de responsabilidad (RV-8)**
  - **Archivos:** `src/app/settings/minors/page.tsx`
  - **Cómo lo compruebo:** al crear el perfil aparece, antes de continuar, un aviso claro de que asumo la responsabilidad de esos datos, y tengo que confirmarlo. *(RV-8.)*
  - **Depende de:** T54

- [ ] **T67 · Límite de 5 perfiles por cuenta (D-2)**
  - **Archivos:** `src/app/settings/minors/page.tsx`
  - **Cómo lo compruebo:** creo 5 perfiles e intento crear el sexto; la app me lo impide y lo explica.
  - **Depende de:** T66

- [ ] **T68 · Aislamiento total entre perfiles (regla 23)**
  - **Archivos:** `supabase/migrations/002_rls_policies.sql`
  - **Cómo lo compruebo:** con dos perfiles en la misma cuenta, el progreso y las reseñas de uno NO aparecen nunca en el otro.
  - **Depende de:** T66

- [ ] **T69 · Recorrido completo del perfil de menor**
  - **Archivos:** `src/app/(onboarding)/`, `src/app/(main)/`
  - **Cómo lo compruebo:** entro con el perfil del menor y hago todo: cuestionario, recomendaciones, meta, progreso, racha, reseñas y títulos, con sus datos propios.
  - **Depende de:** T68

- [ ] **T70 · Borrar un perfil de menor sin tocar los demás**
  - **Archivos:** `src/app/settings/minors/page.tsx`
  - **Cómo lo compruebo:** borro un perfil de menor y los otros perfiles y la cuenta siguen intactos.
  - **Depende de:** T68

- [ ] **T71 · Al cumplir 18, retirar el aviso de responsabilidad (sin convertir en cuenta propia)**
  - **Archivos:** `src/lib/utils/date.ts`, `src/app/settings/minors/page.tsx`
  - **Cómo lo compruebo:** con un perfil de prueba cuya fecha de nacimiento ya cumple 18, el aviso deja de mostrarse y el perfil sigue dentro de la cuenta de la adulta. *(Regla 26 y D-3.)*
  - **Depende de:** T66

---

## HITO 9 · Panel de curadora

**Al terminar verás:** un panel donde dar de alta, editar y retirar libros, y
ver el reparto del catálogo por nivel y género.

- [ ] **T72 · Dar de alta un libro desde el panel**
  - **Archivos:** `src/app/curator/books/page.tsx`, `src/components/curator/`
  - **Cómo lo compruebo:** relleno título, autor/a, idioma, páginas, géneros, sinopsis, fragmento y reseña positiva; el libro aparece en el catálogo.
  - **Depende de:** T14

- [ ] **T73 · Asignar el nivel automáticamente, editar y retirar libros**
  - **Archivos:** `src/app/curator/books/page.tsx`
  - **Cómo lo compruebo:** doy de alta un libro de 60 páginas y queda en nivel 1; al retirar un libro, deja de recomendarse pero el progreso de quien lo leyó se conserva. *(R11 y regla 24.)*
  - **Depende de:** T72

- [ ] **T74 · Pantalla de reparto del catálogo por nivel y género**
  - **Archivos:** `src/app/curator/balance/page.tsx`
  - **Cómo lo compruebo:** abro el balance y veo cuántos libros hay por nivel y por género, para detectar huecos antes de que falten opciones.
  - **Depende de:** T72

- [ ] **T75 · Proteger el panel por rol de curadora**
  - **Archivos:** `src/app/curator/layout.tsx`, `supabase/migrations/002_rls_policies.sql`
  - **Cómo lo compruebo:** con una cuenta normal intento abrir `/curator` y no puedo; la curadora entra sin problema y no ve datos de usuarias.
  - **Depende de:** T72

- [ ] **T76 · Recalcular los embeddings al crear o editar un libro**
  - **Archivos:** `scripts/generate_embeddings.py`, `src/app/curator/books/page.tsx`
  - **Cómo lo compruebo:** doy de alta un libro nuevo y, al poco, ese libro ya tiene `embedding` relleno y puede aparecer en recomendaciones.
  - **Depende de:** T72

---

## HITO 10 · Calidad, seguridad y publicación

**Al terminar verás:** la app publicada en una dirección con HTTPS, con las
pruebas en verde y el plan de vigilancia en marcha.

- [ ] **T77 · Pruebas automáticas de la parte determinista (Paso 13)**
  - **Archivos:** `tests/`, `package.json`
  - **Cómo lo compruebo:** ejecuto el comando de pruebas y todas pasan en verde. Cubren racha, niveles, validaciones de página y de fechas.
  - **Depende de:** T41, T62

- [ ] **T78 · Evals del recomendador de IA (Paso 14)**
  - **Archivos:** `evals/historial.md`, `evals/casos-dificiles.md`
  - **Cómo lo compruebo:** ejecuto los evals y veo un resultado con nota; el historial queda anotado con la fecha.
  - **Depende de:** T76, T77

- [ ] **T79 · Barreras de seguridad del sistema de IA (Paso 15)**
  - **Archivos:** `docs/05-plan-tecnico.md` (sección de guardrails)
  - **Cómo lo compruebo:** existe la lista de barreras y cada una tiene su comprobación definida.
  - **Depende de:** T78

- [ ] **T80 · Red team contra el propio sistema (Paso 16, OWASP LLM)**
  - **Archivos:** `seguridad/red-team.md`
  - **Cómo lo compruebo:** el documento recoge los ataques probados y el resultado de cada uno.
  - **Depende de:** T79

- [ ] **T81 · Accesibilidad WCAG 2.1 AA**
  - **Archivos:** `src/styles/globals.css`, `src/components/`
  - **Cómo lo compruebo:** navego con el lector de pantalla del móvil y todo se entiende; la racha y los niveles se distinguen sin depender solo del color. *(Sección 7.3.)*
  - **Depende de:** T43

- [ ] **T82 · Rendimiento: pantallas <2 s y recomendación <1 s**
  - **Archivos:** `src/app/`
  - **Cómo lo compruebo:** con el móvil en la mano, la pantalla principal carga en menos de 2 segundos y al descartar un libro el siguiente aparece casi al instante. *(Sección 7.2.)*
  - **Depende de:** T32

- [ ] **T83 · Código de prácticas de IA: documentar el recomendador**
  - **Archivos:** `docs/06-ia.md`
  - **Cómo lo compruebo:** existe una descripción de qué hace el recomendador, qué datos usa y qué límites tiene, entendible por alguien de fuera. *(Sección 3 de `03-legal.md`.)*
  - **Depende de:** T63

- [ ] **T84 · Publicar en Vercel con dominio propio y HTTPS**
  - **Archivos:** `next.config.ts`, panel de Vercel
  - **Cómo lo compruebo:** abro el dominio en el móvil y funciona con candado de sitio seguro. Un `git push` despliega solo. *(Sección 3.5.)*
  - **Depende de:** T80
  - **Gasto:** dominio ~12-24 €/año. Vercel y Supabase, en plan gratuito al inicio.

- [ ] **T85 · Migraciones aplicadas de forma automática (GitHub Action)**
  - **Archivos:** `.github/workflows/`
  - **Cómo lo compruebo:** hago un cambio en una migración y, al subirlo, se aplica sola sin tocar mi ordenador.
  - **Depende de:** T84

- [ ] **T86 · Medir el criterio de éxito (40 % leyendo 4 días/semana durante 4 semanas)**
  - **Archivos:** `docs/09-rutina.md`
  - **Cómo lo compruebo:** existe una consulta que devuelve ese porcentaje y sé cada cuándo se revisa. *(Criterio de éxito del problema.)*
  - **Depende de:** T84

---

## Trazabilidad legal

Cada obligación de `docs/03-legal.md` tiene al menos una tarea. Si alguna se
queda sin marcar, no se publica.

| Obligación | Tareas |
|---|---|
| RV-1 · Aviso de recomendación automatizada | T28 |
| RV-2 · Política de privacidad accesible | T19, T20 |
| RV-3 · Consentimiento no premarcado | T19 |
| RV-4 · Derechos ARSOLIP en la app | T55, T56, T57, T60, T61, T62 |
| RV-5 · Eliminación de cuenta en 30 días | T57, T58 |
| RV-6 · Portabilidad de datos | T62 |
| RV-7 · Notificaciones push con consentimiento | T52 |
| RV-8 · Perfiles de menores bajo responsabilidad del adulto | T66 |
| RV-9 · Datos en la Unión Europea | T04, T64 |
| RV-10 · Sin datos especiales | T25, T65 |
| Registro de actividades de tratamiento | T63 |
| Anonimización de reseñas | T59 |
| Código de prácticas de IA | T83 |

---

## Fase 2 (no construir ahora)

- **IA-2 · Agente RAG que genera boosts** (`book_boosts`): solo cuando haya
  datos reales de lectura. Coste < 1 $/mes en batch semanal. Antes de la
  primera llamada, análisis de privacidad nuevo. → tarea futura, no T01-T86.
- **Registro con Apple:** requiere cuenta de desarrollador de Apple
  (**99 €/año**). Decidir antes de publicar si se hace o se deja solo Google.
- **Analítica y banner de cookies:** fuera del MVP (D-7).

---

> **Cierre del Paso 8.** Con esto termina la fase de planificación del
> Manual de obra. Es el momento de invocar **`/bitacora`** y dejar por
> escrito qué se descartó, qué dolió y qué se hará distinto en el Paso 9.
