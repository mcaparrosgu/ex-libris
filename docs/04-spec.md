# Ex-libris: especificación funcional

> **Paso 5 — spec-driven development.** Este documento describe **qué** hace el
> sistema y **por qué**. No menciona tecnología, lenguaje, framework ni base de
> datos a propósito: eso pertenece al Paso 6 (plan técnico).
>
> Fuentes: `docs/00-problema.md`, `docs/01-historias.md`, `docs/03-legal.md` y
> `docs/bitacora.md`. El Paso 3 (MVP) se resolvió de forma implícita: las
> historias marcadas como IMPRESCINDIBLE forman el alcance mínimo y las marcadas
> como DESEABLE quedan sujetas a la sección 8.

---

## 1. OBJETIVO

Ex-libris existe para que una persona que quiere leer pero abandona consiga terminar su primer libro y sostener el hábito. Le recomienda libros cortos, con gancho y ajustados a sus gustos, y le sube la dificultad a su propio ritmo. Convierte el avance diario en un gesto mínimo —anotar por qué página va— para que un mal día no cuente como fracaso. Le devuelve identidad al esfuerzo con una racha, unos títulos de lector con referencia literaria y una celebración contenida. Es una rampa de entrada a la lectura: no es una red social, no es un catálogo y no es un chat.

Se considerará lograda cuando **el 40 % de quienes se registran lean 4 o más días por semana durante 4 semanas seguidas**.

---

## 2. USUARIOS Y PERMISOS

Hay tres tipos de usuario. Los dos primeros son personas de fuera; el tercero es el equipo que mantiene el catálogo.

### 2.1 Usuaria adulta (titular de la cuenta)

Es la dueña de la cuenta y la única que existe en el momento del registro. Debe ser **mayor de edad**: al crear su perfil indica su **fecha de nacimiento**, de la que el sistema deriva la edad. Si la edad resultante es menor de 18 años, no puede tener cuenta propia y debe usar un perfil dentro de la cuenta de una persona adulta.

| Puede | No puede |
|---|---|
| Registrarse, iniciar y cerrar sesión, recuperar su contraseña | Ver o modificar los datos de otra cuenta |
| Responder y editar su cuestionario de gustos | Acceder al mantenimiento del catálogo |
| Ver recomendaciones, descartar libros y configurar su meta | Cambiar el nivel o la longitud de un libro |
| Registrar su progreso, ver su racha y sus títulos | Publicar reseñas de libros que no ha terminado |
| Escribir reseñas de los libros que termina | |
| Ejercer sus derechos de acceso, rectificación, supresión, oposición, limitación y portabilidad | |
| Crear y gestionar perfiles de menores dentro de su cuenta | |
| Decidir si comparte o no sus logros | |

### 2.2 Perfil de menor (dentro de la cuenta de una adulta)

Un perfil de menor **no es una cuenta independiente**: vive dentro de la cuenta de la adulta titular, que asume la responsabilidad del tratamiento de sus datos (obligación RV-8 de la sección 7).

- Cada menor tiene **su propia experiencia completa y personalizada**: su cuestionario, sus recomendaciones, su libro en curso, su progreso, su racha, sus reseñas y sus títulos de lector.
- Los datos de un menor **no se mezclan** con los del titular ni con los de otro menor.
- El menor puede hacer todo lo que hace la titular **salvo** gestionar la cuenta, ejercer los derechos de privacidad y crear o borrar perfiles. Eso lo hace la adulta titular.

### 2.3 Curadora interna (equipo)

Persona del equipo que mantiene el catálogo **desde dentro de la propia aplicación**.

| Puede | No puede |
|---|---|
| Dar de alta, editar y retirar libros | Ver datos personales de las usuarias |
| Asignar nivel, géneros, longitud, sinopsis, fragmento gancho y reseña positiva a un libro | Escribir reseñas como si fuera una lectora |
| Consultar qué libros tiene el catálogo por nivel y por género | Modificar progreso, rachas o reseñas de nadie |

---

## 3. RECORRIDOS

Cada recorrido describe los pasos y **qué ve la usuaria en cada pantalla**. Los textos legales que debe ver están marcados con su identificador (RV-x).

### R1 · Registro y acceso

**Pantalla de registro.**
- La usuaria elige: correo y contraseña, o registro con Google o con Apple.
- Indica su **fecha de nacimiento** para crear el perfil; el sistema deriva su edad. Si es menor de 18 años, no se crea la cuenta y se le explica que debe usar un perfil dentro de la cuenta de una persona adulta.
- La contraseña debe tener **al menos 6 caracteres**; si no, se le indica.
- Antes de poder registrarse ve, **sin marcar por defecto**, una casilla de aceptación de la política de privacidad y los términos de uso, con **enlaces legibles** a ambos documentos (RV-2, RV-3). Si no la marca, el registro no se completa.
- Si el correo ya existe, se le invita a iniciar sesión.

**Pantalla de inicio de sesión.**
- Correo y contraseña. Si son incorrectos, mensaje **genérico**, sin decir cuál de los dos falló.
- Enlace «¿Olvidaste tu contraseña?».

**Recuperar contraseña.**
- Introduce su correo y recibe un enlace para crear una nueva contraseña.
- El enlace **caduca a las 24 horas**; si ha caducado, se le pide solicitar otro.

**Cerrar sesión.** Disponible desde ajustes en cualquier pantalla; vuelve al inicio de sesión.

**Primera vez.** Al terminar el registro pasa directamente al cuestionario (R2).

### R2 · Onboarding: cuestionario de gustos

- **7 preguntas**, una a una, de selección múltiple con **máximo 3 opciones** por pregunta. La última pregunta incluye un **campo de texto libre** (referencias que le gusten).
- Si intenta marcar una cuarta opción, el sistema no se lo permite y se lo indica.
- Al responder todas, envía y pasa a sus recomendaciones (R3).
- El cuestionario **no pregunta** por salud, ideología, religión, orientación sexual, origen étnico ni datos biométricos (RV-10).
- Tiempo objetivo del recorrido completo: **unos 3 minutos**.

**Editar gustos más adelante.** Desde ajustes, la usuaria abre «Editar mis gustos» y ve el mismo cuestionario con sus respuestas **pre-rellenadas**; puede cambiarlas y guardar.

### R3 · Elegir el primer libro

**Pantalla de recomendaciones.**
- Lista de libros del **nivel 1** (menos de 70 páginas), **ordenada por afinidad** con sus respuestas, con reparto equilibrado entre géneros.
- En pantalla, de forma visible, aparece la frase (RV-1):
  *«Estas recomendaciones las genera un sistema automatizado basado en tus gustos.»*
- Cada libro se muestra con: **título, autor/a, número de páginas, géneros, sinopsis breve, fragmento gancho y una reseña positiva**.

**Ficha del libro.** Al seleccionar un libro se abre la ficha con esos mismos datos.

**«Este no me engancha, dame otro».**
- El libro pasa a la lista de **pendientes** y se muestra otro del mismo nivel.
- Ese descarte cuenta como **señal negativa**: el sistema deja de recomendar ese libro **y los parecidos** (regla 5.9).
- La usuaria puede abrir su lista de **pendientes** cuando quiera y recuperar uno de ellos.

**Configurar meta.**
- Al elegir un libro para empezar, elige cuántas páginas leer al día: **entre 0,5 y 4 páginas**.
- Elige una **hora para el recordatorio**. Al activarlo, aparece el diálogo del sistema pidiendo permiso explícito para enviar notificaciones (RV-7). No se activan por defecto.
- Si concede el permiso, esa noche (y las siguientes a esa hora) recibe una **notificación nativa** en el dispositivo.
- Si no lo concede, ve su meta al abrir la aplicación.

### R4 · Progreso diario y racha

**Pantalla principal, cada día.**
- La aplicación pregunta: **«¿Por qué página vas?»** y la usuaria introduce su página actual (número entero, no «cuántas leíste hoy»).
- Se calculan las páginas leídas hoy y se actualiza la racha.
- Al día siguiente se vuelve a preguntar; **el registro no se duplica** aunque abra la app varias veces.

**Micro-registro (día malo).**
- Si solo ha leído dos frases o un párrafo, puede marcar **«leí un poco»**: registra un día de lectura **sin indicar número de página**, para que un día flojo no cuente como fracaso.
- El micro-registro también cuenta como **día activo** para la racha.

**Registro retroactivo.**
- Si leyó y no lo registró, puede anotarlo con **la fecha elegida a mano**, hasta **7 días atrás** y con **un registro por día**.

**Pantalla de racha.**
- Contador de **días consecutivos** y **calendario visual** con los días leídos marcados.
- Si no leyó ayer, la racha se muestra como **«interrumpida»**, con opción de **reanudarla hoy** (sin mensajes de culpa).

### R5 · Terminar un libro y reseñar

**Pantalla de celebración (contenida).**
- Al alcanzar la última página, aparece una celebración **sobria**: sin confeti ni fuegos artificiales.
- Se le pide la reseña, que es un **gesto, no un formulario**:
  - **3 emociones** elegidas de una lista.
  - **De 1 a 5 estrellas**.
  - **Opcionalmente, una frase** sobre qué le ha gustado (sin spoilers) y a quién se lo recomendaría.
- Si solo toca emociones y estrellas, la reseña **se guarda igual** (el texto es opcional).

**Efectos.**
- El libro pasa a **completados**.
- Recibe su **título de lector** correspondiente (R7).
- Sus reseñas **alimentan las recomendaciones siguientes**: los libros sugeridos reflejan mejor las emociones y géneros que ha ido marcando.

### R6 · Progresión de niveles

- La usuaria empieza en el **nivel 1**. Al terminar **3 libros** del nivel actual, aparece una sugerencia **sutil y voluntaria**, por ejemplo:
  *«¿Quieres probar un libro del nivel 2? También puedes seguir en el nivel 1 — tú decides.»*
- Si **acepta**, sus nuevas recomendaciones son del nivel siguiente; si **rechaza**, no pasa nada: **no hay penalización ni mensajes de culpa**. Puede volver al nivel anterior cuando quiera.
- Los niveles suben **de longitud (unos 30-40 páginas por escalón) y también de complejidad de lectura**.
- **Catálogo experto.** Cuando la usuaria lleva **3 meses leyendo 4 o más días por semana**, se le notifica que ha desbloqueado el «catálogo experto» (clásicos y autores exigentes) y puede explorar los niveles avanzados. *(Resuelto: ver 9.1, D-5.)*

### R7 · Títulos de lector

- Al enviar la reseña de su **primer libro** recibe un título con **icono y referencia literaria** («Punto y aparte» — ver 9.1, D-1).
- Al alcanzar cada **hito** (3 libros, 5, 10…) recibe el título correspondiente y puede verlo en su **perfil**.
- El título es identidad, no puntuación: no se pierde por dejar de leer.

### R8 · Compartir (opcional)

- En la pantalla de celebración, y al llevar varios días seguidos, la usuaria **puede** —nunca se hace solo— compartir una imagen: «Terminé [Libro]» o «Hoy leí N páginas de [Libro]».
- La imagen es una **celebración contenida**, coherente con el tono de la app.
- Compartir es siempre **opt-in**.

### R9 · Ajustes y privacidad

Pantalla desde la que se ejerce todo lo que exige la ley. **Cada punto debe funcionar sin necesidad de escribir un correo a nadie.**

- **Acceso:** ver todos sus datos (perfil, respuestas, progreso, reseñas, títulos, racha).
- **Rectificación:** editar gustos, perfil y reseñas.
- **Supresión:** botón **«Eliminar mi cuenta»**. Al pulsarlo, la cuenta queda **inaccesible de inmediato** y los datos se borran del sistema **en un plazo máximo de 30 días**. Sus reseñas se **anonimizan** (se desvinculan de su identidad) en lugar de borrarse.
- **Oposición:** interruptor para **desactivar la personalización de recomendaciones** (a partir de ahí, recomendaciones generales sin usar su perfil).
- **Limitación:** solicitud de congelar los datos sin borrarlos, mediante contacto directo indicado en la propia pantalla.
- **Portabilidad:** botón para **descargar un archivo con todos sus datos** en un formato legible y abierto (perfil, respuestas, progreso, reseñas, títulos).
- Enlace permanente a la **política de privacidad** y a los **términos de uso** (RV-2).
- **Cerrar sesión**.
- **Gestionar perfiles de menores** (crear, cambiar, borrar).

### R10 · Perfiles de menores

- Desde ajustes, la adulta titular crea un perfil de menor.
- Al crearlo, se indica la **fecha de nacimiento** del menor; el sistema deriva su edad y confirma que es menor de 18 años.
- **Antes de crearlo**, aparece un aviso claro e informativo de que está creando un perfil para un menor y de que **asume la responsabilidad del tratamiento de esos datos** (RV-8). Debe confirmarlo para continuar.
- Ese menor realiza **todo el recorrido completo y personalizado**: cuestionario, recomendaciones, meta, progreso, racha, reseñas y títulos, con datos independientes.
- La adulta titular puede ver y gestionar los perfiles, pero **no puede escribir reseñas ni registrar progreso en nombre del menor** como si fuera el menor.

### R11 · Curación del catálogo (interno)

- La curadora interna entra con su propio acceso y gestiona el catálogo **desde la aplicación**.
- Puede **dar de alta** un libro indicando: título, autor/a, idioma, número de páginas, géneros, sinopsis breve, fragmento gancho y reseña positiva.
- El sistema **asigna el nivel** a partir de la longitud y de la complejidad indicada.
- Puede **editar** y **retirar** libros. Un libro retirado deja de recomendarse pero **no borra** el progreso ni las reseñas de quienes ya lo leyeron.
- Puede **consultar el reparto** del catálogo por nivel y por género, para detectar desequilibrios antes de que una usuaria se quede sin opciones (riesgo 2 del problema).

---

## 4. DATOS

En lenguaje natural. «Perfil lector» puede ser el de la titular o el de un menor.

**Cuenta adulta**
- Correo electrónico, contraseña (o identificación externa de Google/Apple), fecha de alta, estado de la cuenta (activa / pendiente de borrado), fecha en que se pidió el borrado.
- Consentimiento de privacidad y términos: aceptado sí/no y fecha.
- Pertenece a: una o varias **perfiles lectores** (la titular y sus menores).

**Perfil lector** (uno por persona: titular o menor)
- Nombre o alias, **fecha de nacimiento** (y edad derivada de ella), tipo (titular / menor), nivel actual, meta diaria de páginas, hora del recordatorio, permiso de notificaciones (sí/no), personalización activada (sí/no).
- Idioma preferido de lectura.
- Pertenece a: una **cuenta adulta**. Tiene: respuestas del cuestionario, libros en su estantería, registros de progreso, racha, reseñas, títulos obtenidos.

**Respuestas del cuestionario**
- Las 7 respuestas (opciones marcadas y texto libre). Fecha de última edición.

**Libro** (catálogo)
- Título, autor/a, idioma, número de páginas, géneros, sinopsis breve, fragmento gancho, reseña positiva, nivel asignado, estado (activo / retirado).

**Libro en la estantería de un perfil**
- Estado: **recomendado / en curso / pendiente (descartado) / completado**.
- Fecha de inicio, fecha de fin, página actual, valoración final.
- Relaciona: un **perfil lector** con un **libro**.

**Registro de progreso**
- Fecha del día leído, tipo (**por página** o **micro-registro «leí un poco»**), página alcanzada ese día.
- Un registro por perfil y por día.
- Relaciona: un **perfil lector** con un **libro en curso**.

**Racha**
- Días consecutivos actuales, mejor racha histórica, último día activo.

**Reseña**
- 3 emociones, estrellas (1-5), frase opcional, fecha. Texto siempre opcional.
- Relaciona: un **perfil lector** con un **libro completado**.

**Meta de lectura**
- Páginas al día (0,5-4) y hora del recordatorio.

**Título de lector**
- Nombre del título, icono, referencia literaria, hito que lo desbloquea, fecha en que se obtuvo.

**Historial de hitos y consentimientos**
- Qué hito se alcanzó y cuándo; permiso de notificaciones y consentimiento de privacidad, con su fecha (necesario para demostrar cumplimiento).

---

## 5. REGLAS DE NEGOCIO

Condiciones que el sistema **siempre** debe respetar.

1. **Sin consentimiento no hay cuenta.** No se puede completar el registro sin marcar la casilla de privacidad y términos (RV-3). La casilla nunca está premarcada.
2. **Contraseña mínima de 6 caracteres.**
3. **Errores de acceso genéricos:** nunca se revela si falló el correo o la contraseña.
4. **Cuestionario:** 7 preguntas, máximo 3 opciones por pregunta, última con texto libre.
5. **Niveles por longitud y complejidad.** Nivel 1: menos de 70 páginas. Cada escalón sube unas 30-40 páginas y aumenta la complejidad. *(Rangos exactos: ver 9.1, D-4.)*
6. **Orden de recomendaciones:** por afinidad con gustos y reseñas, con reparto equilibrado entre géneros. La usuaria siempre debe tener opciones dentro de su nivel antes de que el pozo se agote.
7. **Un foco, varios en curso:** la app empuja **un** libro como recomendación principal, pero la usuaria puede tener **varios libros «en curso» sin límite**.
8. **Nunca se recomienda** un libro ya completado, uno ya descartado, ni uno que esté en curso.
9. **El descarte es señal negativa:** al pulsar «este no me engancha», ese libro **y los parecidos** dejan de recomendarse. El libro pasa a **pendientes** y es recuperable.
10. **Progreso por página entera**, siempre hacia adelante: la página nueva **no puede ser menor** que la última registrada ni mayor que el total de páginas del libro. El micro-registro **no pide página**.
11. **Una lectura por día y perfil.** Registro retroactivo solo hasta **7 días atrás**.
12. **Día activo:** cuenta como día leído tanto un registro con página como un micro-registro. El día corta a **medianoche local del dispositivo**.
13. **Racha:** son días consecutivos. Un día sin lectura la marca **«interrumpida»**, pero **se puede reanudar** el mismo día en que se vuelve a leer. Nunca hay mensajes de culpa.
14. **Reseña:** 3 emociones + 1-5 estrellas + frase opcional. Sin el texto, la reseña se guarda igual.
15. **Nivel sugerido, nunca impuesto:** la sugerencia de subir de nivel aparece tras **3 libros completados** del nivel actual; aceptar o rechazar es libre y reversible.
16. **Catálogo experto** solo se desbloquea con **3 meses leyendo 4 o más días por semana**.
17. **Las recomendaciones son automatizadas** y así se informa en pantalla (RV-1).
18. **Las notificaciones requieren permiso explícito** y se pueden revocar (RV-7). Sin permiso, no se envía ninguna.
19. **Sin datos especiales:** el sistema no recoge salud, ideología, religión, orientación sexual, origen étnico ni datos biométricos (RV-10).
20. **Eliminar la cuenta:** la cuenta queda inaccesible de inmediato y los datos personales se borran en **30 días**. Las reseñas se **anonimizan** y pueden mantenerse como contenido agregado.
21. **Derechos siempre disponibles** desde ajustes: acceso, rectificación, supresión, oposición, limitación y portabilidad (RV-4, RV-5, RV-6).
22. **La oposición funciona de verdad:** al desactivar la personalización, las recomendaciones dejan de usar su perfil.
23. **Aislamiento entre perfiles:** los datos de un perfil (titular o menor) **nunca** se mezclan ni se muestran en otro.
24. **Retirar un libro no destruye el historial:** el progreso y las reseñas de quienes ya lo leyeron se conservan.
25. **Edad y mayoría de edad:** todo perfil indica su **fecha de nacimiento** al crearse y la edad se deriva de ella. Solo una persona de **18 años o más** puede ser titular de una cuenta. Un perfil de menor de 18 años **solo existe** dentro de la cuenta de una persona adulta.
26. **Al cumplir 18 años** un perfil de menor deja de estar sujeto al aviso de responsabilidad del adulto (RV-8), pero en el MVP **sigue dentro de la cuenta de la adulta**: no se convierte en cuenta propia de forma automática.

---

## 6. CASOS LÍMITE

**No hay datos todavía**
- Recién registrada, antes del cuestionario: no hay recomendaciones; se la guía a responderlo.
- Cuestionario a medias: no se generan recomendaciones hasta enviarlo; se conserva lo respondido.
- Sin racha aún: el contador muestra 0 y no un error.

**El catálogo se queda corto**
- Si en su nivel y sus géneros no quedan libros sin descartar, se le avisa con claridad y se le ofrecen: recuperar pendientes, subir de nivel (si ya le corresponde) o afinidad más amplia. **Nunca** una pantalla vacía sin explicación.

**Falla la conexión**
- Un registro de progreso o una reseña hecha sin conexión **no debe perderse**: se conserva y se envía al recuperar la conexión.
- Nunca se muestran datos a medias como si fueran definitivos.

**Datos inválidos**
- Página con letras, negativa, mayor que el total del libro o **menor** que la última registrada: se rechaza con un mensaje claro.
- Fecha futura o de hace más de 7 días: se rechaza.
- Correo con formato inválido o ya existente: se indica.
- Contraseña de menos de 6 caracteres: se indica.
- Cuarta opción en una pregunta o intento de enviar el cuestionario incompleto: se bloquea con aviso.
- Frase de reseña demasiado larga: se limita y se indica el máximo.
- Más estrellas de las permitidas o menos de 1: se rechaza.

**Dos acciones a la vez**
- Misma usuaria en dos dispositivos: gana el registro más reciente de página; no se duplica el día ni se corrompe la racha.
- Dos registros del mismo día: solo uno cuenta para la racha.
- Se pulsa «terminar libro» dos veces: la reseña y el título se generan una sola vez.
- Dos dispositivos intentan registrar el mismo día retroactivo: se queda el primero; el segundo se avisa.

**Bordes de la racha**
- Cambio de zona horaria o de hora del dispositivo: el corte del día es siempre la medianoche local actual; un cambio de zona no debe regalar ni robar días de racha.
- Vuelta a leer tras meses: la racha se reanuda desde 1, sin castigo; la mejor racha histórica se conserva.

**Menores**
- Un perfil de menor sin cuestionario: mismo caso que la titular (no hay recomendaciones aún).
- La titular borra un perfil de menor: se borran los datos de ese perfil y **no** afecta a los demás ni a la cuenta.
- La titular borra su cuenta con perfiles de menores: **todos** los datos asociados, incluidos los de los menores, siguen el mismo borrado de 30 días.
- Un menor cumple la mayoría de edad: al derivarse la edad de su **fecha de nacimiento**, el sistema deja de mostrarle el aviso de responsabilidad del adulto (RV-8); en el MVP el perfil **sigue dentro de la cuenta de la adulta** y no se convierte solo en cuenta propia (ver 9.1, D-3).

**Otros**
- Termina un libro pero no quiere reseñar en ese momento: puede cerrarla más tarde sin perder el hito ni el título.
- La usuaria descarta todos los libros de su nivel y de todos los géneros: se aplica el caso «el catálogo se queda corto».
- Pierde el permiso de notificaciones en el sistema operativo: la app deja de enviarlas de forma silenciosa y no insiste.

---

## 7. REQUISITOS NO FUNCIONALES

### 7.1 Privacidad y cumplimiento (RGPD)

- **Base legal por finalidad** (no se trata ningún dato para una finalidad distinta):
  - Registro y autenticación → ejecución del contrato.
  - Cuestionario de gustos → consentimiento.
  - Tracking de lectura → ejecución del contrato.
  - Reseñas → consentimiento (son opcionales).
  - Mejora de recomendaciones → interés legítimo, con derecho de oposición.
- **Consentimiento** de privacidad y términos antes del registro, no premarcado (RV-3).
- **Datos en la Unión Europea:** todos los datos personales se almacenan y tratan en servidores de la Unión Europea (RV-9).
- **Conservación:** mientras la cuenta esté activa. Al eliminarla, borrado de datos personales en **30 días** como máximo.
- **Reseñas al eliminar la cuenta:** se anonimizan y pueden conservarse como contenido agregado.
- **Datos de menores:** se tratan dentro de la cuenta de la adulta titular, que es informada y responsable (RV-8). No se recogen datos especiales de menores.
- **Sin categorías especiales:** nada de salud, ideología, religión, orientación sexual, origen étnico ni biometría (RV-10).
- **Documentos legales:** política de privacidad y términos de uso accesibles **antes** del registro y desde ajustes. Registro de actividades de tratamiento como documento interno desde el día 1.
- **Transparencia de IA:** aviso visible de que las recomendaciones las genera un sistema automatizado (RV-1).
- **Derechos ARSOLIP** ejercibles desde la app misma (RV-4, RV-5, RV-6).
- **Cookies/trackers:** no se usan en el MVP, por lo que **no** hay banner (solo aplicaría si más adelante se añade analítica).

### 7.2 Rendimiento

- Las pantallas principales responden en **menos de 2 segundos** en conexión móvil normal.
- Una recomendación nueva tras un descarte aparece en **menos de 1 segundo** (debe sentirse inmediata).
- El registro de progreso se confirma **al instante**, aunque se envíe después.

### 7.3 Accesibilidad

- Contraste y tamaños de texto conformes a **WCAG 2.1 nivel AA**.
- Zonas táctiles cómodas para uso con una mano, pensado para móvil.
- Navegación comprensible con **lector de pantalla**.
- Ninguna información depende **solo del color** (la racha y los niveles se distinguen también por forma o texto).
- Lenguaje claro, sin jerga técnica, para una usuaria que no es técnica.

### 7.4 Idiomas

- **Interfaz:** solo español en el MVP (ver 9.1, D-6).
- **Catálogo:** libros en español **y en las demás lenguas oficiales de España** (catalán, gallego, euskera y aranés).
- La usuaria indica su idioma preferido de lectura y puede cambiarlo.

### 7.5 Notificaciones

- Solo se envían con **permiso explícito** (RV-7) y a la hora elegida.
- Contenido breve, amable y fácil de desactivar.

### 7.6 Disponibilidad y fiabilidad

- La app funciona bien **en móvil**, que es el único entorno previsto para el MVP.
- Un fallo de conexión no debe provocar pérdida de datos ya introducidos.

---

## 8. FUERA DE ALCANCE

Todo esto **no** forma parte del MVP. Aparece aquí para que nadie lo dé por supuesto.

1. **Red social y catálogo de reseñas** (nada de Goodreads ni StoryGraph): sin feed, sin seguir a nadie, sin comentarios, sin hilos.
2. **Reseñas largas o formularios extensos.** La reseña es un gesto, no una encuesta.
3. **Chat con IA.** Se recomienda por parecido, no se conversa.
4. **Añadir libros no catalogados.** Solo se recomienda lo que está en el catálogo.
5. **Fichar y organizar una biblioteca personal**, marcar leídos fuera de la app, importar historiales.
6. **Lectores expertos.** No se compite con quien ya lee ni se ofrece el catálogo completo como escaparate.
7. **Releer libros ya completados.**
8. **Analítica y cookies** (y, por tanto, banner de cookies) en el MVP.
9. **Compra, venta o préstamo de libros**, enlaces de afiliado, monedas o pagos.
10. **Modo sin conexión completo.** Solo se tolera el registro temporal sin conexión descrito en la sección 6.
11. **Varios perfiles de adulto** en una misma cuenta: solo una adulta titular y sus perfiles de menores.
12. **Interfaz en varios idiomas** en el MVP (el catálogo sí es multilingüe; la interfaz, no).

---

## 9. CIERRE: DECISIONES

### 9.1 Decisiones cerradas

Quedaron resueltas al cerrar la spec. Se escriben aquí para poder revisarlas o revertirlas, no para reabrir el debate cada vez.

- **D-1 · Títulos de lector e hitos.** Se adoptan los propuestos: **Punto y aparte** (1er libro) · **La tercera orilla** (3) · **Vecina de Babel** (5) · **La última página no existe** (10) · **Pulso de tinta** (30 días de racha) · **Lectora de fondo** (3 meses constantes). Seis hitos en el MVP.
- **D-2 · Tope de perfiles de menores.** Máximo **5** por cuenta adulta, indicado en la pantalla de gestión.
- **D-3 · Edad y mayoría de edad.** Todo perfil (titular o menor) indica su **fecha de nacimiento** al crearse y la edad se deriva de ella. Solo una persona de 18 años o más puede ser titular de una cuenta. Al cumplir 18, el perfil de menor deja de requerir el aviso de responsabilidad del adulto pero **sigue dentro de la cuenta de la adulta** en el MVP. La edad se **declara**; no hay verificación documental en el MVP (riesgo legal menor asumido).
- **D-4 · Rangos de los 10 niveles.** N1 menos de 70 · N2 70-100 · N3 100-140 · N4 140-180 · N5 180-220 · N6 220-260 · N7 260-300 · N8 300-340 · N9 340-380 · N10 380 o más.
- **D-5 · «Catálogo experto».** Es una **etiqueta** que se aplica desde el nivel 6 y que **además** exige 3 meses leyendo 4 o más días por semana para desbloquearse.
- **D-6 · Idioma de la interfaz.** Solo español en el MVP. El catálogo sí es multilingüe (las lenguas oficiales de España).
- **D-7 · Analítica.** Sin analítica en el MVP y, por tanto, sin banner de cookies. Si se añade más adelante, revisar la sección 7.1.

### 9.2 Sin preguntas abiertas

Todas las dudas que surgieron al redactar la spec quedaron cerradas en el apartado 9.1. **No hay ninguna pregunta abierta bloqueando el Paso 6.**

Las únicas decisiones que conviene vigilar más adelante, porque tienen consecuencias legales o se toman por simplicidad, son dos y están documentadas: la **edad se declara sin verificación documental** (D-3) y el **perfil de menor sigue en la cuenta de la adulta al cumplir 18** (D-3). Si el proyecto crece, son las primeras que hay que revisar.
