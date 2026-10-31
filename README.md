# DisChord Skill

Skill para Claude que le enseña a escribir, depurar y explicar código [DisChord](https://github.com/DisChord-Org/DisChord): el lenguaje con sintaxis en español que compila a JavaScript y, con su capa de bots, a bots de Discord sobre Seyfert.

Con la skill instalada, Claude conoce la sintaxis (`es`, `si`, `para`, `mas`, `igual`…), el sistema de tipos, la asincronía automática, los comandos, eventos, botones, embeds y canales, y valida su código con el compilador antes de entregarlo.

## Instalación

Clona el repositorio dentro de tu carpeta de skills:

```bash
git clone https://github.com/DisChord-Org/DisChord-SKILL.git ~/.claude/skills/dischord
```

Claude carga la skill cuando la petición menciona DisChord, archivos `.chord` o el comando `dischord`.

## Requisitos

Para que Claude pueda validar el código necesita el compilador de [DisChord](https://github.com/DisChord-Org/DisChord) (`dischord` en el `PATH`, o el repositorio clonado). El proyecto donde se compile debe tener un `package.json` con `"type": "module"`.

## Contenido

| Ruta | Qué contiene |
| :--- | :--- |
| `SKILL.md` | Instrucciones principales: flujo de trabajo, reglas de oro y referencia del lenguaje base. |
| `references/tipos.md` | Sintaxis de tipos, qué comprueba el compilador y cómo infiere. |
| `references/asincronia.md` | `@asincrono` y el `await` automático. |
| `references/bots.md` | Capa de Discord: bot, comandos, eventos, mensajes, botones, recolectores y canales. |
| `references/errores.md` | Catálogo de errores del compilador con su causa y arreglo. |
| `references/corelib.md` | Biblioteca estándar con firmas completas, generada de `dischord corelib --json`. |
| `examples/` | Archivos `.chord` de ejemplo; todos pasan `dischord --check`. |

## Cómo se validó

Los ejemplos se comprobaron con el compilador de DisChord v1.8.0:

```bash
dischord examples/01-basico.chord --check --json
```

`references/corelib.md` sale de `dischord corelib --json`. Si el compilador cambia, regenera ese archivo con el mismo comando y vuelve a validar los ejemplos.

## Limitaciones

- La skill se escribió contra la versión 1.8.0 del compilador; versiones posteriores pueden cambiar la sintaxis.
- Los bots de ejemplo solo se compilaron; no se ejecutaron contra Discord.
