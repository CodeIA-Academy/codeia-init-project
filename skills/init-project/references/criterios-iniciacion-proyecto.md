# Criterios de iniciación de proyecto de Codeia

Reglas que se aplican al arrancar cualquier proyecto nuevo (o al incorporar uno existente) y que
se mantienen durante todo su desarrollo. Nacen de errores reales ya vividos: cada regla existe
porque su ausencia nos costó tiempo, caídas o retrabajo.

## Cómo se usa este archivo

El arranque de un proyecto sigue siempre tres pasos, en este orden:

1. **Cuestionario inicial** (sección 1). Antes de escribir código o configurar nada, se hacen las
   preguntas. Nada de lo que depende de una respuesta se decide por el usuario.
2. **Reglas universales** (sección 2). Se aplican siempre; algunas se concretan con las respuestas
   del cuestionario.
3. **Cierre: `CLAUDE.md` del proyecto** (secciones 3 y 4). Con todas las respuestas, se genera el
   `CLAUDE.md` del proyecto en tres capas. El arranque no termina hasta que el usuario lo confirma.

---

## 1. Cuestionario inicial

Se hace con la herramienta de preguntas, en rondas cortas (máximo 4 preguntas por ronda), con la
opción recomendada primero y explicada en una línea. Si el repositorio ya responde una pregunta
(por ejemplo, el stack de un proyecto existente), se propone lo detectado y se pide confirmación
en lugar de preguntar en blanco.

### 1.1 Stack
- Frontend, backend, runtime, gestor de paquetes, base de datos y ORM.
- ¿Monorepo o repositorio simple? ¿Hay app móvil o se prevé?
- Con el stack confirmado, se proponen las buenas prácticas que aplican y se registran en la capa
  «Stack» del `CLAUDE.md`. Se irán ampliando con la experiencia del proyecto.

### 1.2 Componentes de interfaz
- Librería de componentes base.
- Componente de **avisos** (toasts).
- Componente de **modal** y de **confirmación** (sustitutos de `alert`, `confirm` y `prompt`).
- Componente de **select / combobox / desplegable** moderno.
- Librería de iconos.

Las respuestas quedan en el `CLAUDE.md` con nombre exacto del componente y su ruta de importación,
para que cualquier agente de interfaz las reciba en su encargo.

### 1.3 Entornos y base de datos
- ¿Qué entornos habrá? (desarrollo, ensayo, producción).
- ¿A qué base de datos apunta el desarrollo local? Recomendación: nunca a producción.
- ¿Cómo se aplican las migraciones en cada entorno y quién las ejecuta?
- ¿Cómo y dónde se hacen las copias de seguridad?

### 1.4 Idiomas
- ¿Será multiidioma desde el inicio? Recomendación: sí, aunque al principio solo haya un idioma;
  añadirlo después cuesta mucho más.
- Idioma principal (el idioma de origen de los datos) e idiomas adicionales previstos.

### 1.5 Puertos
- Se comprueban los puertos en uso (`lsof -i -P | grep LISTEN` y `docker ps`) y se propone un
  puerto fijo para cada servicio del proyecto (web, API, base de datos, caché, almacenamiento...).
- El usuario confirma la tabla de puertos y queda registrada en el `CLAUDE.md`.

### 1.6 Autenticación y sesión
- Mecanismo de autenticación (sesión, JWT, OAuth, proveedor externo).
- Duración de la sesión o del token y si hay renovación automática.

### 1.7 Observabilidad
- Servicio de registro de errores (Sentry u otro) y si se activa en desarrollo.
- Dónde se leen los logs de cada entorno desplegado.

### 1.8 Despliegue e infraestructura
- Plataforma de despliegue y tipo de servidor (Dokploy, Easypanel, Coolify, Vercel, VPS propio...).
  Se admite «sin decidir»; en ese caso nada se ata a una plataforma concreta.
- ¿Se despliega desde CI remoto o con build local? ¿Hay límite de minutos de CI?

### 1.9 Flujo Git
- ¿Se aplica la skill `branching-avanzado`? Recomendación: sí.
- Rama principal (`main` o `master`) y si el repositorio tiene remoto publicado.

---

## 2. Reglas universales

### 2.1 Comunicación
- Respuestas directas, sin conversación de relleno. Cuando proceda, se aporta la solución o el
  procedimiento completo, no solo el diagnóstico.
- Siempre en español de España.
- Cuando haya que decidir, se da una recomendación, no un catálogo de opciones. Nunca se ofrece
  «dejarlo aquí» como opción del siguiente paso.

### 2.2 Puertos y procesos
- Cada servicio del proyecto tiene un **puerto fijo**, asignado en el arranque (1.5) y registrado en
  el `CLAUDE.md`. Ese puerto es del proyecto.
- Si al arrancar el puerto está ocupado por otro proceso (de otro proyecto o un proceso viejo de
  este), **se para ese proceso y se arranca el servicio en su puerto**. Se informa al usuario de qué
  proceso se paró (nombre, PID y proyecto si se conoce). Primero parada limpia (`SIGTERM`); forzada
  solo si no responde.
- **Nunca se cambia el puerto del proyecto, ni se arranca en otro, sin preguntar antes.**
- No se dejan procesos huérfanos: todo proceso en segundo plano que se arranca se registra
  (comando, PID, puerto) y se para al terminar la tarea. Antes de arrancar un servicio se comprueba
  si ya hay uno del mismo proyecto corriendo, para reutilizarlo en lugar de duplicarlo.

### 2.3 Sesión y autenticación
- Cuando la sesión o el token caducan, la aplicación lo detecta en un único punto (interceptor de
  peticiones o middleware) y no en cada pantalla por separado.
- Ante un 401 por caducidad: se limpia la sesión local, se avisa al usuario con un aviso (toast)
  que explica que la sesión ha caducado y se le redirige al login **conservando la URL de retorno**,
  para volver donde estaba tras identificarse.
- Si hay renovación de token, se intenta una sola vez antes de cerrar la sesión.
- Ningún formulario pierde lo escrito por una caducidad silenciosa: si la sesión caduca al enviar,
  el usuario lo sabe antes de perder su trabajo.

### 2.4 Interfaz: prohibiciones
- **Prohibidos los diálogos nativos del navegador**: `alert()`, `confirm()`, `prompt()` y similares.
  Se sustituyen por el modal, la confirmación o el aviso definidos en 1.2.
- **Prohibidos los controles nativos antiguos**: `<select>` nativo y desplegables sin estilo. Se usan
  los componentes modernos definidos en 1.2 (select, combobox, etc.).
- **Prohibidos los emoticonos** en la interfaz. Se usan iconos de la librería definida en 1.2.
- **Prohibido el borde de acento lateral** (por ejemplo `border-l-4` con esquinas redondeadas) en
  avisos, banners, tarjetas o cualquier contenedor. El nivel de un aviso se distingue por fondo,
  color de texto, icono o borde completo uniforme. Si aparece en código existente, se quita.
- Estas prohibiciones se copian en el encargo de cualquier agente que toque interfaz.

### 2.5 Accesibilidad (verificable)
Objetivo: **WCAG 2.2 nivel AA**. Una pantalla no se da por terminada sin cumplir esta lista:
- **Foco visible** en todo elemento interactivo (`:focus-visible` con contraste suficiente); nunca
  `outline: none` sin sustituto.
- **Estados** hover, focus, active y disabled definidos en botones, enlaces, campos y controles.
- **Cursor** `pointer` en todo lo clicable; `not-allowed` en lo deshabilitado.
- **Contraste** mínimo 4.5:1 en texto normal y 3:1 en texto grande e iconos funcionales.
- **Teclado**: todo se puede usar sin ratón, en un orden lógico; los modales atrapan el foco, se
  cierran con Escape y devuelven el foco al elemento que los abrió.
- **Objetivos táctiles** de al menos 44×44 px en móvil.
- **Etiquetas**: cada campo tiene `<label>` asociado; los botones solo con icono llevan
  `aria-label`; las imágenes informativas llevan `alt`.
- **Movimiento**: las animaciones respetan `prefers-reduced-motion`.
- **Comprobación**: auditoría con Lighthouse o axe sin errores de accesibilidad antes de cerrar la
  pantalla, más una pasada manual solo con teclado.

### 2.6 Formularios
- Validación en cliente y en servidor con el mismo esquema cuando el stack lo permita.
- Cada error se muestra **junto al campo que lo provoca**, con un mensaje que dice qué está mal y
  cómo corregirlo. Nunca solo un «Error» o «Algo ha ido mal».
- Si el servidor rechaza el envío, el detalle del rechazo llega al formulario y se muestra; el
  usuario no pierde lo escrito.
- **Móvil**: campos de URL con `type="url"`, de email con `type="email"`, de teléfono con
  `type="tel"`, y `autoCapitalize="off"` / `autoCorrect="off"` donde la mayúscula automática rompa
  el valor (URLs, emails, usuarios, códigos). En servidor se normaliza (por ejemplo, el esquema de
  una URL sin distinguir mayúsculas).

### 2.7 Manejo de errores y observabilidad
El manejo de errores es parte central del producto, no un añadido final.
- **Todo error se registra.** También los de validación (400): un rechazo silencioso sin log es un
  bug invisible.
- Cada error registrado lleva contexto suficiente para reproducirlo: ruta o acción, usuario si lo
  hay, datos de entrada relevantes (sin secretos ni datos personales sensibles) y el detalle.
- Al usuario se le muestra un mensaje concreto y útil; el detalle técnico va al registro.
- Las validaciones y llamadas externas van dentro del manejo de errores: ninguna excepción se
  escapa sin capturar hacia una respuesta 500 genérica.
- El servicio de errores en desarrollo se configura según 1.7 (por defecto, desactivado para no
  ensuciar los datos de producción).
- Antes de diagnosticar un fallo en un entorno desplegado, se leen sus logs reales.

### 2.8 Fechas y zonas horarias
- Las fechas se **guardan en UTC**.
- Se muestran en la zona horaria del usuario, formateadas en el cliente o con la zona explícita.
- En exportaciones (calendarios ICS, APIs, feeds) se emiten en UTC salvo que el formato exija otra
  cosa y esté probado en los clientes reales (Google, Outlook, Apple).

### 2.9 Multiidioma
- Si el proyecto es multiidioma (1.4), se prepara desde el primer día: textos de interfaz fuera del
  código y modelo de datos preparado para traducciones.
- El idioma principal es el origen de los datos. En listados, selectores y pestañas de idiomas, el
  idioma por defecto del sitio va siempre primero.
- Si el proyecto empezó sin multiidioma y se añade después, **lo primero al empezar esa
  implementación es preguntar** idioma principal, idiomas, origen de los datos y alcance, antes de
  tocar código.

### 2.10 Entornos y base de datos
Se concreta con las respuestas de 1.3. Mínimos comunes:
- El desarrollo local nunca apunta a la base de datos de producción.
- **Copia de seguridad antes de cualquier cambio** de esquema o de datos (migración, borrado,
  actualización masiva).
- Ninguna herramienta de migración que pueda resetear datos se ejecuta contra una base real.
- Las migraciones destructivas (borrar tablas o columnas) se aplican **después** de desplegar el
  código que ya no las usa, nunca antes.
- Toda tabla, rol o permiso nuevo revisa los permisos de los roles de base de datos que la usan.
- Operación destructiva sobre producción: solo con confirmación explícita del usuario.

### 2.11 Git y versiones
- El flujo de ramas, versiones, revisión e integración lo define la skill **`branching-avanzado`**.
  No se duplican aquí sus reglas.
- Complementos que no cubre la skill:
  - Cada push o integración en `develop` o en la principal lleva su entrada en el changelog del
    proyecto.
  - Nunca se usa el CI remoto como primera verificación: primero se verifica en local.
  - Commits convencionales, sin referencias a IA ni secretos.

### 2.12 Infraestructura
- Se trabaja con servidores y plataformas de despliegue distintos según el proyecto; la plataforma
  es la que conste en el `CLAUDE.md` (1.8). Si está «sin decidir», nada se ata a una plataforma.
- No se toca la infraestructura de otros proyectos (servidores, bases de datos, almacenamiento,
  contenedores) ni se levanta infraestructura nueva sin pedirlo.
- Excepción expresa: parar el proceso que ocupa el puerto del proyecto (2.2).
- Todo lo que establezca o cambie una ruta operativa (despliegue, logs, credenciales, DNS, copias)
  se documenta.

### 2.13 Trabajo con agentes
- Antes de lanzar un agente se dice qué modelo va a usar. Los modelos se indican con su ID
  explícito, nunca con alias.
- Un solo agente pesado a la vez (suites completas de tests, builds); en paralelo saturan el equipo.
- Antes de relanzar un encargo, se comprueba que no siga en marcha (procesos y `git status`).
- Los planes se validan (predicción, debate o red team) antes de implementar.
- Antes de declarar una fase completa, se repasan sus criterios de aceptación uno a uno.
- Los agentes nunca hacen push, merge ni despliegue si no se les encargó expresamente.

---

## 3. Plantilla del `CLAUDE.md` del proyecto

Con el cuestionario cerrado, se genera el `CLAUDE.md` del proyecto con esta estructura de tres capas:

Todo el bloque generado va entre los marcadores `<!-- init-project:inicio -->` y
`<!-- init-project:fin -->`, para poder actualizarlo en ejecuciones posteriores sin tocar el resto
del archivo.

```markdown
<!-- init-project:inicio -->
# Criterios del proyecto — <nombre del proyecto>

## Capa 1 · Reglas universales
<Las reglas de la sección 2 copiadas íntegras, con las referencias a 1.x sustituidas por la
decisión concreta (por ejemplo, «el aviso definido en 1.2» pasa a ser el componente real).>

## Capa 2 · Stack y componentes
- Stack: <frontend, backend, runtime, BD, ORM, gestor de paquetes>
- Buenas prácticas del stack: <lista propuesta y confirmada>
- Avisos (toasts): <componente y ruta de importación>
- Modal / confirmación: <componente y ruta de importación>
- Select / combobox: <componente y ruta de importación>
- Iconos: <librería>

## Capa 3 · Decisiones del proyecto
- Puertos:
  | Servicio | Puerto |
  |----------|--------|
- Entornos y base de datos: <a qué apunta cada entorno, migraciones, copias>
- Idiomas: <multiidioma sí/no, idioma principal, adicionales>
- Autenticación: <mecanismo, duración de sesión, renovación>
- Observabilidad: <servicio de errores, dónde están los logs>
- Despliegue: <plataforma y tipo de servidor, o «sin decidir»>
- Git: <branching-avanzado sí/no, rama principal, remoto>

## Registro de decisiones
<Fecha y decisión, cada vez que una respuesta del cuestionario cambie.>
<!-- init-project:fin -->
```

---

## 4. Cierre del arranque

El bloque de arranque se da por cerrado con éxito solo cuando:

1. Todas las preguntas de la sección 1 tienen respuesta (o «sin decidir» explícito).
2. El `CLAUDE.md` del proyecto existe con las tres capas completas.
3. La tabla de puertos está comprobada contra los puertos en uso.
4. El usuario ha revisado y confirmado el `CLAUDE.md`.

Si una respuesta cambia más adelante, se actualiza el `CLAUDE.md` y se anota en el registro de
decisiones con la fecha.
