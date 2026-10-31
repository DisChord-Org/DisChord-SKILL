# Sistema de tipos de DisChord

Los tipos son **opcionales**. Sin anotaciones el código compila igual; con anotaciones el compilador (fase `Análisis`) valida asignaciones, argumentos, retornos, índices y acceso a miembros. Un error de tipos **bloquea la compilación**.

## Sintaxis

| Forma | Ejemplo |
|---|---|
| Variable | `var n tipo numero es 5` |
| Parámetro | `funcion f(a tipo texto, b tipo numero)` |
| Retorno | `funcion f(a tipo texto) -> numero { … }` (flecha `->`) |
| Lista | `numero[]`, `texto[][]`, `A[]` |
| Tupla | `[numero, texto]` (longitud fija, tipo por posición) |
| Unión | `texto\|numero`, `Lista\|Mapa\|texto` |
| Lista de unión | `(numero\|texto)[]` — **los paréntesis son obligatorios** (`numero\|(texto[])` también) |
| Clase propia | `A`, `Animal[]` (tipado **nominal**: importa el nombre y la herencia) |

## Tipos disponibles

`texto` `numero` `booleano` `bdo` `indefinido` `cualquiera` `nada` y las clases `Lista` `Mapa` `Conjunto` `Promesa` `Expresion` `Fecha` `Usuario`, más cualquier **clase declarada en el archivo**. Un tipo desconocido es error (`Tipo desconocido 'mango'`); nombrar una clase de la corelib que no es tipo (p. ej. `Mates`) también.

- `nada` solo vale como **tipo de retorno** (o dentro de una unión de retorno). No como parámetro ni elemento de lista/tupla.
- `cualquiera` desactiva la comprobación para ese valor.
- Un constructor no puede declarar tipo de retorno.

## Qué se comprueba

| Situación | Resultado |
|---|---|
| `var n tipo numero es "x"` | Error: la variable se declaró con tipo 'numero' pero se le asignó 'texto' |
| Reasignar con otro tipo (`n es "x"`) | Error |
| `l[0] es "x"` con `l: numero[]` | Error por elemento |
| Tupla con longitud distinta | Error (`se declaró como tupla de 2 elemento(s)…`) |
| Índice de tupla fuera de rango (literal) | Error (`tiene 2 elemento(s) (índices del 0 al 1)`) |
| Índice de texto no entero | Error |
| Llamar con otra cantidad de argumentos | Error (`'f' espera 2 argumento(s) pero se le pasaron 1`) |
| Argumento de tipo equivocado | Error (`El argumento 1 de 'f' es de tipo 'texto', se esperaba 'numero'`) |
| `devolver` de tipo distinto al declarado | Error |
| Función con retorno declarado que no devuelve en todos los caminos | Error |
| Usar un valor `nada` | Error (`se inicializó con una llamada que no devuelve ningún valor`) |
| Subclase donde se pidió otra clase no relacionada | Error. Subclase → superclase **sí** vale; al revés no. |
| Interpolar en un `texto` anotado como `numero` | Error de tipo |
| Opción/parámetro de comando o evento tipado | Se valida contra el tipo real (`miembro.nombre` es `texto`) |

## Inferencia

- Literales: `5`→`numero`, `"a"`→`texto`, `[1,2]`→`numero[]`, `[1,"a"]`→`(numero|texto)[]`, `[nuevo A(), nuevo A()]`→`A[]`.
- Se infiere el retorno de funciones sin anotación (unión de todos los `devolver`; recursión y `@asincrono` incluidos).
- `l.filtrar(f)` conserva el tipo de elemento; `l.mapear(f)` devuelve `cualquiera[]`.
- Variables de bucle: del elemento de la lista, `numero` en `rango`, `texto` en textos, clave `texto` en objetos.
- Acceso a miembros en tipos conocidos se valida (propiedad vs método, existencia). En tipos desconocidos (`cualquiera`, parámetros sin tipo) el compilador **no** discute: usa heurísticas por nombre (`.longitud`, `.tamano`, `.mayusculas` …) para elegir el código JS correcto.
- Tras reasignar a otra clase, el tipo nominal "se degrada" salvo que sea subclase.

## Recomendaciones al escribir código

- Anota **parámetros y retornos** de funciones públicas; deja inferir las variables locales.
- Si necesitas aceptar varios tipos, usa uniones antes que `cualquiera`.
- Cuando el compilador diga "se esperaba X", corrige el valor o amplía la anotación; no la quites para "silenciar".
