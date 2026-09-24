# Ex-libris: análisis legal y regulatorio

## 1. QUÉ HACE ESTE SISTEMA EN TÉRMINOS LEGALES

Ex-libris es una aplicación que **recomienda libros** a partir de las preferencias de la usuaria y le ayuda a **crear un hábito de lectura** mediante registro diario de progreso.

**Datos que trata:**
- **Identificación:** email (registro).
- **Preferencias:** respuestas a un cuestionario de gustos (cine, música, series, personalidad, energía, momento del día, referencias libres).
- **Uso:** registro de páginas leídas, racha de días, libros completados, reseñas breves (emociones, estrellas, texto opcional).
- **Perfil de lectura:** nivel actual, meta diaria, hora de recordatorio.

**Quién responde:** la persona o entidad que publica la app en el mercado (proveedora) es responsable del tratamiento de estos datos bajo el RGPD.

**Finalidad:** facilitar la creación de un hábito lector mediante recomendaciones personalizadas y seguimiento de progreso. No hay otra finalidad.

---

## 2. CLASIFICACIÓN AI ACT

**Riesgo mínimo.**

**Por qué:** el sistema de recomendación de Ex-libris **no es un sistema de IA de alto riesgo** según el Anexo III del Reglamento (UE) 2024/1689 (AI Act). No se usa en:
- Infraestructuras críticas, educación, empleo, servicios públicos esenciales, aplicación de la ley, migración, administración de justicia.
- No evalúa a personas ni toma decisiones que les afecten significativamente.
- No genera contenido sintético que pueda confundirse con real.
- No interactúa emocionalmente de forma engañosa.

**Qué papel juega Ex-libris:** **proveedora** (construye el sistema y lo pone en el mercado), no usuaria/desplegadora de un sistema de otro.

**Obligaciones de transparencia (riesgo limitado):** dado que la app **recomienda** libros mediante un algoritmo, aunque sea de riesgo mínimo, el AI Act exige informar a la usuaria de que las recomendaciones son **generadas por un sistema automatizado** y no por una persona. Esto se resuelve con una frase clara en la interfaz.

**Código de prácticas (voluntario pero recomendado):** adherirse al Código de Prácticas sobre IA del AI Act, que exige documentación del sistema de IA, evaluación de riesgos y medidas de mitigación. Aunque Ex-libris es riesgo mínimo, documentar cómo funciona el recomendador es buena práctica y genera confianza.

---

## 3. SALIDA RÁPIDA (obligaciones mínimas aunque sea riesgo mínimo)

Aunque el sistema sea de **riesgo mínimo**, estas obligaciones aplican igual:

1. **Transparencia AI Act:** informar a la usuaria de que las recomendaciones las genera un sistema automatizado (no una persona).
2. **RGPD:** tratar los datos personales conforme al Reglamento General de Protección de Datos (UE) 2016/679.
3. **Términos de uso y política de privacidad:** documento accesible antes del registro.
4. **Derechos ARSOLIP:** acceso, rectificación, supresión, oposición, limitación del tratamiento y portabilidad.
5. **Consentimiento para notificaciones push:** permiso explícito (ePrivacy + RGPD).

---

## 4. RGPD — ANÁLISIS DETALLADO

### 4.1 Base legal del tratamiento

| Finalidad | Base legal (artículo) | Justificación |
|---|---|---|
| Registro y autenticación | Art. 6.1.b (ejecución de contrato) | Necesario para prestar el servicio. |
| Cuestionario de gustos | Art. 6.1.a (consentimiento) | La usuaria acepta voluntariamente compartir sus preferencias. |
| Tracking de lectura | Art. 6.1.b (ejecución de contrato) | Es el servicio principal que se pide: registrar el progreso. |
| Reseñas | Art. 6.1.a (consentimiento) | La usuaria elige publicarlas; son opcionales. |
| Mejora de recomendaciones | Art. 6.1.f (interés legítimo) | Mejorar el servicio sin finalidad adicional. La usuaria puede oponerse. |

### 4.2 Datos de menores

Los perfiles de menores se crean **dentro de la cuenta de un adulto**, que asume la responsabilidad del tratamiento de sus datos. Esto simplifica el cumplimiento:
- **No se necesita consentimiento parental separado** (el adulto ya consiente al crear la cuenta).
- **Se debe informar al adulto** de que está creando perfiles para menores y que es responsable de su uso.
- **No se recogen datos especiales de menores** (solo preferencias de lectura y progreso).

### 4.3 Derechos de la usuaria (ARSOLIP)

| Derecho | Cómo se ejerce en Ex-libris |
|---|---|
| **Acceso** | La usuaria puede ver todos sus datos en la app (perfil, respuestas, progreso, reseñas). |
| **Rectificación** | Puede editar sus gustos, perfil y reseñas desde ajustes. |
| **Supresión** | Botón "Eliminar mi cuenta" en ajustes → borra todos sus datos en 30 días. |
| **Oposición** | Puede desactivar la personalización de recomendaciones desde ajustes. |
| **Limitación** | Puede solicitar que se congelen sus datos sin borrarlos (contacto directo). |
| **Portabilidad** | Puede descargar un archivo con todos sus datos (JSON/CSV) desde ajustes. |

### 4.4 Conservación de datos

- **Cuenta activa:** se conservan mientras la usuaria mantenga su cuenta.
- **Cuenta eliminada:** se borran todos los datos personales en **30 días** (plazo técnico para replicar el borrado en backups). Los datos anonimizados (estadísticas agregadas) se pueden conservar indefinidamente.
- **Reseñas publicadas:** si la usuaria elimina su cuenta, sus reseñas se **anonimizan** (se desvinculan de su identidad) pero pueden mantenerse como contenido agregado.

### 4.5 Encargados del tratamiento

| Proveedor | Qué trata | Dónde |
|---|---|---|---|
| **Supabase** | Email, perfiles, datos de lectura, autenticación | Servidores en la UE (región configurable) |
| **Proveedor de embeddings/IA** | Texto del cuestionario + sinopsis para generar recomendaciones | Verificar que el proveedor tenga sede/transferencia conforme al RGPD |
| **Servicio de notificaciones push** | Token de dispositivo para enviar recordatorios | Según el proveedor elegido |
| **Analytics (si aplica)** | Datos de uso anonimizados | Preferiblemente en la UE (ej. Plausible) |

---

## 5. REQUISITOS VERIFICABLES

Estos son los requisitos que **deben cumplirse** y que se pueden verificar con tests o revisión:

### RV-1 · Aviso de IA automatizada
- **Qué:** frase visible en la pantalla de recomendaciones: *"Estas recomendaciones las genera un sistema automatizado basado en tus gustos."*
- **Cómo verificar:** inspección visual de la interfaz.

### RV-2 · Política de privacidad accesible
- **Qué:** enlace a la política de privacidad en la pantalla de registro y en ajustes.
- **Cómo verificar:** el enlace existe y lleva a un documento legible (no muro de texto legal).

### RV-3 · Consentimiento explícito
- **Qué:** checkbox o toggle para aceptar la política de privacidad antes de registrarse. No pre-marcado.
- **Cómo verificar:** no se puede completar el registro sin marcar la casilla.

### RV-4 · Derechos ARSOLIP en la app
- **Qué:** la usuaria puede ejercer acceso, rectificación, supresión, oposición, limitación y portabilidad desde la app (no requiere email manual).
- **Cómo verificar:** cada derecho tiene un botón o sección en ajustes que funciona.

### RV-5 · Eliminación de cuenta
- **Qué:** botón "Eliminar mi cuenta" que inicia un borrado completo en 30 días.
- **Cómo verificar:** tras pulsar el botón, la cuenta queda inaccesible y los datos se borran del sistema en el plazo indicado.

### RV-6 · Portabilidad de datos
- **Qué:** la usuaria puede descargar un archivo con todos sus datos (perfil, respuestas, progreso, reseñas).
- **Cómo verificar:** el archivo se genera y contiene los datos completos en formato legible (JSON/CSV).

### RV-7 · Notificaciones push con consentimiento
- **Qué:** la app pide permiso explícito para enviar notificaciones push (no se activan por defecto).
- **Cómo verificar:** al instalar o al activar recordatorios, aparece el diálogo del sistema pidiendo permiso.

### RV-8 · Perfiles de menores bajo responsabilidad del adulto
- **Qué:** al crear un perfil de menor, se informa al adulto de que asume la responsabilidad del tratamiento de esos datos.
- **Cómo verificar:** aparece un aviso claro antes de crear el perfil de menor.

### RV-9 · Datos en la UE
- **Qué:** todos los datos personales se almacenan y procesan en servidores de la Unión Europea.
- **Cómo verificar:** configuración de Supabase y proveedores en región UE (ej. `eu-west-1` o `eu-central-1`).

### RV-10 · Sin datos especiales
- **Qué:** la app no recopila datos de salud, ideología, religión, orientación sexual, origen étnico ni biométricos.
- **Cómo verificar:** revisión del cuestionario y formularios — no incluyen preguntas sobre estas categorías.

---

## 6. DOCUMENTOS LEGALES NECESARIOS

| Documento | Cuándo | Dónde |
|---|---|---|---|
| **Política de privacidad** | Antes del registro | Enlace en registro + ajustes |
| **Términos de uso** | Antes del registro | Enlace en registro + ajustes |
| **Aviso de cookies/trackers** | Si se usa analytics | Banner al primer acceso (solo si aplica) |
| **Registro de actividades de tratamiento** | Obligatorio desde el día 1 | Documento interno (no público) |

---

## 7. RESUMEN DE RIESGOS LEGALES

| Riesgo | Probabilidad | Impacto | Mitigación |
|---|---|---|---|
| **Denuncia por falta de transparencia** | Baja | Medio | Frase de IA automatizada visible (RV-1). |
| **Solicitud de supresión no atendida** | Media | Alto | Botón de eliminación en ajustes (RV-5). |
| **Transferencia de datos fuera de UE** | Media | Alto | Configurar Supabase y proveedores en región UE (RV-9). |
| **Menores sin consentimiento adecuado** | Baja | Alto | Aviso al adulto al crear perfil menor (RV-8). |
| **Notificaciones push sin permiso** | Baja | Medio | Diálogo explícito del sistema (RV-7). |
