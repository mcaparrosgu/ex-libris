# Bitácora del proyecto

Cuaderno de decisiones en orden cronológico. Guarda el porqué, qué se descartó y qué costó entender. Solo se añade al final, nunca se reescribe.

---

## 2026-09-27 · Pasos 1-4: cimientos del proyecto

- **QUÉ SE DECIDIÓ** — Arrancar Ex-libris desde cero con el método de 20 pasos. El problema es claro: personas que quieren leer pero abandonan por intimidación o falta de hábito. La app recomienda libros por nivel (longitud) y ayuda a crear constancia. Se completaron: Paso 1 (problema), Paso 2 (17 historias en 8 bloques), Paso 4 (legal: riesgo mínimo AI Act, RGPD con 10 requisitos verificables).
- **ALTERNATIVAS DESCARTADAS** — Clonar el repo anterior (construido con Lovable) y continuar. Se eligió repo limpio porque el historial de Lovable ensucia los commits y pierde la narrativa del método. Se saltó el Paso 3 (MVP) — se hará implícito en la spec (Paso 5).
- **POR QUÉ ESTA** — Historial limpio = case study fiel (Paso 19) + memoria didáctica honesta (Paso 20). El repo anterior se queda como referencia de consulta (esquema BD y recomendador vectorial valen oro).
- **QUÉ SE ROMPIÓ** — El repo remoto ya tenía un archivo LICENSE y el local tenía commits nuevos. Se resolvió con `git pull --rebase --allow-unrelated-histories`. No hubo `.gitignore` en la creación del repo remoto, pero no era problema: no había secretos subidos.
- **QUÉ QUEDA PENDIENTE DE ENTENDER** — 4 huecos del Paso 2 (push notifications, añadir libro no catalogado, múltiples libros simultáneos, idioma). Fuente del catálogo de libros (¿curación manual, API, scraping?). Cómo se mide "2 frases leídas" en la práctica (páginas vs párrafos). Títulos de lector: nombres exactos y referencias literarias.
- **DECISIONES DE DISEÑO CLAVE** — Niveles por longitud (no por dificultad temática). Trigger de nivel superior a los 3 libros, no 4 (menos fricción). Catálogo difícil separado: requiere constancia (3 meses × 4 días/sem) + longitud. Shareables opt-in, celebración contenida. Reseña = gesto (3 taps + estrellas + texto opcional), no encuesta. Registro diario = "¿por qué página vas?" (no "cuántas leíste"). Perfiles de menores bajo cuenta de adulto (simplifica RGPD).
