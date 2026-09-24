# Ex-libris: historias de usuario

## BLOQUE A: Registro y Autenticación

### H-A1 · Registro
**Como** persona que quiere empezar a leer, **quiero** crear una cuenta en 30 segundos, **para** acceder a mis recomendaciones personalizadas.
> **Prioridad:** IMPRESCINDIBLE

**Criterios de aceptación:**

- **Dado** que estoy en la pantalla de registro
  **Cuando** introduzco email + contraseña (o registro con Google/Apple)
  **Entonces** mi cuenta se crea y paso al cuestionario.

- **Dado** que estoy en la pantalla de registro
  **Cuando** el email ya existe
  **Entonces** se me invita a iniciar sesión.

- **Dado** que estoy en la pantalla de registro
  **Cuando** la contraseña tiene menos de 6 caracteres
  **Entonces** se me indica que debe tener al menos 6.

---

### H-A2 · Inicio de sesión
**Como** usuaria registrada, **quiero** entrar con mis credenciales, **para** retomar mi hábito de lectura.
> **Prioridad:** IMPRESCINDIBLE

**Criterios de aceptación:**

- **Dado** que estoy en la pantalla de login
  **Cuando** introduzco email y contraseña correctos
  **Entonces** accedo a mi pantalla principal.

- **Dado** que estoy en la pantalla de login
  **Cuando** las credenciales son incorrectas
  **Entonces** veo un mensaje de error genérico sin especificar cuál es el dato erróneo.

---

### H-A3 · Recuperar contraseña
**Como** usuaria que olvidó su contraseña, **quiero** restablecerla por email, **para** volver a acceder.
> **Prioridad:** IMPRESCINDIBLE

**Criterios de aceptación:**

- **Dado** que estoy en la pantalla de login
  **Cuando** pulso "¿Olvidaste tu contraseña?" e introduzco mi email
  **Entonces** recibo un email con enlace para crear una nueva contraseña.

- **Dado** que recibí el email de recuperación
  **Cuando** el enlace ha expirado (pasadas 24h)
  **Entonces** se me pide solicitar uno nuevo.

---

### H-A4 · Cerrar sesión
**Como** usuaria, **quiero** poder cerrar sesión, **para** proteger mi cuenta en dispositivos compartidos.
> **Prioridad:** IMPORTANTE

**Criterios de aceptación:**

- **Dado** que estoy en cualquier pantalla de la app
  **Cuando** accedo a ajustes y pulso "Cerrar sesión"
  **Entonces** vuelvo a la pantalla de inicio de sesión.

---

## BLOQUE B: Onboarding (Cuestionario)

### H-B1 · Cuestionario de gustos
**Como** usuaria nueva, **quiero** responder 7 preguntas rápidas sobre mis gustos, **para** que la app me recomiende libros que encajen conmigo.
> **Prioridad:** IMPRESCINDIBLE

**Criterios de aceptación:**

- **Dado** que acabo de registrarme
  **Cuando** empiezo el cuestionario
  **Entonces** veo las preguntas una a una, con opciones de selección múltiple (máx. 3 por pregunta) y la última con campo de texto libre.

- **Dado** que estoy respondiendo el cuestionario
  **Cuando** he contestado todas las preguntas
  **Entonces** puedo enviar mis respuestas y ver mis recomendaciones.

- **Dado** que estoy respondiendo el cuestionario
  **Cuando** intento seleccionar más de 3 opciones en una pregunta
  **Entonces** no se me permite y se me indica el límite.

---

### H-B2 · Editar perfil lector
**Como** usuaria, **quiero** modificar mis gustos más adelante, **para** ajustar mis recomendaciones si cambian mis preferencias.
> **Prioridad:** IMPORTANTE

**Criterios de aceptación:**

- **Dado** que estoy en ajustes
  **Cuando** pulso "Editar mis gustos"
  **Entonces** veo el mismo cuestionario con mis respuestas pre-rellenadas y puedo cambiarlas.

---

## BLOQUE C: Recomendaciones de Libros

### H-C1 · Ver recomendaciones
**Como** usuaria que ha completado el cuestionario, **quiero** ver una lista de libros de nivel 1 elegidos para mí, **para** elegir cuál empiezo.
> **Prioridad:** IMPRESCINDIBLE

**Criterios de aceptación:**

- **Dado** que he completado el onboarding
  **Cuando** veo mis recomendaciones
  **Entonces** aparecen libros de nivel 1 (<70 páginas) ordenados por afinidad con mis respuestas.

- **Dado** que veo un libro recomendado
  **Cuando** lo selecciono
  **Entonces** veo: título, autor/a, nº de páginas, géneros, sinopsis breve, fragmento gancho y reseña positiva.

---

### H-C2 · "Este no me engancha, dame otro"
**Como** usuaria indecisa, **quiero** descartar un libro y ver otro, **para** no quedarme atascada en una recomendación que no me convence.
> **Prioridad:** IMPRESCINDIBLE

**Criterios de aceptación:**

- **Dado** que estoy viendo un libro recomendado
  **Cuando** pulso "Este no me engancha"
  **Entonces** el libro pasa a "pendientes" y se me muestra otro del mismo nivel.

- **Dado** que he descartado varios libros
  **Cuando** quiero volver a uno descartado
  **Entonces** puedo acceder a mi lista de "pendientes" y seleccionarlo.

---

### H-C3 · Meta de lectura
**Como** usuaria que ha elegido un libro, **quiero** definir cuántas páginas leo al día y a qué hora me recuerda la app, **para** crear un hábito realista.
> **Prioridad:** IMPRESCINDIBLE

**Criterios de aceptación:**

- **Dado** que he elegido un libro para empezar
  **Cuando** configuro mi meta diaria
  **Entonces** puedo elegir entre 0,5 y 4 páginas, y una hora para el recordatorio.

- **Dado** que he configurado mi meta
  **Cuando** llega la hora del recordatorio
  **Entonces** recibo una notificación push (si la app lo permite) o veo la meta al abrir la app.

---

## BLOQUE D: Tracking Diario de Lectura

### H-D1 · Registrar progreso diario
**Como** usuaria que está leyendo un libro, **quiero** indicar por qué página voy, **para** que la app calcule cuánto he leído hoy y mantenga mi racha.
> **Prioridad:** IMPRESCINDIBLE

**Criterios de aceptación:**

- **Dado** que tengo un libro en curso
  **Cuando** abro la app y me pregunta «¿por qué página vas?»
  **Entonces** puedo introducir el número de página actual.

- **Dado** que introduzco la página actual
  **Cuando** la página es mayor que la última registrada
  **Entonces** se calculan las páginas leídas hoy y se actualiza mi racha.

- **Dado** que hoy ya registré progreso
  **Cuando** abro la app otro día
  **Entonces** se me vuelve a preguntar por mi página actual (no se duplica el registro).

---

### H-D2 · Ver racha de lectura
**Como** usuaria que quiere mantener el hábito, **quiero** ver mis días consecutivos leyendo, **para** sentirme motivada a no romper la cadena.
> **Prioridad:** IMPORTANTE

**Criterios de aceptación:**

- **Dado** que he registrado al menos un día de lectura
  **Cuando** accedo a mi pantalla principal
  **Entonces** veo un contador de racha (días consecutivos) y un calendario visual con los días leídos marcados.

- **Dado** que no leí ayer
  **Cuando** abro la app hoy
  **Entonces** mi racha se muestra como "interrumpida" pero puedo reanudarla hoy.

---

### H-D3 · Micro-registro flexible
**Como** usuaria con poco tiempo, **quiero** poder registrar incluso 2 frases o 1 párrafo, **para** que un día malo no cuente como fracaso.
> **Prioridad:** DESEABLE

**Criterios de aceptación:**

- **Dado** que leo muy poco hoy (2-3 frases)
  **Cuando** registro mi progreso
  **Entonces** la app reconoce que leí (aunque sea una fracción mínima) y lo cuenta como día activo.

---

## BLOQUE E: Reseña Post-Libro

### H-E1 · Reseña al terminar un libro
**Como** usuaria que acaba de terminar un libro, **quiero** dejar una reseña rápida, **para** cerrar el ciclo de lectura y sentir que lo he completado.
> **Prioridad:** IMPRESCINDIBLE

**Criterios de aceptación:**

- **Dado** que he alcanzado la última página de mi libro
  **Cuando** se me pide una reseña
  **Entonces** puedo: tocar 3 emociones (de una lista), dar de 1 a 5 estrellas, y opcionalmente escribir 1 frase sobre qué me ha gustado (sin spoilers) y a quién se lo recomendaría.

- **Dado** que estoy en la pantalla de reseña
  **Cuando** solo toco emociones y estrellas, sin texto
  **Entonces** la reseña se guarda igual (texto opcional).

- **Dado** que he enviado la reseña
  **Entonces** el libro pasa a "completados" y recibo mi título de lector correspondiente.

---

### H-E2 · Reseñas alimentan recomendaciones
**Como** usuaria, **quiero** que mis reseñas mejoren lo que me recomiendan después, **para** que la app me conozca mejor con cada libro.
> **Prioridad:** IMPORTANTE

**Criterios de aceptación:**

- **Dado** que he dejado reseñas en varios libros
  **Cuando** veo nuevas recomendaciones
  **Entonces** los libros sugeridos reflejan mejor mis preferencias (por emociones y géneros que marqué).

---

## BLOQUE F: Progresión de Niveles

### H-F1 · Sugerencia de nivel superior
**Como** usuaria que ha terminado 3 libros de mi nivel actual, **quiero** que se me sugiera probar libros de más páginas, **para** ir subiendo de dificultad a mi ritmo.
> **Prioridad:** IMPRESCINDIBLE

**Criterios de aceptación:**

- **Dado** que he completado 3 libros del nivel 1
  **Cuando** termino el tercer libro
  **Entonces** se me muestra una sugerencia sutil: "¿Quieres probar un libro del nivel 2? También puedes seguir en el nivel 1 — tú decides."

- **Dado** que acepto pasar al nivel 2
  **Cuando** elijo un libro del nivel 2
  **Entonces** mis nuevas recomendaciones son del nivel 2 (70-100 páginas), pero puedo volver al nivel 1 cuando quiera.

- **Dado** que rechazo pasar al nivel 2
  **Cuando** sigo en el nivel 1
  **Entonces** no hay penalización ni mensajes de culpa.

---

### H-F2 · Catálogo difícil (hábito demostrado)
**Como** usuaria que lee 4-5 días por semana durante 3 meses, **quiero** acceder a libros más complejos (clásicos, autores exigentes), **para** desafiar mi hábito ya consolidado.
> **Prioridad:** IMPORTANTE

**Criterios de aceptación:**

- **Dado** que llevo 3 meses leyendo ≥4 días/semana
  **Cuando** abro la app
  **Entonces** se me notifica que he desbloqueado el "catálogo difícil" y puedo explorar libros de niveles avanzados.

---

## BLOQUE G: Títulos de Lector (Gamificación)

### H-G1 · Recibir títulos de lector
**Como** usuaria, **quiero** recibir títulos con referencias literarias al completar hitos, **para** sentir que mi progreso tiene identidad y significado.
> **Prioridad:** IMPORTANTE

**Criterios de aceptación:**

- **Dado** que termino mi primer libro
  **Cuando** envío la reseña
  **Entonces** recibo el título "El Principiante" con su icono y referencia literaria.

- **Dado** que voy progresando en hitos (3 libros, 10 libros, etc.)
  **Cuando** alcanzo un nuevo hito
  **Entonces** recibo el título correspondiente con su referencia literaria y puedo verlo en mi perfil.

---

## BLOQUE H: Shareables

### H-H1 · Compartir logros (opcional)
**Como** usuaria orgullosa de mi progreso, **quiero** compartir qué estoy leyendo o que terminé un libro, **para** celebrarlo con mi entorno.
> **Prioridad:** DESEABLE

**Criterios de aceptación:**

- **Dado** que termino un libro
  **Cuando** veo la pantalla de celebración
  **Entonces** tengo la opción de compartir una imagen con "Terminé [Libro]" (opt-in, nunca automático).

- **Dado** que registro mi progreso diario
  **Cuando** llevo varios días seguidos
  **Entonces** tengo la opción de compartir "Hoy leí X páginas de [Libro]" (opt-in).

- **Dado** que comparto algo
  **Cuando** genero la imagen
  **Entonces** es una celebración contenida (sin confeti ni fuegos artificiales).

---

## HUECOS

1. **Notificaciones push:** ¿La app usará push nativo (PWA / app móvil) o solo recordatorios in-app? Esto afecta a H-C3.
2. **Añadir libro no catalogado:** ¿Puede una usuaria añadir un libro que no está en nuestra base de datos? No se deduce del problema.
3. **Múltiples libros simultáneos:** ¿Puede leer dos libros a la vez? El ejemplo concreto asume uno, pero no se explicita como regla.
4. **Idioma de los libros:** ¿Solo libros en español o también en otros idiomas?
