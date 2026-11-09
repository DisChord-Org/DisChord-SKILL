# Librerías oficiales

Se instalan con `chord pkg` (ver `cli.md`) y se importan con `importar { a } desde "lib:nombre"` o `importar nombre` (`nombre.a`).

Los `README.md` instalados tienen errores (los de `rutas` listan `dirname`/`basename`/`extname`, inexistentes) y `--check` no detecta un nombre importado que no existe: falla al ejecutar. Usa solo los nombres de esta tabla; para otros:

```bash
node -e "import('$HOME/.dischord/libraries/<lib>/<versión>/src/index.js').then(m=>console.log(Object.keys(m).join(' ')))"
```

## `await` manual

El `await` automático solo cubre funciones `@asincrono` escritas en `.chord`. Las de `sf`, `procesos` e `internet` son JS y devuelven `Promise { <pending> }`: envuélvelas en una función `@asincrono` propia.

```chord
importar { leer } desde "lib:sf"

@asincrono
funcion cargar(ruta) {
    devolver leer(ruta)
}

@asincrono
funcion principal() {
    consola.imprimir(cargar("datos.txt"))   // aquí sí se espera solo
}
principal()
```

`esperar(ms)` es una pausa, no un `await`. `salir` (de `procesos`) no se puede importar: es palabra reservada.

## Exports

| Librería | Exports |
|---|---|
| `ent` | `ent.NOMBRE`: variables de `.env` (o `.ent`), cargadas al importar. Ej.: `token ent.TOKEN_DISCORD`. |
| `sf` (async) | `existe info leer escribir eliminar copiar mover`. `leer` de carpeta lista nombres; `escribir(ruta)` sin contenido crea la carpeta (y las padre). |
| `rutas` | `unir resolver normalizar relativa directorio nombreBase extension analizar formatear esAbsoluta estaDentro raiz separador delimitador path posix win32` |
| `procesos` | Async: `ejecutar` (→ `{stdout, stderr, codigo}`, no lanza con código ≠ 0), `ejecutarArchivo`. Sync: `lanzar matar entorno variableEntorno establecerVariableEntorno directorioActual cambiarDirectorio pid pidPadre plataforma argumentos version tiempoActividad usoMemoria alSeñal nombreHost directorioHome directorioTemporal tipoSistema versionSistema arquitectura nucleos memoriaTotal memoriaLibre tiempoActividadSistema interfacesRed usuario finDeLinea` |
| `internet` | Async (→ `{ok, estado, cabeceras, datos}`, `datos` ya decodificado): `obtener enviar peticion resolverHost resolverInverso resolverRegistros`. Sync: `analizar construir parametros construirParametros resolver esValida archivoAUrl urlAArchivo URL URLSearchParams` |

No hay literal `{ a: 1 }` como argumento: declara antes `var o es { a 1 }` (BDO) y pásalo.
