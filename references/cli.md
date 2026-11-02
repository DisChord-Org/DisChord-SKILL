# `chord`, el compilador y las librerías

Dos ejecutables: **`chord`** (CLI: crear, compilar, ejecutar, actualizar, librerías) y **`dischord-compiler`** (el compilador; `chord compile/run` lo invocan). En el repo del compilador es `node bin/dischord.js` (`dischord`). Instalados en `~/.local/bin` (Windows: `~/.dischord/bin`); datos en `~/.dischord/`.

| Comando | Qué hace |
|---|---|
| `chord init <ruta>` | Crea `package.json`, instala `seyfert`, `src/index.chord` (vacío) y `.gitignore`. **No** crea `comandos/`/`eventos/`/`componentes/`: créalas. Falla si la ruta existe (incluida `.`). Necesita `pnpm` y red. |
| `chord compile src/index.chord` | Compila todo `src/` a `dist/` sin ejecutar. Instala dependencias si falta `node_modules`. |
| `chord run src/index.chord` | Compila y ejecuta `dist/index.js` con Node. **Arranca el bot real.** |
| `chord update cli\|compiler\|all [-f] [--json]` | Actualiza las herramientas. |
| `chord pkg …` | Librerías (abajo). |

**Rutas de `chord compile/run`**: siempre un archivo `.chord` **directamente dentro de `src/`** (usa `src/index.chord`). La carpeta `src` falla (`Se esperaba una extensión '.chord'`); `src/comandos/x.chord` y archivos fuera de `src/` fallan (`No se ha encontrado la carpeta src`). Hace falta `src/index.chord` o no hay `dist/index.js`.

## Opciones del compilador

`dischord-compiler <src|archivo> [--check] [--json] [--no-run] [--stdin --stdin-filepath <ruta>] [--emit tokens|ast] [-o <dir>]` · `dischord-compiler corelib [--json]` (vocabulario completo). `--check --json` valida sin escribir ni ejecutar. Salida: `0` ok · `1` errores en el código · `2` fallo de la herramienta. Para depurar: `--emit tokens` (Lexer) / `--emit ast` (Parser).

## Librerías (`chord pkg`)

Se instalan una vez en `~/.dischord/libraries/<nombre>/<versión>/` y se enlazan al proyecto en `./lib/<nombre>`.

| Comando | Qué hace |
|---|---|
| `chord pkg search [consulta] [-i]` | Busca en el registro (`-i`: solo instaladas). |
| `chord pkg install <nombre>[@v1.0.0]…` | Descarga y verifica la firma PGP (inválida → aborta; sin firma → avisa y sigue). |
| `chord pkg use <nombre> <versión>` | Enlaza en `./lib` y anota en `dischord.lock.conf` (`nombre: v1.0.0;`). |
| `chord pkg unuse <nombre>` · `uninstall <nombre> <versión>` | Quita el enlace del proyecto · borra del sistema. |
| `chord pkg sync` | Instala y enlaza todo lo del lock (como `npm install`). Ejecútalo en la raíz del proyecto. |

Todos aceptan `--json` (NDJSON para el IDE). Uso en el código (se resuelve a `lib/<nombre>/src/index.js`):

```chord
importar ent                      // atajo de: importar { ent } desde "lib:ent"
token ent.TOKEN_DISCORD           // dentro de `encender bot`
```

Si no está enlazada: `No se encontró el módulo 'lib:x'` → `chord pkg use` o `sync`. Oficiales: `ent` (carga `.env`/`.ent` y expone `ent.NOMBRE`), `sf` (archivos, async), `rutas` (rutas), `internet` (HTTP/DNS, async), `procesos` (procesos, async). La API de cada una está en su `README.md` dentro de la carpeta instalada. Avisa al usuario antes de instalar algo no oficial o sin firmar.
