# La CLI `chord` y el compilador

DisChord se usa con **dos ejecutables**. No los confundas:

| Ejecutable | Qué es | Para qué sirve |
|---|---|---|
| **`chord`** | La CLI de DisChord (repo `DischordCLI`). | Crear proyectos, **compilar y ejecutar** bots, actualizar y gestionar librerías. **Es la forma normal de ejecutar un proyecto.** |
| **`dischord-compiler`** | El compilador (repo `DisChord`). Se llama a sí mismo `dischord` en su ayuda. | Compilar/validar con opciones finas (`--check --json`, `--emit`, `--stdin`, `corelib`). `chord compile` y `chord run` lo invocan por debajo. |

Ubicación de lo instalado: Linux/macOS `~/.local/bin/chord` y `~/.local/bin/dischord-compiler`; Windows `~/.dischord/bin/`. Datos en `~/.dischord/` (`cli-version`, `compiler-version`, `libraries/`). Al ejecutar `chord update` los binarios se descargan ahí.

> Si en el repo del compilador (`~/Documentos/Projects/DisChord`) no tienes instalado `dischord-compiler`, el equivalente es `node bin/dischord.js` (o `dischord` tras `npm link`). Es el mismo compilador. En esta skill, cuando aparece `dischord …`, usa el que tengas: `dischord-compiler …` o `dischord …`.

## Ejecutar un proyecto: `chord run`

```bash
chord run src/index.chord
```

1. Comprueba que Node.js está disponible (usa el Node que trae el IDE de DisChord si existe; si no, el `node` del sistema).
2. Compila **todo `src/`** (llama a `chord compile`).
3. Lanza `node dist/index.js` con la raíz del proyecto como directorio de trabajo, y reenvía las señales (`Ctrl+C`) al bot. Sale con el código de salida del bot.

**Esto arranca el bot real contra Discord** (con el token del `encender bot`). No lo ejecutes sin que el usuario lo pida y sin un token válido.

Requisitos que `chord run` comprueba y avisa con error en rojo:

- La ruta debe estar **dentro de una carpeta `src/`**.
- Debe existir `dist/index.js` tras compilar → hace falta `src/index.chord`.

## Compilar: `chord compile`

```bash
chord compile src/index.chord
```

Compila el proyecto a `dist/` **sin ejecutarlo** (llama a `dischord-compiler <ruta> --no-run`). Antes asegura que el proyecto es ejecutable: crea el `package.json` si falta (`pnpm init`), le pone `"type": "module"` y, si no hay `node_modules`, instala las dependencias (`pnpm install`).

Reglas de la ruta (comprobadas):

| Ruta | Resultado |
|---|---|
| `chord compile src/index.chord` | ✅ compila **todo** `src/` (incluye `comandos/` y `eventos/`). |
| `chord compile src` (la carpeta) | ❌ `Se esperaba una extensión '.chord'`. Pasa un archivo. |
| `chord compile src/comandos/hi.chord` | ❌ `No se ha encontrado la carpeta src. Se encontró 'comandos'`. El archivo debe estar **directamente** en `src/`. |
| `chord compile suelto.chord` (fuera de `src/`) | ❌ `No se ha encontrado la carpeta src`. |

Conclusión práctica: usa siempre **`src/index.chord`** como argumento de `chord compile` y `chord run`.

## Crear un proyecto: `chord init <ruta>`

```bash
chord init mi-bot
```

Crea la carpeta con: `package.json` (con `pnpm init` y `"type": "module"`), `seyfert` instalado, `src/index.chord` (solo un comentario) y un `.gitignore` con `node_modules`, `package.json`, `pnpm-lock.yaml`, `seyfert.config.mjs`, `dist` y `lib`.

- Si la ruta **ya existe** responde `Ya existe un proyecto en esa ruta.` y no hace nada. Por el código, `chord init .` (carpeta actual, que existe) cae en ese caso: usa una ruta nueva.
- Necesita `pnpm` y red (instala `seyfert`).
- **No crea `src/comandos/`, `src/eventos/` ni `src/componentes/`**: créalas tú (ver [bots.md](bots.md#estructura-del-proyecto-obligatoria)). El `src/index.chord` inicial hay que reescribirlo con el `encender bot`.

## Actualizar: `chord update`

```bash
chord update cli          # actualiza la CLI (chord)
chord update compiler     # actualiza dischord-compiler
chord update all          # ambos
chord update all -f       # --force: reinstala aunque la versión sea la misma
chord update all --json   # salida NDJSON (una línea JSON por evento) para integraciones como el IDE
```

Compara la versión instalada (`~/.dischord/*-version`) con la del servidor de DisChord, descarga el binario y lo reemplaza. Si el binario no existe, lo instala.

## Librerías: `chord pkg`

Las librerías oficiales (escritas en DisChord) se instalan **una vez en el sistema** (`~/.dischord/libraries/<nombre>/<versión>/`) y se **enlazan** a cada proyecto (`<proyecto>/lib/<nombre>`) con un enlace simbólico.

| Comando | Qué hace |
|---|---|
| `chord pkg search [consulta]` | Busca en el registro oficial (por nombre, descripción, repositorio, nivel de confianza o versión exacta). `-i, --installed` busca solo en lo instalado. `--json` para integraciones. |
| `chord pkg install <paquete...>` | Descarga e instala. `nombre` toma la última versión; `nombre@v1.0.3` una concreta. Verifica la firma PGP: si es inválida **aborta**; si no está firmado, avisa y sigue bajo tu riesgo. Instala sus dependencias y compila sus `.chord`. |
| `chord pkg use <nombre> <versión>` | Enlaza esa versión en `./lib/<nombre>` y la apunta en `dischord.lock.conf`. Debe estar instalada. |
| `chord pkg unuse <nombre>` | Quita el enlace de `./lib/<nombre>` del proyecto. |
| `chord pkg uninstall <nombre> <versión>` | Borra esa versión del sistema (global). |
| `chord pkg sync` | Lee `dischord.lock.conf`, instala lo que falte y lo enlaza (equivale a `npm install`). También instala `seyfert`. |

Todos aceptan `--json` (NDJSON) para el IDE. Ejecuta `use`/`unuse`/`sync` **dentro de la raíz del proyecto** (usan el directorio actual). En Windows los enlaces necesitan "Modo Desarrollador" o administrador.

`dischord.lock.conf` es texto plano, una línea por librería:

```
ent: v2.0.0;
sf: v1.0.1;
```

### Usar una librería en el código

Una vez enlazada, se importa con el prefijo `lib:` (se resuelve a `<proyecto>/lib/<nombre>/src/index.js`):

```chord
importar { ent } desde "lib:ent"
importar ent                      // atajo de: importar ent desde "lib:ent"

consola.imprimir(ent.TOKEN_DISCORD)
```

Si la librería no está enlazada, el compilador da `No se encontró el módulo 'lib:x'` → ejecuta `chord pkg use` o `chord pkg sync`.

### Librerías oficiales vistas en esta máquina

| Librería | Qué ofrece |
|---|---|
| `ent` | Variables de entorno: carga `.env` (o `.ent`) al importar y las expone como `ent.NOMBRE`. Para tokens: `token ent.TOKEN_DISCORD`. |
| `sf` | Sistema de archivos (async): `existe`, `info`, `leer`, `escribir`… sobre `node:fs`. |
| `rutas` | Manejo de rutas (`dirname`, `basename`, `extname`, `normalizar`…) sobre `node:path`. |
| `internet` | Peticiones HTTP/DNS (async): `obtener`, `enviar`, `peticion`… sobre `fetch`. |
| `procesos` | Procesos y SO (async): `ejecutar`, `ejecutarArchivo`, `lanzar`… sobre `node:child_process`. |

Para ver el catálogo real y la API de cada una: `chord pkg search` y el `README.md` en `~/.dischord/libraries/<nombre>/<versión>/`. Las librerías tienen nivel de confianza y marca de auditoría en el registro: avisa al usuario antes de instalar una no oficial o sin firmar.

## Opciones del compilador (`dischord-compiler`)

Las que no expone `chord`. Útiles para validar y depurar:

| Opción | Efecto |
|---|---|
| `--check` | Corre todas las fases sin escribir ni ejecutar. |
| `--json` | Diagnósticos en JSON (con `--check` para editores). |
| `--no-run` | Compila sin ejecutar el bot. |
| `--stdin`, `--stdin-filepath <ruta>` | Compila el código de la entrada estándar como si fuera ese archivo. |
| `--emit tokens\|ast` | Imprime tokens o árbol sintáctico en JSON. |
| `-o, --out-dir <dir>` | Carpeta de salida (aun así escribe `dist/`/`seyfert.config.mjs` de apoyo en la raíz). |
| `corelib [--json]` | Vocabulario del lenguaje. |
| `-v, --version` | Versión del compilador. |

Códigos de salida: `0` ok · `1` el código tiene errores · `2` la herramienta no pudo hacer su trabajo.

## Qué comando usar

| Quiero… | Comando |
|---|---|
| Validar el código sin tocar nada | `dischord-compiler src --check --json` |
| Compilar sin arrancar el bot | `chord compile src/index.chord` |
| Ejecutar el bot | `chord run src/index.chord` *(arranca el bot real)* |
| Crear un proyecto | `chord init <ruta>` |
| Tener las últimas herramientas | `chord update all` |
| Instalar y usar una librería | `chord pkg install <n>` → `chord pkg use <n> <versión>` |
| Reproducir las librerías de un proyecto | `chord pkg sync` |
