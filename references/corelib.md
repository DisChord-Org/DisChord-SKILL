# Biblioteca estándar (corelib) — referencia completa

Generada de `dischord corelib --json`. Si el compilador cambia, **regenera con ese comando**: es la fuente de verdad. Notación: `p?` opcional, `...p` resto de argumentos. Los métodos de instancia se llaman sobre el valor (`"hola".mayusculas()`); los estáticos sobre la clase (`Mates.redondear(2.5)`). Los métodos llevan **paréntesis siempre** (`lista.agregar(1)`); las propiedades **no** (`lista.longitud`). Confundirlos es error de compilación.

## Funciones globales
- `esperar` (function, async)
- `imprimir` (function)
- `rango` (syntax)

## Literales
- `espacio` = " "
- `intro` = "\n"

## Tipos
`texto` (primitive), `numero` (primitive), `booleano` (primitive), `bdo` (primitive), `indefinido` (primitive), `cualquiera` (any), `nada` (void), `Lista` (class), `Mapa` (class), `Conjunto` (class), `Promesa` (class), `Expresion` (class), `Fecha` (class), `Usuario` (class)

## Clases

### consola
- `imprimir(...valores: cualquiera) -> nada` *(estático)* — Escribe los valores en la consola.
- `error(...valores: cualquiera) -> nada` *(estático)* — Escribe los valores en la consola como un error.
- `advertencia(...valores: cualquiera) -> nada` *(estático)* — Escribe los valores en la consola como una advertencia.
- `limpiar() -> nada` *(estático)* — Limpia la consola.

### Texto (valores de tipo `texto`)
Propiedades (sin paréntesis):
- `longitud: numero` — Cantidad de caracteres del texto.

Métodos:

- `limpiar() -> texto` — Quita los espacios del principio y del final.
- `partir(separador: texto, limite?: numero) -> texto[]` — Divide el texto en una lista por cada aparición del separador.
- `reemplazar(buscado: texto, reemplazo: texto) -> texto` — Reemplaza la primera aparición de lo buscado.
- `reemplazarTodo(buscado: texto, reemplazo: texto) -> texto` — Reemplaza todas las apariciones de lo buscado.
- `terminaCon(final: texto) -> booleano` — Indica si el texto termina con el valor dado.
- `empiezaCon(inicio: texto) -> booleano` — Indica si el texto empieza con el valor dado.
- `repetir(veces: numero) -> texto` — Repite el texto el número de veces dado.
- `cortar(inicio: numero, fin?: numero) -> texto` — Devuelve la parte del texto entre dos posiciones.
- `minusculas() -> texto` — Convierte el texto a minúsculas.
- `mayusculas() -> texto` — Convierte el texto a mayúsculas.
- `tiene(buscado: texto, posicion?: numero) -> booleano` — Indica si el texto contiene lo buscado.
- `indiceDe(buscado: texto, posicion?: numero) -> numero` — Posición de la primera aparición de lo buscado, o -1.
- `ultimoIndiceDe(buscado: texto, posicion?: numero) -> numero` — Posición de la última aparición de lo buscado, o -1.
- `caracterEn(indice: numero) -> texto` — Carácter en la posición dada.
- `rellenarInicio(longitud: numero, relleno?: texto) -> texto` — Rellena el principio hasta alcanzar la longitud dada.
- `rellenarFinal(longitud: numero, relleno?: texto) -> texto` — Rellena el final hasta alcanzar la longitud dada.
- `concatenar(...textos: texto) -> texto` — Une el texto con los que se le pasan.
- `recortarInicio() -> texto` — Quita los espacios del principio.
- `recortarFinal() -> texto` — Quita los espacios del final.
- `normalizar(forma?: texto) -> texto` — Normaliza el texto Unicode.
- `coincidir(patron: cualquiera) -> cualquiera` — Busca coincidencias con una expresión.
- `coincidirTodo(patron: cualquiera) -> cualquiera` — Busca todas las coincidencias con una expresión.
- `buscar(patron: cualquiera) -> numero` — Posición de la primera coincidencia con una expresión, o -1.
- `codigoEn(indice: numero) -> numero` — Código Unicode del carácter en la posición dada.
- `subcadena(inicio: numero, fin?: numero) -> texto` — Devuelve la parte del texto entre dos posiciones.
- `comparar(otro: texto) -> numero` — Compara con otro texto: negativo, 0 o positivo.
- `en(indice: numero) -> indefinido|texto` — Carácter en la posición dada; admite negativos para contar desde el final.

### Lista (valores de tipo `cualquiera[]`)
Propiedades (sin paréntesis):
- `longitud: numero` — Cantidad de elementos de la lista.

Métodos:

- `agregar(...elementos: cualquiera) -> numero` — Agrega elementos al final y devuelve la nueva longitud.
- `quitarUltimo() -> cualquiera` — Quita el último elemento y lo devuelve.
- `quitarPrimero() -> cualquiera` — Quita el primer elemento y lo devuelve.
- `agregarInicio(...elementos: cualquiera) -> numero` — Agrega elementos al inicio y devuelve la nueva longitud.
- `unir(separador?: texto) -> texto` — Une los elementos en un texto con el separador dado.
- `mapear(accion: cualquiera) -> cualquiera[]` — Crea una lista con el resultado de la función sobre cada elemento.
- `llenar(valor: cualquiera, inicio?: numero, fin?: numero) -> cualquiera[]` — Rellena la lista con un valor entre dos posiciones.
- `todos(condicion: cualquiera) -> booleano` — Indica si todos los elementos cumplen la condición.
- `algunos(condicion: cualquiera) -> booleano` — Indica si algún elemento cumple la condición.
- `filtrar(condicion: cualquiera) -> cualquiera[]` — Devuelve los elementos que cumplen la condición.
- `encontrar(condicion: cualquiera) -> cualquiera` — Devuelve el primer elemento que cumple la condición.
- `tiene(valor: cualquiera) -> booleano` — Indica si la lista contiene el valor.
- `cortar(inicio?: numero, fin?: numero) -> cualquiera[]` — Devuelve una copia de la parte de la lista entre dos posiciones.
- `invertir() -> cualquiera[]` — Invierte el orden de la lista en su lugar.
- `ordenar(comparador?: cualquiera) -> cualquiera[]` — Ordena la lista en su lugar.
- `reducir(accion: cualquiera, inicial?: cualquiera) -> cualquiera` — Reduce la lista a un valor aplicando la función de izquierda a derecha.
- `aplanar(profundidad?: numero) -> cualquiera[]` — Aplana las listas anidadas hasta la profundidad dada.
- `paraCada(accion: cualquiera) -> nada` — Ejecuta la función con cada elemento.
- `indiceDe(valor: cualquiera, inicio?: numero) -> numero` — Posición de la primera aparición del valor, o -1.
- `ultimoIndiceDe(valor: cualquiera, inicio?: numero) -> numero` — Posición de la última aparición del valor, o -1.
- `concatenar(...listas: cualquiera) -> cualquiera[]` — Une la lista con otras listas o valores en una nueva.
- `buscarIndice(condicion: cualquiera) -> numero` — Posición del primer elemento que cumple la condición, o -1.
- `encontrarUltimo(condicion: cualquiera) -> cualquiera` — Devuelve el último elemento que cumple la condición.
- `aplanarMapear(accion: cualquiera) -> cualquiera[]` — Aplica la función a cada elemento y aplana el resultado un nivel.
- `en(posicion: numero) -> cualquiera` — Elemento en la posición dada; admite posiciones negativas.
- `insertar(inicio: numero, cantidad?: numero, ...elementos: cualquiera) -> cualquiera[]` — Inserta elementos desde una posición, como splice: quita la cantidad indicada antes de insertar.
- `eliminar(inicio: numero, cantidad?: numero) -> cualquiera[]` — Elimina elementos desde una posición y devuelve los eliminados.
- `claves() -> cualquiera` — Iterador con las posiciones de la lista.
- `valores() -> cualquiera` — Iterador con los elementos de la lista.
- `entradas() -> cualquiera` — Iterador de pares posición y elemento.
- `reducirDerecha(accion: cualquiera, inicial?: cualquiera) -> cualquiera` — Como reducir, pero de derecha a izquierda.
- `copiarDentro(destino: numero, inicio: numero, fin?: numero) -> cualquiera[]` — Copia una parte de la lista a otra posición dentro de ella.
- `ordenarCopia(comparador?: cualquiera) -> cualquiera[]` — Devuelve una copia ordenada sin modificar la original.
- `invertirCopia() -> cualquiera[]` — Devuelve una copia invertida sin modificar la original.

### Mates
Propiedades (sin paréntesis):
- `PI: numero` — La constante π.
- `E: numero` — La constante e.
- `LOGNEP2: numero` — Logaritmo natural de 2.
- `LOGNEP10: numero` — Logaritmo natural de 10.
- `LOG2E: numero` — Logaritmo en base 2 de e.
- `LOG10E: numero` — Logaritmo en base 10 de e.
- `RAIZCUADRADA1_2: numero` — Raíz cuadrada de 1/2.
- `RAIZCUADRADA2: numero` — Raíz cuadrada de 2.

Métodos:

- `absoluto(valor: numero) -> numero` *(estático)* — Valor sin signo.
- `redondearArriba(valor: numero) -> numero` *(estático)* — Entero más cercano hacia arriba.
- `redondearAbajo(valor: numero) -> numero` *(estático)* — Entero más cercano hacia abajo.
- `redondear(valor: numero) -> numero` *(estático)* — Entero más cercano.
- `truncar(valor: numero) -> numero` *(estático)* — Parte entera, sin decimales.
- `potencia(base: numero, exponente: numero) -> numero` *(estático)* — Base elevada al exponente.
- `raizCuadrada(valor: numero) -> numero` *(estático)* — Raíz cuadrada.
- `raizCubica(valor: numero) -> numero` *(estático)* — Raíz cúbica.
- `hipotenusa(...valores: numero) -> numero` *(estático)* — Raíz cuadrada de la suma de los cuadrados de los valores.
- `exponente(valor: numero) -> numero` *(estático)* — e elevado al valor.
- `log(valor: numero) -> numero` *(estático)* — Logaritmo natural.
- `log10(valor: numero) -> numero` *(estático)* — Logaritmo en base 10.
- `log2(valor: numero) -> numero` *(estático)* — Logaritmo en base 2.
- `seno(valor: numero) -> numero` *(estático)* — Seno, con el ángulo en radianes.
- `coseno(valor: numero) -> numero` *(estático)* — Coseno, con el ángulo en radianes.
- `tangente(valor: numero) -> numero` *(estático)* — Tangente, con el ángulo en radianes.
- `arcoseno(valor: numero) -> numero` *(estático)* — Arcoseno, en radianes.
- `arcocoseno(valor: numero) -> numero` *(estático)* — Arcocoseno, en radianes.
- `arcotangente(valor: numero) -> numero` *(estático)* — Arcotangente, en radianes.
- `arcotangente2(ordenada: numero, abscisa: numero) -> numero` *(estático)* — Ángulo, en radianes, del punto (x, y).
- `senoHiperbolico(valor: numero) -> numero` *(estático)* — Seno hiperbólico.
- `cosenoHiperbolico(valor: numero) -> numero` *(estático)* — Coseno hiperbólico.
- `tangenteHiperbolica(valor: numero) -> numero` *(estático)* — Tangente hiperbólica.
- `maximo(...valores: numero) -> numero` *(estático)* — El mayor de los valores.
- `minimo(...valores: numero) -> numero` *(estático)* — El menor de los valores.
- `aleatorio() -> numero` *(estático)* — Número aleatorio entre 0 (incluido) y 1 (excluido).
- `signo(valor: numero) -> numero` *(estático)* — Signo del valor
- `arcosenoHiperbolico(valor: numero) -> numero` *(estático)* — Arcoseno hiperbólico.
- `arcocosenoHiperbolico(valor: numero) -> numero` *(estático)* — Arcocoseno hiperbólico.
- `arcotangenteHiperbolica(valor: numero) -> numero` *(estático)* — Arcotangente hiperbólica.
- `redondearFlotante(valor: numero) -> numero` *(estático)* — Valor como número de 32 bits.
- `exponenteMenosUno(valor: numero) -> numero` *(estático)* — e elevado al valor, menos 1.
- `logMasUno(valor: numero) -> numero` *(estático)* — Logaritmo natural de 1 más el valor.
- `ceroInicial(valor: numero) -> numero` *(estático)* — Cantidad de ceros a la izquierda en 32 bits.
- `limitar(valor: numero, minimo: numero, maximo: numero) -> numero` *(estático)* — Mantiene el valor dentro del rango dado.

### Numero (valores de tipo `numero`)
Propiedades (sin paréntesis):
- `MAXIMO_SEGURO: numero` — Mayor entero que se representa sin pérdida.
- `MINIMO_SEGURO: numero` — Menor entero que se representa sin pérdida.
- `MAXIMO: numero` — Mayor número representable.
- `MINIMO: numero` — Menor número positivo representable.
- `EPSILON: numero` — Diferencia entre 1 y el siguiente número representable.

Métodos:

- `esEntero(valor: cualquiera) -> booleano` *(estático)* — Indica si el valor es un número entero.
- `esNumero(valor: cualquiera) -> booleano` *(estático)* — Indica si el valor es un número finito.
- `esEnteroSeguro(valor: cualquiera) -> booleano` *(estático)* — Indica si el valor es un entero que se representa sin pérdida.
- `esNaN(valor: cualquiera) -> booleano` *(estático)* — Indica si el valor es NaN.
- `aEntero(texto: texto, base?: numero) -> numero` *(estático)* — Convierte un texto en un número entero.
- `aDecimal(texto: texto) -> numero` *(estático)* — Convierte un texto en un número decimal.
- `aFijo(decimales?: numero) -> texto` — Texto con la cantidad de decimales dada.
- `aPrecision(precision?: numero) -> texto` — Texto con la cantidad de cifras significativas dada.
- `aTexto(base?: numero) -> texto` — Texto del número, en la base dada.
- `aLocal(idioma?: texto) -> texto` — Texto del número según el idioma.

### JSON
- `leer(texto: texto) -> cualquiera` *(estático)* — Convierte un texto JSON en un valor.
- `escribir(valor: cualquiera, reemplazo?: cualquiera, espacios?: numero) -> texto` *(estático)* — Convierte un valor en un texto JSON.

### BDO (valores de tipo `bdo`)
- `claves(objeto: cualquiera) -> texto[]` *(estático)* — Lista con los nombres de los campos del objeto.
- `valores(objeto: cualquiera) -> cualquiera[]` *(estático)* — Lista con los valores de los campos del objeto.
- `entradas(objeto: cualquiera) -> cualquiera[]` *(estático)* — Lista de pares nombre y valor de los campos del objeto.
- `unir(destino: cualquiera, ...fuentes: cualquiera) -> bdo` *(estático)* — Copia los campos de las fuentes en el destino y lo devuelve.
- `congelar(objeto: cualquiera) -> bdo` *(estático)* — Impide cambiar el objeto y lo devuelve.
- `desdeEntradas(entradas: cualquiera) -> bdo` *(estático)* — Crea un objeto a partir de una lista de pares nombre y valor.
- `tienePropia(objeto: cualquiera, clave: texto) -> booleano` *(estático)* — Indica si el objeto tiene el campo como propio.

### Mapa (valores de tipo `Mapa`) — instanciable con `nuevo Mapa()`
Propiedades (sin paréntesis):
- `tamano: numero` — Cantidad de claves del mapa.

Métodos:

- `obtener(clave: cualquiera) -> cualquiera` — Devuelve el valor guardado para la clave.
- `poner(clave: cualquiera, valor: cualquiera) -> Mapa` — Guarda un valor para la clave y devuelve el mapa.
- `tiene(clave: cualquiera) -> booleano` — Indica si hay un valor para la clave.
- `borrar(clave: cualquiera) -> booleano` — Borra la clave y devuelve si existía.
- `limpiar() -> nada` — Borra todas las claves.

### Conjunto (valores de tipo `Conjunto`) — instanciable con `nuevo Conjunto()`
Propiedades (sin paréntesis):
- `tamano: numero` — Cantidad de valores del conjunto.

Métodos:

- `agregar(valor: cualquiera) -> Conjunto` — Agrega un valor y devuelve el conjunto.
- `tiene(valor: cualquiera) -> booleano` — Indica si el conjunto contiene el valor.
- `borrar(valor: cualquiera) -> booleano` — Quita el valor y devuelve si estaba.
- `limpiar() -> nada` — Quita todos los valores.

### Promesa (valores de tipo `Promesa`) — instanciable con `nuevo Promesa()`
- `todas(promesas: cualquiera) -> cualquiera` *(estático)* *(async)* — Espera a todas las promesas y devuelve la lista de resultados.
- `todasResueltas(promesas: cualquiera) -> cualquiera` *(estático)* *(async)* — Espera a que todas terminen y devuelve el estado de cada una.
- `primera(promesas: cualquiera) -> cualquiera` *(estático)* *(async)* — Devuelve el resultado de la primera promesa en terminar.
- `cualquiera(promesas: cualquiera) -> cualquiera` *(estático)* *(async)* — Devuelve el resultado de la primera promesa que se cumpla.
- `resolver(valor?: cualquiera) -> cualquiera` *(estático)* *(async)* — Crea una promesa ya cumplida con el valor.
- `rechazar(motivo?: cualquiera) -> cualquiera` *(estático)* *(async)* — Crea una promesa ya rechazada con el motivo.

### Expresion (valores de tipo `Expresion`) — instanciable con `nuevo Expresion()`
- `probar(texto: texto) -> booleano` — Indica si el texto cumple la expresión.
- `ejecutar(texto: texto) -> cualquiera` — Busca la expresión en el texto y devuelve la coincidencia, o indefinido si no hay.

### Fecha (valores de tipo `Fecha`) — instanciable con `nuevo Fecha()`
- `ahora() -> numero` *(estático)* — Marca de tiempo actual, en milisegundos.
- `parsear(texto: texto) -> numero` *(estático)* — Convierte un texto de fecha en una marca de tiempo.
- `año() -> numero` — Año de la fecha.
- `mes() -> numero` — Mes de la fecha, de 0 a 11.
- `dia() -> numero` — Día del mes de la fecha.
- `diaSemana() -> numero` — Día de la semana, de 0 (domingo) a 6.
- `hora() -> numero` — Hora de la fecha.
- `minuto() -> numero` — Minutos de la fecha.
- `segundo() -> numero` — Segundos de la fecha.
- `milisegundo() -> numero` — Milisegundos de la fecha.
- `marcaDeTiempo() -> numero` — Milisegundos transcurridos desde 1970.
- `aISO() -> texto` — La fecha en formato ISO 8601.
- `aTexto() -> texto` — La fecha como texto.
- `aLocal(idioma?: texto, opciones?: cualquiera) -> texto` — La fecha como texto según el idioma y las opciones dadas.

### Aleatorio
- `elegir(lista: cualquiera) -> cualquiera` *(estático)* — Elige un elemento al azar de la lista.
- `mezclar(lista: cualquiera) -> cualquiera[]` *(estático)* — Devuelve una copia de la lista con el orden al azar.
- `entre(minimo: numero, maximo: numero) -> numero` *(estático)* — Número entero al azar entre dos valores, ambos incluidos.

### cliente — *nombre inyectado por DisChord*
Propiedades (sin paréntesis):
- `id: texto` — Identificador del bot.
- `nombre: texto` — Nombre de usuario del bot.
- `avatar: indefinido|texto` — Hash del avatar del bot, si tiene.
- `avatarUrl: texto` — URL del avatar del bot.
- `ping: numero` — Latencia con la puerta de enlace, en milisegundos.

Métodos:

- `emitir(nombre: texto, destinatario: cualquiera, paquete: cualquiera, fragmento: numero, usarCache?: booleano) -> nada` *(estático)* — Reproduce un paquete de la puerta de enlace a través de los manejadores de eventos: recibe el nombre del evento de Discord (p. ej. "MESSAGE_CREATE"), el cliente, el paquete en bruto, el fragmento (shard) y, opcionalmente, si debe actualizar la caché. Devuelve una promesa.

### canal — *nombre inyectado por DisChord*
Propiedades (sin paréntesis):
- `tema: indefinido|texto` — Tema del canal, si tiene.
- `modoLento: indefinido|numero` — Segundos que debe esperar un usuario entre mensajes.
- `topico: indefinido|texto` — Tema del canal, si tiene.
- `ratelimit: indefinido|numero` — Segundos que debe esperar un usuario entre mensajes.
- `posicion: numero` — Posición del canal en la lista de canales.
- `categoria: indefinido|texto` — Identificador de la categoría que contiene el canal.
- `nombre: indefinido|texto` — Nombre del canal.
- `ultimoMensaje: indefinido|texto` — Identificador del último mensaje del canal.

Métodos:


### Usuario (valores de tipo `Usuario`)
Propiedades (sin paréntesis):
- `nombre: texto` — Nombre de usuario.
- `nombreGlobal: indefinido|texto` — Nombre visible global del usuario, si lo tiene.
- `etiqueta: texto` — Etiqueta completa del usuario.
- `discriminador: texto` — Discriminador del usuario.
- `insignias: indefinido|numero` — Insignias públicas del usuario, como número de banderas.
- `esBot: booleano|indefinido` — Indica si el usuario es un bot.
- `esSistema: booleano|indefinido` — Indica si el usuario es del sistema de Discord.
- `avatarUrl: texto` — URL del avatar del usuario.
- `bannerUrl: indefinido|texto` — URL del banner del usuario, si tiene.
- `colorPerfil: indefinido|numero` — Color del perfil del usuario, si lo tiene.
- `tipoPremium: indefinido|numero` — Tipo de suscripción premium del usuario, si la tiene.

Métodos:


### Canal
Propiedades (sin paréntesis):
- `nombre: indefinido|texto` — Nombre del canal.
- `tema: indefinido|texto` — Tema del canal, si tiene.
- `tipo: indefinido|texto` — Tipo del canal: texto, voz, categoria, anuncios, escenario, foro o multimedia.
- `categoria: indefinido|texto` — Identificador de la categoría que contiene el canal, si está en una.
- `posicion: indefinido|numero` — Posición del canal en la lista de canales.
- `modoLento: indefinido|numero` — Segundos que debe esperar un usuario entre mensajes.
- `limiteUsuarios: indefinido|numero` — Máximo de usuarios de un canal de voz, si lo tiene.
- `servidor: indefinido|texto` — Identificador del servidor al que pertenece el canal.
- `ultimoMensaje: indefinido|texto` — Identificador del último mensaje del canal.
- `mencion: texto` — Mención del canal, para escribirla en un mensaje.
- `enlace: texto` — Enlace al canal.
- `creadoEn: Fecha` — Fecha de creación del canal.

Métodos:

