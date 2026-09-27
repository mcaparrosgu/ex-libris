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
