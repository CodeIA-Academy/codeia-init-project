# codeia-init-project

Skill para [Claude Code](https://docs.claude.com/en/docs/claude-code) que arranca un proyecto con los
**criterios de iniciación de Codeia**. Con un solo comando, `/init-project`:

1. Reconoce el proyecto (stack, componentes de interfaz, idiomas, puertos en uso, `CLAUDE.md` existente).
2. Hace un cuestionario corto en rondas: stack, componentes de interfaz, entornos y base de datos,
   idiomas, puertos, autenticación, observabilidad, despliegue y flujo Git.
3. Te enseña un resumen de todo lo decidido y espera tu confirmación.
4. Guarda las reglas y las decisiones en el `CLAUDE.md` del proyecto, en tres capas: reglas
   universales, stack y componentes, y decisiones del proyecto.

Las reglas nacen de errores reales en proyectos de Codeia: puertos fijos por proyecto, nada de
diálogos nativos del navegador ni `<select>` antiguos, sin emoticonos ni bordes laterales de acento,
accesibilidad WCAG 2.2 AA comprobable, manejo de errores con detalle, fechas en UTC, multiidioma
desde el inicio, separación de entornos y buenas prácticas al trabajar con agentes.

## Requisitos

- Node.js 18 o superior (para `npx`).
- Un agente compatible con skills: Claude Code, Codex, Cursor, OpenCode y
  [muchos más](https://github.com/vercel-labs/skills/blob/main/docs/agents.md).

## Instalación

```bash
npx skills add CodeIA-Academy/codeia-init-project
```

El instalador te pregunta en qué agentes quieres instalarla y si la quieres para el proyecto actual o
para todos tus proyectos. No hace falta clonar nada.

Si prefieres indicarlo directamente:

```bash
# Solo en Claude Code, para todos tus proyectos
npx skills add CodeIA-Academy/codeia-init-project -a claude-code -g

# En varios agentes, solo para el proyecto actual
npx skills add CodeIA-Academy/codeia-init-project -a claude-code -a codex -a cursor
```

Abre una sesión nueva del agente para que cargue la skill. En Claude Code, comprueba que aparece
escribiendo `/init-project` en el prompt.

## Uso

Desde la raíz del proyecto, en Claude Code:

```text
/init-project
```

Para revisar decisiones de un proyecto ya iniciado:

```text
/init-project --actualizar
```

La skill no escribe nada hasta que confirmas el resumen. Si el proyecto ya tenía `CLAUDE.md`, guarda
antes una copia (`CLAUDE.md.bak-AAAAMMDD-HHMM`) y conserva el contenido existente. Todo lo que añade va
entre los marcadores `<!-- init-project:inicio -->` y `<!-- init-project:fin -->`, para poder
actualizarlo después sin tocar el resto.

### Flujo Git

El cuestionario pregunta si se usa la skill `branching-avanzado` para ramas y versiones. Si no la
tienes instalada, responde que no y la skill dejará registrado el flujo Git que indiques.

## Actualizar

```bash
npx skills check            # comprueba si hay versión nueva
npx skills update init-project
```

## Desinstalar

```bash
npx skills remove init-project
```

## Estructura

```text
skills/init-project/
├── SKILL.md                                    # Procedimiento que sigue Claude
└── references/
    └── criterios-iniciacion-proyecto.md        # Reglas, cuestionario y plantilla del CLAUDE.md
```

Las reglas y preguntas viven solo en `references/criterios-iniciacion-proyecto.md`. Para adaptarlas
a tu equipo, edita ese archivo.

## Colaborar

Las propuestas son bienvenidas. Lee [CONTRIBUTING.md](CONTRIBUTING.md) antes de abrir un issue o un
pull request, y respeta el [código ético](CODE_OF_CONDUCT.md).

## Licencia

[MIT](LICENSE) © 2026 Codeia
