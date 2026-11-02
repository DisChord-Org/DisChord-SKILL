# Capa dischord: bots de Discord

Traduce a [Seyfert](https://github.com/tiramisulabs/seyfert). Proyecto de ejemplo completo y validado: [`examples/proyecto-bot/`](../examples/proyecto-bot).

## Estructura del proyecto (OBLIGATORIA)

Seyfert solo carga comandos y eventos de carpetas con nombre fijo. DisChord compila cada `src/<ruta>.chord` a `dist/<ruta>.js` (misma ruta relativa a `src/`) y genera `seyfert.config.mjs` con `base: "dist"`, `commands: "comandos"`, `events: "eventos"`, `components: "componentes"`. Por eso la ubicación **no es libre**:

```
mi-bot/
├─ package.json              # "type": "module"  (obligatorio)
├─ seyfert.config.mjs        # lo genera el compilador, no lo escribas
├─ src/
│  ├─ index.chord            # punto de entrada: aquí va `encender bot { … }`
│  ├─ comandos/              # TODOS los nuevo comando — 1 por archivo
│  │  ├─ ping.chord
│  │  └─ util/hora.chord     # subcarpetas permitidas
│  ├─ eventos/               # TODOS los nuevo evento — 1 por archivo
│  │  └─ listo.chord
│  └─ componentes/           # reservada por Seyfert: déjala vacía (ver más abajo)
└─ dist/                     # salida compilada; no la edites
```

| Qué | Dónde va | Qué pasa si lo pones en otro sitio |
|---|---|---|
| `encender bot` | `src/index.chord` | `dischord src` ejecuta `dist/index.js`; sin él no arranca el bot. |
| `nuevo comando` | `src/comandos/…` | En `src/mal.chord` compila a `dist/mal.js`, **Seyfert no lo detecta** y el comando no existe en Discord. Sin ningún error del compilador. |
| `nuevo evento` | `src/eventos/…` | Igual: no se registra. |
| funciones, clases, config compartidas | `src/` (p. ej. `src/utilidades/…`) | Se importan con `importar { x } desde "../utilidades/x"`. |
| `nuevo boton` / `nuevo embed` | **dentro del archivo del comando/evento que los usa** | En un módulo aparte (incluida `componentes/`) compilan pero **fallan al cargar** con `Button is not defined`. |

Reglas:

- **Nunca crees comandos o eventos directamente en `src/`** ni en una carpeta distinta a `src/comandos/` o `src/eventos/` (ni `comandos/` en la raíz del proyecto: debe colgar de `src/`).
- Los nombres de carpeta están en español y son exactos: `comandos`, `eventos`, `componentes`.
- Un `nuevo comando` **o** un `nuevo evento` por archivo; un solo `encender bot` (en `index.chord`).
- `src/componentes/` es la carpeta de componentes de Seyfert (`components: "componentes"` en `seyfert.config.mjs`). El compilador la crea siempre en `dist/`, pero DisChord **no genera nada para ella**: los botones se manejan con `recolector`. Créala vacía (con un `.gitkeep` si usas git) y **no pongas ahí `nuevo boton` ni `nuevo embed`**.
- **Declara `nuevo boton` y `nuevo embed` dentro del archivo del comando/evento que los usa.** Comprobado: un módulo que solo contiene esas declaraciones se compila a `export let X = new Button()…` **sin** `import { Button } from 'seyfert'`, así que al importarlo (o al cargarlo Seyfert desde `componentes/`) lanza `ReferenceError: Button is not defined`. El compilador no lo detecta. Si dos comandos necesitan el mismo botón, repite la declaración en cada uno.
- Al compilar un directorio o cualquier archivo dentro de `src/`, el compilador procesa **todos** los `.chord` de `src/`, borra del `dist/` los `.js` obsoletos y crea `dist/comandos`, `dist/eventos` y `dist/componentes` aunque estén vacías.
- Un archivo suelto **fuera** de una carpeta `src/` se compila como script independiente: no sirve para un bot.

### Comandos de proyecto

```bash
chord run src/index.chord                 # compila y ejecuta el bot (arranca el bot REAL)
chord compile src/index.chord             # compila a dist/ sin arrancar
dischord-compiler src --check --json      # valida TODO el proyecto sin escribir nada
```

`chord` exige que le pases un **archivo** directamente dentro de `src/` (usa `src/index.chord`), nunca la carpeta ni `src/comandos/x.chord`. El compilador (`dischord-compiler`) sí acepta la carpeta `src`. Todos los detalles en [cli.md](cli.md).

### Cuando el usuario pida "un comando"

1. Si no existe, crea la estructura: `package.json` (`"type": "module"`), `src/index.chord` con `encender bot`, `src/comandos/`, `src/eventos/`.
2. Crea el archivo en `src/comandos/<nombre>.chord` (o `src/eventos/<nombre>.chord`).
3. Valida con `dischord-compiler src --check --json` (o `dischord src --check --json`).
4. Comprueba la salida (`dischord src --no-run` y mira que exista `dist/comandos/<nombre>.js`) cuando quieras estar seguro de que quedará registrado.
5. Si el proyecto ya existe, **respeta su estructura**: mira dónde están los comandos que ya hay antes de crear uno nuevo.

## Regla de los bloques (afecta a todo)

Un bloque `nombre { … }` tiene **propiedades primero, sin separadores, y el código después de una línea en blanco**:

```chord
nuevo comando Ping {
    descripcion "Latencia"          // propiedades
    nsfw falso

    consola.imprimir("hola")        // código (tras la línea en blanco)
    enviar mensaje { contenido "Pong" }
}
```

- Cada propiedad solo puede aparecer **una vez**.
- Los **campos cerrados** (`tipo`, `estilo`, `intenciones`, `contextos`, `integraciones`, `ignorar`) solo aceptan **literales de texto** de la lista válida; no variables ni `"{x}"`.
- `{{ }}` escapa llaves literales (`descripcion "usa {{nombre}}"`).

## `encender bot`

```chord
encender bot {
    token "TU_TOKEN"            // obligatorio; puede ser una variable/import (token TOKEN)
    prefijo "!"                 // obligatorio (alias: prefijos); puede ser una lista
    intenciones [ "MensajesDelServidor", "ContenidoDelMensaje" ]
}
```

Intenciones válidas: ConfiguracionDelAutomoderador, EjecucionDelAutomoderador, EncuestasPorPrivado, ReaccionesPorPrivado, EscribiendoPorPrivado, MensajesDirectos, ExpresionesDelServidor, IntegracionesDelServidor, InvitacionesDelServidor, MiembrosDelServidor, EncuestasDelServidor, ReaccionesEnServidor, EscribiendoEnElServidor, MensajesDelServidor, ModeracionDelServidor, EstadosDelServidor, EventosProgramadosDelServidor, EstadosDeVozDelServidor, WebhooksDelServidor, Servidores, ContenidoDelMensaje.

Elige solo las necesarias: leer texto de mensajes (`mensaje.contenido`, comandos con prefijo) exige `MensajesDelServidor` + `ContenidoDelMensaje`; eventos de miembros exigen `MiembrosDelServidor`; etc.

**Seguridad del token**: no lo escribas en repos públicos ni lo pegues en respuestas. Guárdalo en un `.env` y léelo con la librería oficial `ent` (`importar ent` → `token ent.TOKEN_DISCORD`; instalación en [cli.md](cli.md#librerías-chord-pkg)), o con un import local. Dile al usuario que lo rote si se expuso.

## `nuevo comando`

```chord
nuevo comando Avatar {
    descripcion "Muestra el avatar"          // obligatorio
    nsfw falso
    contextos [ "Servidor", "DM", "CanalPrivado" ]
    integraciones [ "Servidor", "Usuario" ]
    servidoresPermitidos [ "123" ]
    ignorar "Mensajes"                       // o "Slashs": desactiva esa vía de invocación
    alias [ "av", "foto" ]

    opciones {
        persona {
            tipo "usuario"                   // texto entero booleano usuario canal rol mencionable numero archivo
            descripcion "A quién"
            requerido verdadero
        }
    }

    enviar mensaje { contenido "Hola {persona.nombre}" }
}
```

- El nombre del comando: 1–32 caracteres, se admiten Unicode/acentos/ñ. Los **nombres de opción** no pueden tener mayúsculas y también 1–32.
- Cada opción se convierte en **variable** con su nombre dentro del comando (`persona`, `cantidad`…). Con tipo: `var n tipo numero es cantidad`.
- Opciones `tipo "usuario"` dan un `Usuario` (`persona.nombre`, `persona.avatarUrl`, `persona.colorPerfil`, `persona.esBot`…).
- No nombres una opción `cliente`, `contexto` ni `ctx` (chocan con nombres internos) ni como un nombre inyectado del comando.
- Dentro del comando están disponibles `usuario` (quien lo ejecuta), `canal` y `cliente`. `ctx`/`contexto` son internos y **no se pueden usar**.
- Puedes declarar dentro del comando `nuevo embed` y `nuevo boton` locales, variables y bucles; no puedes declarar variables con nombres que el comando ya define.
- Los comandos son implícitamente async: `esperar(...)` y `enviar mensaje` se usan directamente.

## `nuevo evento`

```chord
nuevo evento mensajeCreado {
    si (mensaje.contenido igual "ping") {
        enviar mensaje { contenido "pong" }
    }
}
```

Se puede escribir `evento X` sin `nuevo`. El nombre decide los parámetros inyectados:

| Evento | Variables inyectadas |
|---|---|
| `encendido` | `usuario`, `cliente` |
| `mensajeCreado` | `mensaje`, `cliente` |
| `mensajeBorrado` | `mensaje`, `mensajeBorrado`, `cliente` |
| `mensajeEditado` | `mensaje`, `mensajeEditado`, `cliente` |
| `canalCreado` · `canalBorrado` · `canalEditado` | `canal`, `cliente` |
| `ban` · `unban` | `miembro`, `cliente` |
| `invitado` · `expulsado` | `servidor`, `cliente` |
| `entradaMiembro` · `idaMiembro` | `miembro`, `cliente` |
| `rolCreado` · `rolBorrado` · `rolEditado` | `rol`, `cliente` |
| `limitado` | `ratelimit` |
| `interaccion` | `interaccion`, `cliente` |

- Esas variables existen **solo dentro del evento** (no en funciones sueltas ni a nivel superior). Para usarlas en una función, pásalas como argumento: `f(usuario)`.
- No las redeclares (`var mensaje es 1` dentro del evento es error; fuera de comandos/eventos sí se permite como variable normal).
- `enviar mensaje` en un evento de mensaje responde en ese canal; en otros eventos indica `canal "id"` o `usuario "id"`.
- Nombre de evento inexistente → `El evento 'x' no existe`.

## Mensajes — `enviar mensaje`

```chord
var msg es enviar mensaje {
    contenido "Hola"          // texto, admite {interpolación}
    canal "id-del-canal"      // destino alternativo (por defecto: donde se ejecutó)
    usuario "id-del-usuario"  // enviar por DM
    embed { … }               // o un nombre declarado, o una lista (ver abajo)
    boton { … }               // o nombre, o lista de botones/filas
}
```

- Propiedades válidas: `canal`, `usuario`, `contenido`, `embed`, `boton`. Cualquier otra → error.
- `var msg es enviar mensaje { … }` guarda el mensaje enviado (necesario para un `recolector`).

### Embeds

```chord
nuevo embed Bienvenida {
    color 0x5865F2
    titulo "Bienvenido"
    descripcion "Gracias por unirte"
    imagen "https://…/img.png"
    cartel "https://…/thumb.png"
    campos [
        { titulo "Reglas" descripcion "Sé amable" lineado verdadero }
    ]
    pie { texto "DisChord Bot v1.0" icono "https://…/logo.png" }
}

enviar mensaje {
    embed Bienvenida                              // uno por nombre
}
enviar mensaje {
    embed [ { titulo "Uno" }, Bienvenida ]        // varios: UNA propiedad `embed` con lista
}
```

Campos de `embed`: `color`, `titulo`, `descripcion`, `imagen`, `cartel`, `campos` (lista de `campo`: `titulo` requerido, `descripcion`, `lineado`), `pie` (`texto` requerido, `icono`).

### Botones

```chord
nuevo boton Aceptar {
    id "aceptar"                 // obligatorio
    etiqueta "Aceptar"           // obligatorio
    estilo "verde"               // obligatorio: azul gris verde rojo enlace premium (o 1–6)
    emoji "✅"
}

enviar mensaje {
    boton Aceptar                         // uno
    boton [ Aceptar, Rechazar ]           // varios: se reparten solos en filas de 5
    boton [                               // filas manuales (máx. 5 por fila)
        [ { id "1" etiqueta "1" estilo "verde" } ],
        [ { id "2" etiqueta "2" estilo "gris" } { id "3" etiqueta "3" estilo "rojo" } ]
    ]
}
```

Límites de Discord: 5 botones por fila (el compilador lo valida), y el estilo se escribe como **texto literal**.

## Recolector de botones

```chord
funcion alAceptar(interaccion, cliente) { imprimir("aceptado") }

nuevo comando Confirmar {
    descripcion "Pide confirmación"

    var msg es enviar mensaje {
        contenido "¿Seguro?"
        boton Aceptar
    }

    recolector msg {
        filtro soloAutor                  // opcional: funcion(interaccion, cliente, contexto) -> booleano
        tiempo 30000                      // opcional: ms hasta cerrar
        alFinalizar cuandoTermine         // opcional: funcion(razon, reiniciar, cliente, contexto)
        alPulsarId {
            aceptar alAceptar             // idDelBoton  funcionCallback(interaccion, cliente[, contexto])
        }
    }
}
```

Reglas validadas por el compilador:

- `recolector` se escribe **sin** `nuevo`.
- El primer argumento es una variable que contiene un `enviar mensaje` del mismo comando (no vale `var msg es crear()` de una función).
- Cada clave de `alPulsarId` debe coincidir con el `id` **fijo** de un botón del mensaje. Si el mensaje no tiene botones o el id no existe: `Ids disponibles: …`.
- Ids dinámicos (`"b{n}"`) no se pueden enlazar con claves fijas.
- Los callbacks son funciones declaradas aparte (no hay lambdas).

## Canales

```chord
var c es crear canal {
    servidor "id"          // obligatorio solo en eventos sin servidor implícito
    nombre "avisos"        // obligatorio
    tipo "texto"           // texto voz categoria anuncios escenario foro multimedia
    tema "Novedades"
    nsfw falso
    categoria "id"
    posicion 1
    modoLento 5
    bitrate 64000
    limiteUsuarios 10
}

editar canal {
    canal canal            // obligatorio: id o el canal inyectado
    nombre "novedades"
    razon "renombrado"
}

borrar canal {
    canal canal
    razon "limpieza"       // OBLIGATORIA
}

var uno es obtener canal { id "123456789" }
var voces es listar canales { servidor "1" tipo "voz" categoria "9" }
```

### Barreras de seguridad (no desactivables)

1. **Nunca dentro de un bucle** (`para`) → error de compilación.
2. **No en el evento que las dispara** (`canalCreado`/`Editado`/`Borrado`) → error `lo dispararía a sí mismo`.
3. **`borrar` exige `razon`**; `crear` no admite `razon`.
4. Solo dentro de **comando o evento** (no en nivel superior ni en funciones, aunque sean `@asincrono`).
5. El canal debe pertenecer al servidor del contexto.
6. En ejecución: se comprueban los permisos de quien ejecuta el comando y hay una **cuota por servidor** que crece con el número de servidores del bot.

`obtener canal` y `listar canales` son lecturas: sí pueden ir en bucles, pero también solo dentro de comando/evento.

Las palabras `crear`, `editar`, `borrar` solas siguen siendo identificadores válidos (`var crear es 1`); solo son acción cuando van seguidas de `canal` y un bloque.

## Extensiones sobre objetos de Discord

Con contexto de Discord (comando/evento) los objetos `usuario`, `miembro`, `canal`, `servidor`… ganan propiedades en español (`nombre`, `avatarUrl`, `colorPerfil`, `tema`, `categoria`, `enlace`…). Ver clases `Usuario`, `Canal`, `cliente` en [corelib.md](corelib.md). En funciones sin contexto (`funcion f(u) { devolver u.nombre }`) el compilador no sabe el tipo de `u`: pásale un objeto de Discord solo desde un comando/evento.

## Checklist antes de entregar un bot

1. Un comando/evento por archivo, un solo `encender bot`.
2. Cada bloque: propiedades → línea en blanco → código.
3. Campos cerrados con literales válidos.
4. Botones con `id`, `etiqueta`, `estilo`; recolectores con ids fijos coincidentes.
5. Sin acciones de canal en bucles ni en el evento que las dispara; `razon` en `borrar`.
6. Token fuera del código compartido.
7. `dischord <archivo> --check --json` en **cada** archivo → `ok:true`.
8. Avisa de que no se probó contra Discord real.
