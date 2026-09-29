# Glosario de Ex-libris

> Palabras que aparecieron en el camino y quedaron apuntadas el mismo día que
> descolocaron. No es un diccionario general: solo lo que ha hecho falta en
> este proyecto. Cada término lleva **qué es**, una **analogía cotidiana** y
> **dónde se usa en Ex-libris**, para que el Paso 20 pueda leerlo tal cual.

---

## Cron job

- **Qué es:** una tarea que un servidor ejecuta solo, a una hora o un ritmo fijo.
- **Analogía:** un despertador programado que no necesita que nadie pulse nada.
- **En Ex-libris:** envía el recordatorio a la hora elegida (T53) y borra los datos de las cuentas eliminadas a los 30 días (T58).

## Embedding

- **Qué es:** texto convertido en una lista de números (un vector) que representa su significado.
- **Analogía:** el «código postal» del significado. Dos textos con códigos cercanos hablan de cosas parecidas.
- **En Ex-libris:** cada libro y cada perfil de gustos tienen el suyo; sobre ellos se calculan las recomendaciones (T15, T29).

## Evals

- **Qué es:** pruebas con nota para la parte de IA, porque su respuesta no es «sí o no» sino «qué tal».
- **Analogía:** un examen corregido con puntuación, no un interruptor.
- **En Ex-libris:** miden si las recomendaciones aciertan, con historial fechado en `evals/historial.md` (T78).

## Guardrails

- **Qué es:** límites que se ponen al sistema de IA para que no haga lo que no debe.
- **Analogía:** las barandillas de una carretera de montaña: no evitan conducir, evitan caerse.
- **En Ex-libris:** impiden que el recomendador use datos que no le corresponden y acotan el daño de un boost malo (T79).

## Manifest

- **Qué es:** un archivo que describe la app para que el móvil la trate como aplicación instalada.
- **Analogía:** el carné de identidad de la app ante el teléfono.
- **En Ex-libris:** nombre, icono y pantalla completa al añadir Ex-libris a la pantalla de inicio (T06).

## Migración

- **Qué es:** un archivo con un cambio de la base de datos, en orden, para poder repetirlo siempre igual.
- **Analogía:** el acta notarial de cada obra: si otro levanta el edificio mañana, sabe qué se hizo y en qué orden.
- **En Ex-libris:** `supabase/migrations/001` a `004` crean tablas, permisos, catálogo y vectores (T07-T13).

## OAuth

- **Qué es:** iniciar sesión usando la cuenta de otro servicio (Google, Apple) sin crear contraseña nueva.
- **Analogía:** entrar en casa de un amigo con su llave, en vez de que te hagan otra copia.
- **En Ex-libris:** el registro «Continuar con Google» (T21).

## pgvector

- **Qué es:** una extensión de PostgreSQL que guarda vectores y sabe compararlos.
- **Analogía:** añadir un buscador de «cosas parecidas» al archivador que ya usabas.
- **En Ex-libris:** resuelve el recomendador con SQL puro, 0 € por consulta y menos de 1 segundo (T13, T29).

## PWA

- **Qué es:** una web que se instala en el móvil y funciona como una app: icono propio, pantalla completa, avisos.
- **Analogía:** una caseta prefabricada: no es un edificio de obra, pero se usa igual.
- **En Ex-libris:** la entrega del MVP; no hay app nativa de tienda (Hito 0).

## Red team

- **Qué es:** atacar tu propio sistema a propósito, antes de que lo haga otro, para encontrar los fallos.
- **Analogía:** el abogado del diablo: discutir contra ti mismo para ver por dónde te rompen.
- **En Ex-libris:** pruebas contra el sistema de IA siguiendo la lista OWASP de riesgos de LLM (T80).

## RLS (Row-Level Security)

- **Qué es:** reglas dentro de la base de datos que deciden qué filas puede ver o editar cada usuario.
- **Analogía:** cristal esmerilado por filas: aunque alguien mire, solo ve lo suyo.
- **En Ex-libris:** impide que una usuaria vea datos de otra o de otro perfil, aunque la aplicación tenga un fallo (T10, T11, T68).

## Service worker

- **Qué es:** un pequeño programa que el navegador mantiene en segundo plano para tareas como guardar y reenviar datos.
- **Analogía:** el recepcionista que apunta un recado cuando no estás y te lo da al volver.
- **En Ex-libris:** guarda un registro de lectura hecho sin conexión y lo envía al recuperarla (T44).

## Similitud coseno

- **Qué es:** una medida de parecido entre dos vectores basada en el ángulo que forman.
- **Analogía:** dos flechas que apuntan casi al mismo sitio son parecidas; la medida no depende de lo largas que sean las flechas.
- **En Ex-libris:** es la cuenta que dice qué libro se parece más a tus gustos (T29).

## Trazabilidad

- **Qué es:** poder recorrer el camino completo desde un requisito hasta la tarea que lo cumple.
- **Analogía:** la etiqueta de una prenda: de dónde viene y de qué está hecha, sin tener que adivinar.
- **En Ex-libris:** la tabla que une cada RV-x con sus tareas; si una queda sin marcar, no se publica (final de `docs/07-tareas.md`).

## VAPID

- **Qué es:** el par de claves que identifica a la app ante el servicio de notificaciones del navegador.
- **Analogía:** el sello en cera de un sobre: demuestra que el aviso lo manda quien dice mandarlo.
- **En Ex-libris:** firma los recordatorios push; las claves viven fuera de git, en `.env.local` y en Vercel (T52).

## Web Push

- **Qué es:** un aviso que llega al móvil desde una web, aunque la web esté cerrada.
- **Analogía:** un mensaje que dejas en el buzón de casa, no una llamada que exige estar presente.
- **En Ex-libris:** el recordatorio diario a la hora elegida, solo si se concede el permiso (RV-7, T52).
