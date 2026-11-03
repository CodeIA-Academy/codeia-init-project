# Cómo colaborar

Gracias por querer mejorar `codeia-init-project`. Esta guía explica cómo proponer cambios para que
podamos revisarlos rápido.

Al participar aceptas el [código ético](CODE_OF_CONDUCT.md).

## Antes de empezar

- Busca en los issues abiertos y cerrados por si ya se ha hablado de lo mismo.
- Para cambios grandes (una regla nueva, cambiar el flujo del cuestionario, otra estructura del
  `CLAUDE.md`), abre primero un issue y explica el problema. Así acordamos el enfoque antes de que
  dediques tiempo al cambio.

## Tipos de aportación

- **Reportar un fallo**: qué hiciste, qué esperabas, qué pasó, versión de Claude Code y sistema
  operativo. Si puedes, pega el fragmento del `CLAUDE.md` generado.
- **Proponer una regla**: explica el problema real que evita. Las reglas de esta skill existen porque
  su ausencia costó tiempo, caídas o retrabajo; una regla sin ese motivo difícilmente entrará.
- **Mejorar la redacción o la documentación**: siempre bienvenido.

## Dónde va cada cosa

- `skills/init-project/SKILL.md`: solo el procedimiento (pasos, orden, cómo se pregunta y se escribe).
- `skills/init-project/references/criterios-iniciacion-proyecto.md`: reglas, preguntas del
  cuestionario y plantilla del `CLAUDE.md`. Es la única fuente de las reglas: no las dupliques en
  `SKILL.md`.

## Estilo

- Español de España, frases completas y directas.
- Cada regla debe poder comprobarse: evita formulaciones como «cumplir al 100 %» o «buenas prácticas»
  sin concretar.
- Sin emoticonos.
- Mantén las preguntas del cuestionario agrupadas en rondas de cuatro como máximo.

## Proceso

1. Haz un fork y crea una rama desde `main` con un nombre descriptivo, por ejemplo
   `feat/regla-zonas-horarias` o `fix/marcadores-claude-md`.
2. Haz commits pequeños con [Conventional Commits](https://www.conventionalcommits.org/es/):
   `feat:`, `fix:`, `docs:`, `refactor:`, `chore:`.
3. Prueba el cambio: instala tu versión de la skill (ver el README), ejecuta `/init-project` en un
   proyecto de prueba y comprueba el `CLAUDE.md` generado. Si tocas el modo actualización, pruébalo
   también con `--actualizar`.
4. Abre el pull request explicando qué cambia, por qué y cómo lo has probado.

## Revisión

Revisamos los pull requests lo antes posible. Puede que pidamos cambios; no es nada personal, sino la
forma de mantener las reglas coherentes entre sí.

## Licencia de las aportaciones

Al enviar una aportación aceptas que se publique bajo la [licencia MIT](LICENSE) del proyecto.
