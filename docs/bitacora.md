# Bitácora del proyecto

Cuaderno de decisiones en orden cronológico. Guarda el porqué, qué se descartó y qué costó entender. Solo se añade al final, nunca se reescribe.

---

## 2026-09-27 · Pasos 1-4: cimientos del proyecto

- **QUÉ SE DECIDIÓ** — Arrancar Ex-libris desde cero con el método de 20 pasos. El problema es claro: personas que quieren leer pero abandonan por intimidación o falta de hábito. La app recomienda libros por nivel (longitud) y ayuda a crear constancia. Se completaron: Paso 1 (problema), Paso 2 (18 historias en 8 bloques), Paso 4 (legal: riesgo mínimo AI Act, RGPD con 10 requisitos verificables).
- **ALTERNATIVAS DESCARTADAS** — Clonar el repo anterior (construido con Lovable) y continuar. Se eligió repo limpio porque el historial de Lovable ensucia los commits y pierde la narrativa del método. Se saltó el Paso 3 (MVP) — se hará implícito en la spec (Paso 5).
- **POR QUÉ ESTA** — Historial limpio = case study fiel (Paso 19) + memoria didáctica honesta (Paso 20). El repo anterior se queda como referencia de consulta (esquema BD y recomendador vectorial valen oro).
- **QUÉ SE ROMPIÓ** — El repo remoto ya tenía un archivo LICENSE y el local tenía commits nuevos. Se resolvió con `git pull --rebase --allow-unrelated-histories`. No hubo `.gitignore` en la creación del repo remoto, pero no era problema: no había secretos subidos.
- **QUÉ QUEDA PENDIENTE DE ENTENDER** — 4 huecos del Paso 2 (push notifications, añadir libro no catalogado, múltiples libros simultáneos, idioma). Fuente del catálogo de libros (¿curación manual, API, scraping?). Cómo se mide "2 frases leídas" en la práctica (páginas vs párrafos). Títulos de lector: nombres exactos y referencias literarias.
- **DECISIONES DE DISEÑO CLAVE** — Niveles por longitud (no por dificultad temática). Trigger de nivel superior a los 3 libros, no 4 (menos fricción). Catálogo difícil separado: requiere constancia (3 meses × 4 días/sem) + longitud. Shareables opt-in, celebración contenida. Reseña = gesto (3 taps + estrellas + texto opcional), no encuesta. Registro diario = "¿por qué página vas?" (no "cuántas leíste"). Perfiles de menores bajo cuenta de adulto (simplifica RGPD).

---

## 2026-09-27 · Paso 5: especificación funcional

- **QUÉ SE DECIDIÓ** — Escribir `docs/04-spec.md` con las 9 secciones del método y **sin ninguna mención de tecnología**. Salieron **11 recorridos** (R1-R11), **26 reglas de negocio**, casos límite y las **10 obligaciones legales (RV-1 a RV-10)** colocadas dentro de las secciones, no como anexo. Se añadieron dos recorridos que no venían de las historias: **R9 Ajustes y privacidad** (obligación legal sin historia) y **R11 Curación del catálogo** (por el rol interno). Los menores entran en el MVP con experiencia completa y propia.
- **ALTERNATIVAS DESCARTADAS** — Verificar documentalmente la edad al registrarse (se quedó en declarar la fecha de nacimiento). Fracción de página para el micro-registro (se eligió «leí un poco» sin número). «Catálogo difícil» como un nivel más (se quedó como **etiqueta** desde el nivel 6). Permitir añadir libros no catalogados (fuera de alcance). Convertir en cuenta propia el perfil de menor que cumple 18 (se queda dentro de la cuenta de la adulta).
- **POR QUÉ ESTA** — La opción más simple que no compromete seguridad ni cumplimiento. El rol interno se eligió para poder reponer catálogo sin salir de la app.
- **QUÉ SE ROMPIÓ** — La spec destapó una contradicción: el progreso se registra por **página entera**, pero la meta admite 0,5 páginas y había un micro-registro de «2 frases». Se arregló con dos vías separadas. Se renombró «catálogo difícil» → **«catálogo experto»**. Se corrigió el recuento de historias de la entrada anterior (17 → **18**), desviándome a propósito de la regla de «no reescribir»: era un error de hecho, no una decisión.
- **QUÉ QUEDA PENDIENTE DE ENTENDER** — Nada bloquea el Paso 6: la spec cierra sin preguntas abiertas. Conviene vigilar dos decisiones legales tomadas por simplicidad: la edad se **declara sin verificación documental** y el perfil de menor **no se convierte solo** en cuenta al cumplir 18. Sigue sin resolverse (y no toca a la spec) la **fuente real del catálogo**: de dónde salen los 200+ libros por nivel.

---

## 2026-09-27 · Paso 6: plan técnico

- **QUÉ SE DECIDIÓ** — Stack: Next.js 15 + Supabase (PostgreSQL + pgvector + Auth + RLS) → Vercel. PWA con next-pwa. Modelo de 10 tablas con Row-Level Security. Recomendador vectorial Fase 1: pgvector directo (consulta SQL, 0 €/llamada, <50 ms). Recomendador Fase 2: agente RAG como complemento con análisis de patrones reales. Notificaciones push: MVP con badge in-app, push real post-MVP por limitaciones de iOS.
- **ALTERNATIVAS DESCARTADAS** — PocketBase + Railway (SQLite no escala tan bien, menos ecosistema). Firebase + Firestore (NoSQL obliga a desnormalizar, datos en UE no garantizado, Cloud Functions son "cajas negras" para depurar). Agente RAG como recomendador principal desde el día 1 (la spec exige <1 s de respuesta y un LLM tarda 2-5 s; además costaría ~10 €/día a 500 usuarias).
- **POR QUÉ ESTA** — PostgreSQL encaja con las 10+ entidades relacionales de la spec. pgvector resuelve el recomendador con SQL puro. RLS protege el aislamiento entre perfiles (RN-23) a nivel de base de datos. Datos en UE configurable (Frankfurt). Ecosistema Vercel ya conocido por Taskflow. El repo `desfasado-ex-libris` sirve de referencia para el esquema.
- **QUÉ SE ROMPIÓ** — Nada: la spec no tenía preguntas abiertas (sección 9.2), así que el plan técnico fluyó sin bloqueos. Se añadió la Fase 2 del recomendador (RAG) como evolución futura, no como bloqueador del inicio.
- **QUÉ QUEDA PENDIENTE DE ENTENDER** — Cómo se generan los embeddings de los libros en la práctica (script local vs. API). La tabla `curator_profiles` aún no tiene estructura definida en el plan. El reparto equilibrado por género necesita lógica de post-filtrado que se definirá en el Paso 8.
- **DECISIONES DE DISEÑO CLAVE** — pgvector directo primero, RAG después (coste y rendimiento). RLS como mecanismo principal de aislamiento de datos (no lógica de aplicación). PWA como entrega (no app nativa). Notificaciones in-app como MVP, push como mejora. Modelos de embeddings open-source locales (0 €).

---

## 2026-09-29 · Paso 7: papel de la IA

- **QUÉ SE DECIDIÓ** — IA solo en dos puntos: IA-1 embeddings del catálogo y del perfil (peldaño 1, modelo local `all-MiniLM-L6-v2`, 0 €/mes, riesgo bajo) e IA-2 agente RAG de boost del recomendador (peldaño 2, Fase 2 post-lanzamiento, batch semanal, <1 $/mes, riesgo medio con mitigación de anonimización). Todo lo demás (racha, niveles, títulos, permisos, retroactivo, descartes, reparto por género) es código determinista.
- **ALTERNATIVAS DESCARTADAS** — Peldaño 3 (workflow encadenado) y peldaño 4 (agente autónomo) para cualquier parte visible de la app: la spec prohíbe el chat y la IA no genera texto visible para la usuaria. IA-2 en peldaño 1: sin acceso al conjunto de documentos no hay análisis de patrones que justifique la Fase 2.
- **POR QUÉ ESTA** — El error de IA se autocorrige con el mecanismo ya diseñado (botón «este no me engancha» = señal negativa). El boost está acotado (0,5-1,5) y es reversible (borrar `book_boosts` = volver a pgvector puro). Coste 0 € al lanzamiento.
- **QUÉ SE ROMPIÓ** — Nada bloqueante. Se decidió posponer IA-2 hasta que existan datos reales de lectura: sin datos, el agente no tiene nada que aprender que pgvector no sepa ya.
- **QUÉ QUEDA PENDIENTE DE ENTENDER** — Análisis de privacidad completo de IA-2 antes de activarla (qué datos salen, a qué proveedora, con qué base legal). Elección del modelo económico exacto para el batch semanal. Cómo medir la tasa de descarte por nivel/género en el dashboard de curación.
- **DECISIONES DE DISEÑO CLAVE** — La IA no genera texto visible para la usuaria (jamás). La IA no toca racha, progreso ni permisos (esas tablas no aparecen en ninguna herramienta). Errores acotados por diseño (boost 0,5-1,5) y reversibles (borrar tabla = estado anterior). Detección de errores por métricas (tasa de descarte, tasa de finalización A/B).

---

## 2026-09-29 · Paso 8: lista de tareas

- **QUÉ SE DECIDIÓ** — Trocear spec, plan técnico, papel de la IA y legal en **86 tareas** agrupadas en **11 hitos**. Cada tarea lleva los archivos que toca, una comprobación manual (que la alumna puede hacer sin saber programar), de qué depende y su casilla. El terreno (Paso 9) queda fuera: solo se lista en el Hito 0 para no perderlo de vista.
- **ALTERNATIVAS DESCARTADAS** — Una sola tarea llamada «cumplir la normativa» (se troceó en RV-1 a RV-10, registro de tratamiento, anonimización de reseñas y código de prácticas de IA). Fusionar tareas para acortar la lista (se priorizó que cada una quepa en menos de una hora). Meter el registro con Apple en el MVP (cuesta 99 €/año → Fase 2). Construir el agente RAG de boosts ahora (Fase 2, <1 $/mes, cuando existan datos reales).
- **POR QUÉ ESTA** — La lista es el contrato del bucle de construcción: una tarea por sesión, `/plan` antes y `/clear` después. Que la comprobación la pueda hacer ella con sus ojos evita que el «ya funciona» lo decida el asistente.
- **QUÉ SE ROMPIÓ** — Al validar la lista apareció una dependencia circular: T77 (pruebas automáticas) decía depender de sí misma. Corregida a T41 y T62. Se cerró además lo que el Paso 6 dejó abierto: la estructura de `curator_profiles` pasa a ser T14 y el post-filtrado por género, T31.
- **QUÉ QUEDA PENDIENTE DE ENTENDER** — Marcados por la alumna como «sonó a chino»: **embedding, similitud coseno y pgvector**; **RLS** (que el aislamiento lo imponga la base de datos, no la aplicación); **service worker y cola sin conexión**; **evals, guardrails y red team**; y la **tabla de trazabilidad legal** (atar cada RV-x a una tarea y no publicar si alguna queda sin marcar). Quedan en `docs/glosario.md`.
