---
name: dischord
description: Escribe, depura y explica código DisChord (.chord), el lenguaje con sintaxis en español que compila a JavaScript y a bots de Discord (Seyfert). Úsala cuando el usuario mencione DisChord, archivos .chord, los comandos `chord` o `dischord-compiler`, o pida un bot con palabras clave en español (`si`, `para`, `mas`, `igual`, `es`, `nuevo comando`, `enviar mensaje`, `encender bot`, `recolector`).
---

# DisChord

Transpilador a JavaScript (ESM). Capas: **chord** (lenguaje base) y **dischord** (bots de Discord sobre Seyfert). Código del compilador: `~/Documentos/Projects/DisChord`; sus `tester/tests/*/input.chord` son ejemplos fiables.

- Bots (comandos, eventos, mensajes, botones, canales) → [references/bots.md](references/bots.md)
- `chord`, librerías `lib:`, opciones del compilador → [references/cli.md](references/cli.md)
- Plantillas validadas → [examples/](examples) (bot completo en `examples/proyecto-bot`)
- Vocabulario exacto (clases, métodos con firmas, eventos, bloques): **`dischord-compiler corelib --json`**. No lo adivines.

## Flujo

1. Escribe el código y **valídalo siempre**: `dischord-compiler src --check --json` (o `--check --json` sobre un archivo suelto). Devuelve `{"ok","diagnostics":[{phase,message,line,column}]}`; itera hasta `ok:true`. Los mensajes del compilador son claros y traen sugerencias (`¿Querías decir…?`): léelos.
2. Sin `dischord-compiler`: `node ~/Documentos/Projects/DisChord/bin/dischord.js …`.
3. El proyecto necesita `package.json` con `"type": "module"`.
4. Ejecutar un proyecto: **`chord run src/index.chord`** (compila `src/` y lanza `dist/index.js`). **Arranca el bot real**: no lo hagas sin que el usuario lo pida y sin token válido. Más en cli.md.

## Estructura de un bot (obligatoria)

```
package.json                # "type": "module"
src/index.chord             # encender bot { … }
src/comandos/*.chord        # un `nuevo comando` por archivo
src/eventos/*.chord         # un `nuevo evento` por archivo
src/componentes/            # reservada por Seyfert, vacía
```

Un comando/evento fuera de `src/comandos/` o `src/eventos/` **compila sin error pero Seyfert no lo carga**. Si el proyecto ya existe, respeta su estructura.

## Reglas que suelen fallar

1. **Sin `;`** (error de Lexer). Comentarios `//` y `/* */`.
2. **`es` en vez de `=`**: `var x es 1`, `x es x mas 1`, `esta.a es a`.
3. Condición **siempre entre paréntesis**: `si (x mayor 1) {…} ademas si (…) {…} sino {…}`. `sino si` no existe.
4. Solo **comillas dobles**. **Sin escapes**: `"\n"` no es salto de línea; usa los literales `intro` y `espacio` (`"a" mas intro mas "b"`).
5. **No existen**: `-` unario (usa `0 menos x`), `mientras`, ternario, funciones anónimas/flechas, try/catch, destructuring, parámetros por defecto, `...resto`, `var a es 1, b es 2`. Los callbacks son el **nombre** de una función declarada: `lista.mapear(duplicar)`.
6. Único bucle: `para (x en …)`. `rango(n)` **solo admite un argumento** (0…n-1). Itera listas, textos (por carácter), objetos (claves), `Mapa` (claves), `Conjunto` (valores). `salir` = break, `pasar` = continue.
7. **Nombres reservados no valen para variables/funciones/parámetros** (`o`, `y`, `en`, `es`, `no`, `tipo`, `prop`, `js`, `nuevo`, `esta`…); sí como claves de objeto. Tampoco declares nombres de la corelib (`Mapa`, `Mates`, `Fecha`, `JSON`, `consola`…) ni inyectados (`usuario`, `canal`, `cliente`, `mensaje`…).
8. **Métodos con paréntesis, propiedades sin ellos**: `lista.longitud`, `texto.mayusculas()`, `mapa.tamano`. Confundirlos es error.
9. Texto con **interpolación de expresiones**: `"Hola {nombre}, {edad mas 1}, {no activo}"`. Llaves literales: `{{x}}`. Booleanos se muestran `verdadero`/`falso`.
10. Un archivo = **un** `nuevo comando` **o** `nuevo evento`.
11. Una propiedad no puede repetirse en un bloque; tras las propiedades de un bloque (BDO, comando, evento…) el **código va después de una línea en blanco**.

## Sintaxis esencial

| Familia | Palabras |
|---|---|
| Aritmética | `mas` `menos` `por` `entre` `resto` `exp` (`mas` concatena) |
| Comparación | `igual` `no_igual` `mayor` `menor` `mayor_igual` `menor_igual` `igual_tipado` `no_igual_tipado` |
| Lógica | `y` `o` `no` (los símbolos `+ > && …` también compilan, pero usa palabras) |
| Literales | `verdadero` `falso` `indefinido` `espacio` `intro` |

```chord
var nombre tipo texto es "Ana"            // tipos opcionales, se comprueban al compilar
var notas tipo numero[] es [7, 8]
var par tipo [numero, texto] es [1, "a"]  // tupla; uniones: texto|numero, (numero|texto)[]
consola.imprimir("Hola {nombre}", tipo nombre)   // `tipo x` = typeof

funcion sumar(a tipo numero, b tipo numero) -> numero {
    devolver a mas b
}

clase Animal {
    Animal(nombre) { esta.nombre es nombre }     // constructor = nombre de la clase
    funcion hablar() { consola.imprimir(esta.nombre) }
    @fijar
    funcion especie() { devolver "animal" }      // estático
}
clase Perro extiende Animal {                    // la base se declara ANTES
    Perro(nombre) { super(nombre) }
}
nuevo Animal Gato { Gato(n) { super(n) } }       // forma corta de "clase Gato extiende Animal"
var p es nuevo Perro("Rex")

var persona es {                 // BDO: objeto sin ':' ni ','
    nombre "Juan"
    direccion { ciudad "Lima" }
    saludar saludarPersona       // referencia a función

    consola.imprimir("código tras línea en blanco")
}
persona.direccion.ciudad   persona["nombre"]
// @BDOI sobre un `var` permite valores calculados con claves previas: doble (base por 2)

@asincrono
funcion cargar(id) { esperar(100)  devolver id }   // el `await` se inserta solo, no lo escribas
esperar(10)                                          // válido en el nivel superior

importar { x } desde "./otro"       // busca otro.chord
importar ent                        // librería de ./lib (ver cli.md)
exportar funcion f() { }            // también `exportar var` y `exportar clase`
var r es js("1 + 1")                // escape a JavaScript crudo
```

## Asincronía

Llamar a una función `@asincrono` desde una función **no** `@asincrono` es error (`márcala con @asincrono`). Vale desde el nivel superior y desde comandos/eventos. Un método sobrescrito debe tener la misma asincronía que el de la clase base.

## Tipos

Se comprueban asignaciones, argumentos (cantidad y tipo), retornos, índices de tupla y propiedad vs. método. `nada` solo como tipo de retorno; `cualquiera` desactiva la comprobación. Tipado **nominal** para clases (subclase → base vale; al revés no). Tipos válidos: `texto numero booleano bdo indefinido cualquiera nada Lista Mapa Conjunto Promesa Expresion Fecha Usuario` y clases propias; `T[]`, `[A, B]`, `A|B`.

## Al responder

Entrega código ya validado con `--check` y di qué **no** pudiste probar (por ejemplo, el bot real contra Discord). Si piden algo que el lenguaje no soporta, dilo y propón el rodeo; no inventes sintaxis.
