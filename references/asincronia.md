# Asincronía

DisChord resuelve el `await` **por ti**. Tú declaras qué funciones son asíncronas y el compilador inserta `await` en las llamadas.

## Reglas

1. `@asincrono` encima de `funcion` (o de un método de clase) la hace `async`.
2. Cualquier llamada a una función `@asincrono` (propia, método, `esperar`, métodos asíncronos de la corelib como `Promesa.*`, y las acciones de Discord) **recibe `await` automáticamente**. No escribas `await`.
3. Solo se puede llamar a una función asíncrona desde otra asíncrona, desde el **nivel superior del archivo**, o desde el cuerpo de un **comando/evento** (ahí el compilador ya lo envuelve en async). Desde una función normal es error:
   `Se llama a la función asíncrona 'cargar' desde una función que no es asíncrona; márcala con @asincrono`.
4. `esperar(ms)` es asíncrona: válida en nivel superior, comandos/eventos y funciones `@asincrono`.
5. Se encadena sin paréntesis extra: `cargar(1).mayusculas()`, `raiz().dato().c`, `lista()[0]`, `lista().longitud`, `repo.leer(3).longitud` — el `await` envuelve la llamada correcta.
6. En herencia, un método sobrescrito debe tener **la misma asincronía** que el de la clase base (`El método 'm' de 'B' es asíncrono pero el de 'A' no`).
7. Con tipos: una función `@asincrono` anotada `-> texto` devuelve `texto` al llamarla (el `await` ya está aplicado), no `Promesa`.

```chord
@asincrono
funcion cargar(id tipo numero) -> texto {
    esperar(100)
    devolver "dato {id}"
}

@asincrono
funcion principal() {
    var uno es cargar(1)
    var varios es Promesa.todas([cargar(1), cargar(2)])
    consola.imprimir(uno mas " / " mas varios.longitud)
}

principal()
```

## Cuándo marcar `@asincrono`

- La función llama a `esperar`, a otra `@asincrono` o a `Promesa.*`.
- Un callback (de botón, etc.) que necesita esperar algo.
- `enviar mensaje` y las acciones de canal **no** se pueden usar en funciones sueltas aunque sean `@asincrono`: solo dentro del cuerpo de un comando/evento.
