---
name: init-project
description: >-
  Arranca un proyecto con los criterios de iniciación de Codeia: lanza el cuestionario inicial
  (stack, componentes de interfaz, entornos y base de datos, idiomas, puertos, sesión,
  observabilidad, despliegue y flujo Git), aplica las reglas universales (puertos fijos, sin
  diálogos nativos, sin emoticonos ni borde lateral de acento, accesibilidad WCAG 2.2 AA
  verificable, manejo de errores, fechas en UTC, multiidioma, trabajo con agentes) y, tras la
  confirmación del usuario, guarda todo en el CLAUDE.md del proyecto en curso. Úsala cuando el
  usuario escriba /init-project o pida iniciar, configurar o incorporar un proyecto con los
  criterios de Codeia, o actualizar las decisiones de arranque ya guardadas.
argument-hint: "[--actualizar]"
---

# init-project

Lleva el arranque de un proyecto de principio a fin: pregunta, propone, confirma y escribe el
`CLAUDE.md` del proyecto en curso. Todo en español de España.

**Fuente única de las reglas y preguntas:** `references/criterios-iniciacion-proyecto.md`.
Léelo entero antes de empezar. Esta skill solo define el procedimiento; no dupliques aquí reglas.

## Principios del procedimiento

- Nada que dependa de una respuesta se decide por el usuario. Se recomienda, no se impone.
- Preguntas con `AskUserQuestion`: máximo 4 por ronda, opción recomendada primero con
  «(Recomendado)» y una línea de motivo. Cada pregunta se entiende sola, sin contexto previo.
- Lo que el repositorio ya responde se propone como detectado y se confirma, no se pregunta en blanco.
- No se escribe nada en el `CLAUDE.md` hasta la confirmación final (paso 5).
- No se instalan dependencias, ni se crean ramas, ni se toca infraestructura durante esta skill.

## Paso 1 · Reconocimiento (sin preguntar)

Recoge en silencio lo que sirva para proponer respuestas:

1. Raíz del proyecto (`git rev-parse --show-toplevel`, o el directorio actual si no hay git), nombre
   del proyecto y rama principal si hay remoto.
2. `CLAUDE.md` existente:
   - Si contiene el bloque `<!-- init-project:inicio -->` … `<!-- init-project:fin -->`, o se pasó
     `--actualizar`, trabaja en **modo actualización** (ver más abajo).
   - Si existe sin el bloque, se conservará su contenido y el bloque se añadirá.
3. Stack: `package.json` (dependencias, `packageManager`, workspaces), lockfiles, `pyproject.toml`,
   `go.mod`, `Cargo.toml`, `composer.json`, `Dockerfile`, `docker-compose*.yml`, esquemas de ORM.
4. Componentes de interfaz ya en uso (librería UI, toasts, modales, selects, iconos) buscando
   importaciones en el código.
5. Idiomas: librerías de i18n, carpetas de mensajes o traducciones.
6. Puertos: los que declare el proyecto (scripts `dev`, compose, `.env.example`) y los ocupados ahora
   (`lsof -iTCP -sTCP:LISTEN -P -n` y `docker ps --format '{{.Names}} {{.Ports}}'`).
7. Observabilidad y despliegue: SDK de errores, Dockerfile, configuración de plataformas, workflows
   de CI.

No leas archivos de entorno con secretos (`.env`, `.env.local`...); basta con `.env.example`.

## Paso 2 · Cuestionario

Recorre la sección 1 del archivo de criterios en este orden de rondas:

1. Stack (1.1) y flujo Git (1.9).
2. Componentes de interfaz (1.2).
3. Entornos y base de datos (1.3) e idiomas (1.4).
4. Autenticación y sesión (1.6), observabilidad (1.7) y despliegue (1.8).

Con el stack confirmado, propón las buenas prácticas propias de ese stack (5 a 10, concretas y
verificables) y pide confirmación en una pregunta de selección múltiple.

«Sin decidir» es una respuesta válida en despliegue, entornos y observabilidad; queda registrada
como tal.

## Paso 3 · Puertos

1. Propón un puerto fijo por servicio (web, API, BD, caché, almacenamiento...) evitando los ocupados
   y los que ya usen otros proyectos conocidos. Si el proyecto ya declara puertos, respétalos.
2. Pide confirmación de la tabla. Ningún puerto queda asignado sin ella.

## Paso 4 · Resumen para confirmar

Presenta en el chat un resumen breve y legible de todo lo decidido, agrupado en las tres capas
(reglas universales que se aplican, stack y componentes, decisiones del proyecto). Después pregunta
con `AskUserQuestion`:

- «Confirmar y guardar en CLAUDE.md» (Recomendado)
- «Cambiar alguna respuesta» → vuelve solo a las preguntas afectadas y repite este paso.

## Paso 5 · Escribir el CLAUDE.md

1. Si el `CLAUDE.md` existe, haz antes una copia: `CLAUDE.md.bak-AAAAMMDD-HHMM` en la misma carpeta,
   y avisa de que no debe subirse al repositorio.
2. Genera el bloque con la plantilla de la sección 3 del archivo de criterios:
   - **Capa 1:** copia íntegra de las reglas de la sección 2, sustituyendo cada referencia a una
     pregunta (1.x) por la decisión concreta. Ejemplo: «el aviso definido en 1.2» pasa a ser el
     nombre real del componente y su ruta de importación.
   - **Capa 2 y Capa 3:** respuestas confirmadas, sin huecos. «Sin decidir» se escribe tal cual.
   - **Registro de decisiones:** una entrada con la fecha de hoy: «Arranque del proyecto con
     init-project».
3. Escritura:
   - Sin `CLAUDE.md`: créalo con el bloque.
   - Con `CLAUDE.md` sin bloque: añade el bloque al final, sin modificar lo demás.
   - Con bloque existente: sustituye solo lo que hay entre los marcadores.
4. Si alguna regla del bloque contradice algo ya escrito fuera del bloque en ese `CLAUDE.md`, no lo
   resuelvas tú: señálalo al usuario con las dos versiones y pregunta cuál vale.

## Paso 6 · Cierre

Comprueba los criterios de la sección 4 del archivo de criterios y da el resultado en pocas líneas:
ruta del `CLAUDE.md`, copia de seguridad si la hubo, tabla de puertos y cualquier punto «sin
decidir» que quede pendiente. El arranque solo se da por cerrado si se cumplen los cuatro criterios.

## Modo actualización

Cuando el bloque ya existe (o con `--actualizar`):

1. Lee las decisiones actuales del bloque.
2. Pregunta qué apartados se quieren revisar (selección múltiple con los apartados de la sección 1).
3. Haz solo esas preguntas, con la respuesta actual como opción recomendada si sigue siendo válida.
4. Pasos 4 a 6 igual, añadiendo al registro de decisiones una entrada fechada por cada cambio.

Si al actualizar se activa el multiidioma en un proyecto que no lo tenía, aplica la regla 2.9:
antes de cualquier otra cosa, pregunta idioma principal, idiomas, origen de los datos y alcance.
