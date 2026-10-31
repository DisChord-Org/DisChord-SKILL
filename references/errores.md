# Catálogo de errores del compilador

Cada diagnóstico trae `phase` (`Lexer` → `Parser` → `Análisis` → `Interno`), `message`, `line`, `column`. Se obtienen con `dischord archivo.chord --check --json`. Abajo, el mensaje (o su inicio) y qué hacer. Muchos errores incluyen sugerencias `¿Querías decir 'x'?`: léelas.

## Lexer / sintaxis

| Mensaje | Causa → arreglo |
|---|---|
| `No se usa ';': separa las propiedades y las sentencias con saltos de línea` | Quita el `;`. |
| `Carácter inesperado: =` / `?` / `'` | `=` → `es`; no hay ternario ni comillas simples. |
| `Token inesperado en expresión: menos …` | No hay `-` unario: usa `0 menos x`. |
| `Después de 'si' se debe abrir una expresión con '('…` | Falta el paréntesis en la condición. |
| `Esperaba un elemento de tipo 'L_BRACE'… (En su lugar se encontró 'si')` | Escribiste `sino si`; es `ademas si`. |
| `Se debe especificar un nombre para la variable (… se encontró 'o')` | Nombre reservado (`o`, `y`, `en`, `tipo`, `prop`, `js`…). Renombra. |
| `El nombre 'mientras' no está declarado` | No existe `mientras`; usa `para (i en rango(n))`. |
| `Se esperaba el nombre del parámetro` | Valores por defecto / `...resto` no existen. |
| `Token inesperado en expresión: funcion` | No hay funciones anónimas; declara y pasa el nombre. |
| `Se esperaba un tipo válido después de 'tipo'` | Falta o está mal el tipo. |
| `Un tipo con '[]' dentro de una unión debe ir entre paréntesis` | `(texto[])`, `(numero\|texto)[]`. |
| `Separa el código de las propiedades con una línea en blanco` | En un BDO/comando/evento/bloque, deja una línea vacía antes de las sentencias. |
| `La propiedad 'x' está repetida` / `Identificador duplicado` | Clave repetida en un bloque (p. ej. dos `embed`) o variable duplicada. |

## Nombres y declaraciones

| Mensaje | Arreglo |
|---|---|
| `El nombre 'x' no está declarado. ¿Querías decir 'y'?` | Declara la variable o corrige el nombre; fuera de comando/evento, `mensaje`, `usuario`, `canal` no existen. |
| `No se puede declarar la clase/función/variable/parámetro 'Mapa': es el nombre de una clase de la biblioteca estándar` | Renombra. Aplica a `Mapa Conjunto Lista Texto Mates Fecha JSON BDO Promesa Expresion Aleatorio Numero Usuario Canal consola …`. |
| `No se puede declarar la variable 'canal': es un identificador reservado por DisChord` | Nombre inyectado. Usa `miCanal`. |
| `No se puede declarar 'usuario': el comando 'X' ya lo define` | Ya existe dentro de ese comando/evento (también como variable de bucle). |
| `La clase 'A' hereda de sí misma (A → B → A)` | Ciclo de herencia. |
| `La clase 'B' extiende de 'A', que se declara más abajo` | Declara la base antes. |
| `Ya existe una declaración 'encender bot'` | Solo uno por proyecto/archivo. |
| `Solo se permite un 'comando' o 'evento' por archivo` | Separa en archivos. |
| `No se encontró el módulo './x'` / `'lib:x'` | Ruta o nombre de módulo inexistente. |

## Tipos y llamadas

| Mensaje | Arreglo |
|---|---|
| `La variable 'x' se declaró con tipo 'T' pero se le asignó un valor de tipo 'U'` | Corrige el valor o la anotación. |
| `'f' espera N argumento(s) pero se le pasaron M` | Cantidad de argumentos. |
| `El argumento k de 'f' es de tipo 'U', se esperaba 'T'` | Tipo de argumento. |
| `La función 'f' declara retorno 'T' pero devuelve un valor de tipo 'U'` / `… pero no devuelve ningún valor` | Ajusta retorno o `devolver`. |
| `'x' es un método de Lista: llámalo con paréntesis, x()` | Añade `()`. |
| `'longitud' es una propiedad de Texto, no un método` | Quita `()`. |
| `'limpiar' es un método de instancia de Texto, no se puede usar sobre la clase` | Llámalo sobre un valor: `"  a ".limpiar()`. |
| `Tipo desconocido 'X'. Tipos válidos: …` | Usa un tipo de la lista o una clase declarada. |
| `'nada' solo puede usarse como tipo de retorno` | Quita `nada` de parámetros/listas. |
| `El índice N está fuera de rango: la tupla tiene K elemento(s)` | Índice literal inválido. |
| `Un constructor no puede declarar un tipo de retorno` | Quita el `->`. |

## Asincronía

| Mensaje | Arreglo |
|---|---|
| `Se llama a la función asíncrona 'x' desde una función que no es asíncrona; márcala con @asincrono` | Añade `@asincrono` a la función que llama. |
| `El método 'm' de 'B' es asíncrono pero el de 'A' no` (y viceversa) | Iguala la asincronía en la herencia. |

## Capa Discord (bots)

| Mensaje | Arreglo |
|---|---|
| `'enviar mensaje' solo puede usarse dentro de un comando o un evento…` | No lo uses en nivel superior ni en funciones sueltas. Devuelve datos y envía desde el comando/evento. |
| `'enviar mensaje' espera una respuesta` | Dentro de función normal no hay contexto donde responder. |
| `Después de 'enviar' se esperaba 'mensaje'` | `enviar mensaje { … }`. |
| `'mensaje' no admite 'contnido'` | Propiedad con typo; las válidas son `canal`, `usuario`, `contenido`, `embed`, `boton`. |
| `'comando' no admite expresiones ni texto con llaves en 'ignorar': escribe uno de: …` | Los campos **cerrados** (`ignorar`, `estilo`, `tipo`, `intenciones`, `contextos`, `integraciones`) solo aceptan **textos literales sin `{}`** de la lista válida. |
| `Tipo de opción no reconocido en 'dato'` / `'opcion' no admite 'opcion'` | La clave del tipo de opción es `tipo "texto"`. |
| `La opción 'ctx' choca con un nombre que DisChord usa internamente` | No llames así a una opción: `cliente`, `contexto`, `ctx`. |
| `El evento 'constructor' no existe` / `Intención desconocida: …` | Nombre de evento o intención fuera de la lista. |
| `El nombre 'ctx' no está declarado` | `ctx` y `contexto` son internos, no usables en el código. |
| `debe tener entre 1 y 32 caracteres (tiene 33)` / `no puede tener mayúsculas` | Límites de Discord para nombres de comando/opciones. |
| `'crear canal' no admite 'razon'` | `razon` solo en `editar` y `borrar` canal. |
| `En 'borrar canal' se esperaba 'razon'` / `En 'crear canal' se esperaba 'nombre'` / `En 'editar canal' se esperaba 'canal'` / `En 'obtener canal' se esperaba 'id'` | Falta el campo obligatorio. |
| `necesita al menos una propiedad que cambiar` | `editar canal` sin cambios. |
| `no puede usarse dentro de un bucle` | Acciones de canal en `para` → prohibido. |
| `lo dispararía a sí mismo` | `crear/editar/borrar canal` dentro de `canalCreado/Editado/Borrado` → bucle infinito evitado. |
| `no trae un servidor` | En eventos sin servidor implícito añade `servidor "id"` a `crear canal`. |
| `solo puede usarse dentro de un comando o un evento` | Acciones de canal fuera de comando/evento (aunque la función sea `@asincrono`). |
| `Tipo de canal inválido: 'x'` | `texto voz categoria anuncios escenario foro multimedia`. |
| `alPulsarId espera un botón con id 'x' en el mensaje 'msg'. Ids disponibles: …` | La clave del recolector debe coincidir con un `id` fijo de un botón del mensaje. |
| `… solo tiene uno dinámico ('b{n}'); alPulsarId no puede enlazar ids dinámicos` | Usa ids fijos para los botones que quieras recolectar. |
| `Una fila de botones admite como máximo 5` | Máx. 5 botones por fila. |

## Depurar lo desconocido

1. `dischord x.chord --check --json` → fase, línea, columna.
2. `dischord x.chord --emit tokens` si sospechas del Lexer; `--emit ast` si del Parser.
3. Reduce el archivo hasta el mínimo que reproduce el error.
4. Busca un test parecido en `~/Documentos/Projects/DisChord/tester/tests/` (`grep -rl "texto" --include=*Test.ts`): cada test de error declara su mensaje esperado.
