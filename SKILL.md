---
name: dischord
description: Escribe, depura, revisa y explica código DisChord (.chord), el lenguaje de programación con sintaxis en español que compila a JavaScript y, con su capa de bots, a bots de Discord sobre Seyfert. Úsala siempre que el usuario mencione DisChord, Chord, archivos .chord, el comando `dischord`, el compilador/CLI o el IDE DisChord Code Studio, o quiera programar en español con palabras clave como `si`, `para`, `mas`, `igual`, `es`, `funcion`, `clase`, `nuevo comando`, `nuevo evento`, `enviar mensaje`, `encender bot`, `recolector`, `crear canal`.
---

# DisChord

DisChord es un **transpilador**: lees/escribes `.chord`, el compilador genera JavaScript (ES Modules). Tiene dos capas:

| Capa | Qué aporta | Código en el repo |
|---|---|---|
| **chord** | Lenguaje base: variables, operadores en palabras, funciones, clases, bucles, módulos, tipos opcionales, async | `src/chord/` |
| **dischord** | Bots de Discord: bot, comandos slash/prefijo, eventos, mensajes, embeds, botones, recolectores, canales | `src/dischord/` |

El resto del ecosistema: `DischordCLI` (CLI), `DisChord-Code-Studio` (IDE de escritorio con modo `chord`), `DisChord_Server`.
Repo principal del compilador: `~/Documentos/Projects/DisChord`. Sus tests `tester/tests/*/input.chord` son ejemplos de referencia fiables.

## Índice de esta skill

| Necesitas… | Lee |
|---|---|
| Sintaxis y semántica del lenguaje base | este archivo |
| Tipos, inferencia y qué comprueba el compilador | [references/tipos.md](references/tipos.md) |
| Funciones async / `@asincrono` / `await` automático | [references/asincronia.md](references/asincronia.md) |
| **Ejecutar/compilar/crear proyectos, actualizar, librerías (`chord`)** | [references/cli.md](references/cli.md) |
| Bots: comandos, eventos, mensajes, botones, canales | [references/bots.md](references/bots.md) |
| Todos los métodos de `Texto`, `Lista`, `Mates`, `Mapa`… con firmas | [references/corelib.md](references/corelib.md) |
| Interpretar un error del compilador | [references/errores.md](references/errores.md) |
| Plantillas completas ya validadas | carpeta [examples/](examples); proyecto de bot completo en [examples/proyecto-bot](examples/proyecto-bot) |

## Flujo de trabajo obligatorio

1. **Escribe** el `.chord`. Parte de la plantilla de `examples/` más cercana.
2. **Valida** antes de entregar: `dischord archivo.chord --check --json`.
   Devuelve `{"ok":bool,"diagnostics":[{"file","phase","severity","message","line","column"}]}`. Fases: `Lexer`, `Parser`, `Análisis`, `Interno`. Itera hasta `ok:true`.
3. El directorio de trabajo necesita un `package.json` con `"type": "module"` (si no, error de fase `Interno`). Para bots, **respeta o crea la estructura `src/index.chord` + `src/comandos/` + `src/eventos/`** y valida el proyecto entero con `dischord src --check --json`.
4. **Dos ejecutables** (detalle en [references/cli.md](references/cli.md)): **`chord`** es la CLI para crear, compilar y **ejecutar** proyectos; **`dischord-compiler`** es el compilador con las opciones finas de validación (el repo lo expone como `node ~/Documentos/Projects/DisChord/bin/dischord.js` o `dischord`). Donde esta skill escribe `dischord …`, usa el que esté instalado.
5. **Ejecutar un proyecto = `chord run src/index.chord`** (compila todo `src/` y lanza `dist/index.js`). `chord compile src/index.chord` solo compila. Pasa siempre `src/index.chord`, no la carpeta ni un archivo de una subcarpeta. `chord run` **arranca el bot real** contra Discord: no lo ejecutes sin token válido y permiso expreso.
6. Ante cualquier duda sobre vocabulario, consulta `dischord corelib --json` (palabras clave, clases, bloques, eventos, ámbitos) en lugar de adivinar.

### Opciones del compilador

| Opción | Efecto |
|---|---|
| `--check` | Corre todas las fases sin escribir ni ejecutar. |
| `--json` | Diagnósticos en JSON (con `--check` para editores). Sin `--check` ejecuta después. |
| `--no-run` | Compila pero no ejecuta el bot. |
| `--stdin`, `--stdin-filepath <ruta>` | Compila el código de la entrada estándar como si fuera ese archivo. |
| `--emit tokens\|ast` | Imprime tokens o el árbol sintáctico en JSON (útil para depurar el parser). |
| `-o, --out-dir <dir>` | Carpeta de salida. |
| `corelib [--json]` | Vocabulario del lenguaje. |

Códigos de salida: `0` ok · `1` el código tiene errores (o el bot falló) · `2` la herramienta no pudo hacer su trabajo.

### Comandos de `chord` (resumen)

| Comando | Qué hace |
|---|---|
| `chord init <ruta>` | Crea un proyecto (`package.json`, `seyfert`, `src/index.chord`, `.gitignore`). No crea `comandos/` ni `eventos/`. |
| `chord compile src/index.chord` | Compila todo `src/` a `dist/` sin ejecutar. |
| `chord run src/index.chord` | Compila y ejecuta `dist/index.js` (arranca el bot). |
| `chord update <cli\|compiler\|all> [-f] [--json]` | Actualiza la CLI y/o el compilador. |
| `chord pkg install\|uninstall\|search\|use\|unuse\|sync` | Gestiona las librerías (`lib:nombre`). |
| `chord -v` / `chord -h` | Versión / ayuda. |

## Reglas de oro (lo que más se rompe)

1. **Nada de `;`.** Se separa por saltos de línea. Es error de Lexer (`No se usa ';'`). Dentro de textos y comentarios sí se pueden escribir.
2. **`es` en vez de `=`** para declarar y asignar: `var x es 1`, `x es x mas 1`, `esta.a es a`. El `=` suelto es error (`Carácter inesperado: =`).
3. **Condición entre paréntesis siempre**: `si (x mayor 1) { }`. Sin paréntesis es error.
4. **Solo comillas dobles** para textos. Las comillas simples y las comillas invertidas no existen.
5. **No hay `-` unario**: `-5`, `[-1]` y `menos a` fallan. Usa `0 menos 5`.
6. **No hay `mientras`/`while`, ternario `? :`, funciones anónimas, `intentar/capturar`, destructuring, parámetros por defecto ni `...resto`**. Para repetir: `para (i en rango(n))`. Para callbacks: pasa el **nombre** de una función declarada (`lista.mapear(duplicar)`).
7. **`sino si` no existe**: se escribe `ademas si`. Estructura: `si (…) {…} ademas si (…) {…} sino {…}`.
8. **Palabras reservadas no sirven como nombre de variable/función/parámetro** (`o`, `y`, `en`, `es`, `no`, `tipo`, `prop`, `js`, `nuevo`, `esta`, `super`, `sino`, `para`, `si`…). Un `var o es 1` falla. Sí sirven como **claves de objeto**.
9. **No declares nombres de la biblioteca estándar** (`Mapa`, `Mates`, `Fecha`, `JSON`, `Texto`, `Lista`, `consola`…) como variable, función, clase, parámetro, variable de bucle ni import. Tampoco los **nombres inyectados** (`usuario`, `canal`, `cliente`, `mensaje`…) donde el contexto ya los define.
10. **Métodos con paréntesis, propiedades sin ellos**: `lista.longitud`, `texto.mayusculas()`, `mapa.tamano`. Equivocarse es error (`'agregar' es un método de Lista: llámalo con paréntesis`).
11. **Un archivo = un `nuevo comando` o un `nuevo evento`** como máximo (el resto —`encender bot`, `nuevo boton`, `nuevo embed`, funciones, clases— puede convivir).
12. **Estructura de proyecto fija para bots**: `src/index.chord` (con `encender bot`), **`src/comandos/`** para cada `nuevo comando` y **`src/eventos/`** para cada `nuevo evento`. Un comando creado en `src/` o en cualquier otra carpeta compila sin errores pero **Seyfert no lo detecta** y no existe en Discord. `src/componentes/` existe pero se queda vacía. `nuevo boton`/`nuevo embed` se declaran **dentro** del comando que los usa, nunca en un módulo aparte (falla al cargar). Detalle en [references/bots.md](references/bots.md#estructura-del-proyecto-obligatoria).
13. **Valida con `--check` antes de responder.** Nunca entregues `.chord` sin compilar. En un proyecto, valida todo con `dischord src --check --json`.

## Léxico

- **Comentarios**: `// línea` y `/* bloque */`.
- **Identificadores**: letras (incluidas `ñ` y acentos), dígitos y `_`. Distingue mayúsculas.
- **Números**: enteros y decimales (`3`, `26.83`), hexadecimales (`0xff`). La notación científica (`1e3`) **no** existe.
- **Textos**: `"…"` pueden abarcar varias líneas. **No hay escapes**: `"a\nb"` produce la barra y la n literales, y `\"` cierra el texto. Para un salto de línea concatena el literal `intro` (`"línea 1" mas intro mas "línea 2"`); para un espacio, `espacio`. Si necesitas una comilla dentro de un texto, usa `js("String.fromCharCode(34)")` (comprobado) o evítala.
- **Interpolación**: `"Hola {nombre}"`, admite **expresiones DisChord**: `"{edad mas 1}"`, `"{n mayor 2}"`, `"{no activo}"`, `"{lista.longitud}"`, `"{tipo n}"`. Los booleanos se muestran como `verdadero`/`falso`. Llaves literales: `{{x}}` produce `{x}`. `"a{2,3}"`/`"{2}"` se tratan según el contenido; una llave sin cerrar se deja tal cual. Un `{expresion mal}` es error de análisis.
- **Literales de palabra**: `verdadero`, `falso`, `indefinido`, `espacio` (`" "`), `intro` (`"\n"`).

## Operadores

| Familia | Palabras | Notas |
|---|---|---|
| Aritmética | `mas` `menos` `por` `entre` `resto` `exp` | `mas` también concatena textos. `exp` = potencia. |
| Comparación | `igual` `no_igual` `mayor` `menor` `mayor_igual` `menor_igual` | |
| Comparación estricta | `igual_tipado` `no_igual_tipado` | equivalen a `===`/`!==`. |
| Lógica | `y` `o` `no` | `no` es prefijo: `no activo`, `no (a igual b)`. |
| Agrupación | `( … )` | Úsala para fijar precedencia. |
| Símbolos | `+ - * / > < >= <= && \|\|` | Aceptados por compatibilidad; **lo idiomático son las palabras**. |
| Tipo | `tipo x` | En expresión da el tipo del valor (como `typeof`). |

## Variables

```chord
var a es 5                      // inferido
var nombre tipo texto es "Ana"  // anotado
var sinValor                    // declaración sin inicializar
sinValor es 3                   // asignación
miObjeto.campo es 1             // asignación a miembro
lista[0] es 9                   // asignación por índice
```

No se pueden declarar dos variables con el mismo nombre en el mismo ámbito (`Identificador duplicado`). No se admite `var a es 1, b es 2`.

## Condiciones

```chord
si (nota mayor_igual 9) {
    consola.imprimir("Excelente")
} ademas si (nota mayor_igual 6) {
    consola.imprimir("Aprobado")
} sino {
    consola.imprimir("Reprobado")
}
```

## Bucles

Solo existe `para (x en <algo>)`, que itera:

| `<algo>` | `x` vale |
|---|---|
| `rango(n)` | los números `0 … n-1`. **Solo admite un argumento**: `rango(2, 8, 3)` compila pero ignora los demás y recorre `0…1`. Para otros tramos usa `rango(n)` y calcula (`inicio mas i`, `i por paso`). |
| una lista `[…]` | cada elemento |
| un texto | cada carácter (respeta emojis/pares sustitutos) |
| un objeto BDO | cada **clave** |
| un `Mapa` | sus **claves** (usa `m.obtener(k)` para el valor) |
| un `Conjunto` | sus valores |

`salir` = `break`, `pasar` = `continue`. La expresión iterada se evalúa **una sola vez**. La variable del bucle no existe fuera de él, no puede redeclararse dentro y, si la lista es tipada, hereda el tipo del elemento.

## Funciones

```chord
funcion sumar(a tipo numero, b tipo numero) -> numero {
    devolver a mas b
}

@asincrono
funcion cargar(id) { esperar(100)  devolver id }
```

- `devolver` sin valor está permitido en funciones sin tipo de retorno o `-> nada`.
- No hay funciones anónimas ni flechas: define la función y pasa su nombre.
- Los parámetros sin tipo se aceptan; los tipados se validan en cada llamada (cantidad y tipos).
- Una función puede declarar otra dentro (anidadas).
- `js("expresión")` inserta JavaScript crudo como escape: `var r es js("({ a: 1 }).a")`. Úsalo solo si el lenguaje no lo cubre.

## Clases

```chord
clase Animal {
    Animal(nombre) {                       // el constructor se llama como la clase
        esta.nombre es nombre
    }
    funcion hablar() { consola.imprimir(esta.nombre) }
    @fijar
    funcion especie() { devolver "animal" }   // método estático
}

clase Perro extiende Animal {
    Perro(nombre) { super(nombre) }
}

nuevo Animal Gato {                        // forma corta de "clase Gato extiende Animal"
    Gato(nombre) { super(nombre) }
}

var p es nuevo Perro("Rex")
p.hablar()
Animal.especie()
```

- `esta` = this, `super(...)`/`super.metodo()`, `nuevo C(args)`.
- Una clase debe declararse **antes** de ser extendida (`'B' extiende de 'A', que se declara más abajo`).
- Prohibidas: herencia cíclica, constructor con tipo de retorno, sobrescribir un método async con uno no async (o viceversa).
- Puedes extender clases de la corelib con `nuevo Mapa MiMapa { }` (los miembros heredados conservan sus nombres JS: `set`, `size`).
- `prop` está reservada para propiedades de clase.
- Se comprueba el número y tipo de argumentos del constructor y de los métodos (incluidos los heredados).

## Objetos (BDO — "bloque de datos")

Un objeto es un bloque `{ … }` donde cada línea es `clave valor` (sin `:` ni `,`):

```chord
var persona es {
    nombre "Juan"
    edad 30
    direccion { ciudad "Lima" }     // anidado
    saludar saludarPersona          // referencia a una función
    total 1 mas
        2                           // el valor puede continuar tras un operador
}
persona.direccion.ciudad
persona["nombre"]
persona.edad es 31
```

- Claves duplicadas → error (`La propiedad 'nombre' está repetida`).
- Cualquier palabra reservada es válida como clave (`si`, `clase`, `mas`, `es`, `tipo`…).
- **Código dentro del bloque** (sentencias, llamadas) es válido pero debe ir **tras una línea en blanco** que lo separe de las propiedades; si no: `Separa el código de las propiedades con una línea en blanco`. Un `{` suelto dentro del bloque lo trata como bloque anónimo.
- `@BDOI` (BDO "inteligente") deja que un valor use claves anteriores del mismo objeto: `doble (base por 2)`. También puede aplicarse a una propiedad anidada.
- Funciones como miembro de un `@BDOI`: `funcion saludar() { … esta.nombre … }`.
- Se puede iterar por claves (`para (k en obj)`), leer con `obj.clave` u `obj["clave"]`.
- Funciones de utilidad: clase `BDO` (`BDO.claves(o)`, `BDO.valores(o)`, `BDO.entradas(o)`, `BDO.unir(destino, fuentes)`, `BDO.congelar(o)`…).

## Listas, Mapas, Conjuntos

```chord
var l tipo numero[] es [1, 2, 3]
l.agregar(4)
l[0] es 10
l.longitud
var dobles es l.mapear(duplicar)        // callback = nombre de función
var m es nuevo Mapa()
m.poner("a", 1)  m.obtener("a")  m.tamano
var s es nuevo Conjunto()
```

Los literales de lista admiten salto de línea y comas. Índices fuera de rango en tuplas tipadas son error en compilación. Lista completa en [references/corelib.md](references/corelib.md).

## Módulos

```chord
importar { PREFIJO } desde "./config"       // busca config.chord relativo al archivo
importar { ent } desde "lib:ent"            // librerías instaladas con `chord pkg` (carpeta ./lib)
exportar funcion saludar(n) { devolver "Hola " mas n }
exportar var VERSION es 1
exportar clase Util { }
```

- Un módulo inexistente es error de análisis (`No se encontró el módulo './x' (se buscó en: x.chord, x)`).
- No importes un nombre que choque con una clase de la corelib (`Mapa`, `Mates`…).
- Un `encender bot` puede recibir `token`/`prefijo` importados (`token TOKEN`, `prefijo PREFIJO()`).

## Biblioteca estándar (resumen)

`consola` (`imprimir`, `error`, `advertencia`, `limpiar`; atajo global `imprimir(...)`) · `Texto` · `Lista` · `Mates` (+ `Mates.PI`) · `Numero` · `JSON` · `BDO` · `Mapa` · `Conjunto` · `Promesa` · `Expresion` (regex) · `Fecha` · `Aleatorio` · `esperar(ms)` · `rango(…)`.
**Firmas completas: [references/corelib.md](references/corelib.md).**

## Cómo actuar según la petición

- **"Hazme un script/función/clase en DisChord"** → parte de `examples/01…06`, aplica las reglas de oro, valida.
- **"Hazme un bot/comando/evento"** → lee [references/bots.md](references/bots.md) **y su sección de estructura**. Si el proyecto ya existe, mira dónde están sus comandos y respeta esa estructura; si no, crea `src/index.chord`, `src/comandos/`, `src/eventos/`. Un archivo por comando/evento, y valida el proyecto con `dischord src --check --json`.
- **"Me da este error…"** → busca el mensaje en [references/errores.md](references/errores.md); si no está, reproduce con `--check --json` y `--emit ast` para localizar la fase.
- **"Convierte este JS/Python a DisChord"** → traduce operadores a palabras, `=`→`es`, `function`→`funcion`, `while`→`para … en rango` o reestructura, lambdas→funciones con nombre, `;` fuera, `-x`→`0 menos x`. Valida.
- **"Explica este código .chord"** → explica construcción por construcción; si dudas del significado, `--emit ast` o la salida JS en `dist/`.
- **Siempre**: dile al usuario qué archivos creaste, que pasaron `--check`, y qué **no** pudiste probar (por ejemplo, ejecutar el bot real contra Discord).

## Honestidad sobre los límites

Si el usuario pide algo que el lenguaje no soporta (while, try/catch, lambdas, etc.), dilo y propón el rodeo (`js("…")`, función con nombre, reestructurar). No inventes sintaxis ni métodos: consulta `corelib` o los tests.
