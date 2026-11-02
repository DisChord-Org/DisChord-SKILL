# Bots de Discord (capa dischord)

Ejemplo completo y validado: [`examples/proyecto-bot/`](../examples/proyecto-bot). Estructura obligatoria de carpetas: ver SKILL.md (comandos en `src/comandos/`, eventos en `src/eventos/`, bot en `src/index.chord`; el resto compila pero Seyfert no lo carga).

## Bloques

Propiedades primero, sin separadores, **una sola vez cada una**; el código va tras una línea en blanco. Los **campos cerrados** (`tipo`, `estilo`, `intenciones`, `contextos`, `integraciones`, `ignorar`) solo aceptan **literales de texto** de su lista, sin variables ni `{}`. Llaves literales: `{{x}}`.

```chord
encender bot {
    token "TOKEN"          // obligatorio; puede ser una variable/import (token ent.TOKEN)
    prefijo "!"            // obligatorio (o prefijos)
    intenciones [ "MensajesDelServidor", "ContenidoDelMensaje" ]
}
```

Intenciones: nombres en inglés-español de Discord (`MensajesDelServidor`, `ContenidoDelMensaje`, `MiembrosDelServidor`, `MensajesDirectos`, `Servidores`…); un valor inválido da error con la lista completa. Leer `mensaje.contenido` exige `ContenidoDelMensaje`; eventos de miembros, `MiembrosDelServidor`.

No escribas el token en repos ni respuestas: guárdalo en `.env` y léelo con la librería `ent` (cli.md).

## Comando

```chord
nuevo comando Avatar {
    descripcion "Muestra el avatar"        // obligatorio
    contextos [ "Servidor", "DM", "CanalPrivado" ]
    // opcionales: nsfw, integraciones ["Servidor","Usuario"], servidoresPermitidos [..],
    //             ignorar "Mensajes"|"Slashs", alias [..]

    opciones {
        persona {
            tipo "usuario"     // texto entero booleano usuario canal rol mencionable numero archivo
            descripcion "A quién"
            requerido verdadero
        }
    }

    enviar mensaje { contenido "Hola {persona.nombre}" }
}
```

- Cada opción es una **variable** con su nombre (`persona`, `cantidad`…). La clave del tipo es `tipo`.
- Nombres de comando/opción: 1–32 caracteres; las opciones sin mayúsculas. No llames a una opción `cliente`, `contexto` ni `ctx`.
- Disponibles dentro: `usuario`, `canal`, `cliente`. `ctx`/`contexto` son internos y no se pueden usar. No los redeclares (tampoco como variable de bucle).
- El cuerpo ya es async: usa `esperar(…)` y `enviar mensaje` directamente.

## Evento

```chord
nuevo evento mensajeCreado {            // también vale `evento mensajeCreado`
    si (mensaje.contenido igual "ping") { enviar mensaje { contenido "pong" } }
}
```

| Evento | Variables inyectadas (solo dentro del evento) |
|---|---|
| `encendido` | `usuario`, `cliente` |
| `mensajeCreado` | `mensaje`, `cliente` |
| `mensajeBorrado` / `mensajeEditado` | `mensaje`, `mensajeBorrado`/`mensajeEditado`, `cliente` |
| `canalCreado` · `canalBorrado` · `canalEditado` | `canal`, `cliente` |
| `ban` · `unban` · `entradaMiembro` · `idaMiembro` | `miembro`, `cliente` |
| `invitado` · `expulsado` | `servidor`, `cliente` |
| `rolCreado` · `rolBorrado` · `rolEditado` | `rol`, `cliente` |
| `limitado` | `ratelimit` |
| `interaccion` | `interaccion`, `cliente` |

En funciones sueltas no existen: pásalas como argumento. `enviar mensaje` en eventos que no son de mensaje necesita `canal "id"` o `usuario "id"`.

## Mensajes

`enviar mensaje { … }` solo dentro de comando/evento. Propiedades: `canal`, `usuario` (DM), `contenido`, `embed`, `boton`. `var msg es enviar mensaje {…}` guarda el mensaje (hace falta para un recolector).

```chord
nuevo embed Bienvenida {                 // `nuevo embed` / `nuevo boton`: declara DENTRO del archivo que los usa
    color 0x5865F2  titulo "Hola"  descripcion "…"  imagen "url"  cartel "url"
    campos [ { titulo "Reglas" descripcion "Sé amable" lineado verdadero } ]
    pie { texto "pie" icono "url" }
}
nuevo boton Aceptar {
    id "aceptar"  etiqueta "Aceptar"
    estilo "verde"          // azul gris verde rojo enlace premium (o 1–6)
    emoji "✅"
}

enviar mensaje {
    embed Bienvenida                          // o bloque `{…}`; varios: UNA propiedad con lista
    embed [ { titulo "Uno" }, Bienvenida ]
    boton [ Aceptar, Rechazar ]               // se reparten solos en filas de 5
    boton [ [ {…} ], [ {…} {…} ] ]            // filas manuales, máx. 5 por fila
}
```

**Declara `nuevo boton`/`nuevo embed` dentro del comando/evento que los usa.** En un módulo propio (incluida `componentes/`) compilan, pero al cargarse lanzan `Button is not defined` (falta el `import` de seyfert) y el compilador no lo avisa.

## Recolector de botones

```chord
funcion alAceptar(interaccion, cliente) { imprimir("ok") }       // callbacks: funciones declaradas aparte

nuevo comando Confirmar {
    descripcion "x"

    var msg es enviar mensaje { contenido "¿Seguro?"  boton Aceptar }

    recolector msg {                       // sin `nuevo`
        filtro soloAutor                   // opcional: funcion(interaccion, cliente, contexto) -> booleano
        tiempo 30000                       // opcional, ms
        alFinalizar cuandoTermine          // opcional: funcion(razon, reiniciar, cliente, contexto)
        alPulsarId {
            aceptar alAceptar              // idDelBoton  funcion(interaccion, cliente)
        }
    }
}
```

La variable debe ser un `enviar mensaje` del mismo comando. Cada clave debe coincidir con un `id` **fijo** de un botón del mensaje (los ids dinámicos `"b{n}"` no se enlazan).

## Canales

```chord
var c es crear canal { servidor "id"  nombre "avisos"  tipo "texto"  tema "x"  nsfw falso
                       categoria "id"  posicion 1  modoLento 5  bitrate 64000  limiteUsuarios 10 }
editar canal { canal canal  nombre "nuevo"  razon "motivo" }     // `canal` obligatorio; ≥1 cambio
borrar canal { canal canal  razon "limpieza" }                   // `razon` OBLIGATORIA
var uno es obtener canal { id "123" }
var voces es listar canales { servidor "1"  tipo "voz"  categoria "9" }
```

`nombre` es obligatorio en crear; `servidor` solo hace falta en eventos sin servidor implícito. Tipos: texto voz categoria anuncios escenario foro multimedia. `crear` no admite `razon`.

**Barreras (no se desactivan)**: ninguna acción de canal dentro de un bucle; ninguna dentro del evento que la dispara (`canalCreado/Editado/Borrado`); solo dentro de comando/evento (aunque la función sea `@asincrono`); `borrar` exige `razon`; el canal debe ser del servidor del contexto. En ejecución: permisos de quien lo ejecuta y cuota por servidor. `obtener`/`listar` sí pueden ir en bucles.

## Antes de entregar un bot

Estructura de carpetas correcta · un comando/evento por archivo · línea en blanco antes del código en cada bloque · campos cerrados con literales · ids de botón fijos y coincidentes · sin acciones de canal en bucles · token fuera del código · `dischord-compiler src --check --json` con `ok:true` · avisar de que no se probó contra Discord.
