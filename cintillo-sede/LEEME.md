# cintillo-sede — franja inferior con la sede de la transmisión

Gráfica en desarrollo. **Todavía no es una gráfica del GC**: es una página suelta, autocontenida,
con su propia paleta. Está en `taller/` justamente por eso — no se necesita para transmitir y no
tiene por qué viajar en el pendrive con `gc/`.

## De dónde viene

Se escribió el 2026-08-30 para el torneo de bicipolo del fin de semana siguiente, como solución
única para esa transmisión. Esa copia ya está en la carpeta de streaming del evento y sale al aire
tal cual. Acá queda el original para seguir trabajándolo con la línea gráfica del GC.

Muestra una barra a todo el ancho, pegada abajo: pin de ubicación animado, `EN VIVO desde el` +
nombre de la sede, y la dirección en una segunda línea. Cicla sola mientras la escena esté al aire:
30 s puesta, 30 s oculta, y vuelve a empezar.

## Lo que falta decidir

**1. La estética.** Hoy replica a mano los tokens del overlay `timeline` de la app BPT1 —negro al
80%, borde `white/10`, acento `emerald-400`— porque tenía que convivir con esos overlays en la
misma escena. El tema `bicipolo.json` del GC es azul `#2e35ff` sobre fondo claro: son dos
identidades distintas. Hay que elegir una de dos:

- Adoptar las variables del GC (`--gc-acento`, `--gc-fondo`, `--gc-titulo`…) y que el cintillo se
  vea como el resto del GC, con tema por cliente.
- Dejar la paleta oscura como un tema nuevo del GC (uno tipo `bpt1`), y que sea el tema el que
  decida, no el archivo.

La segunda es más fiel a cómo está pensado el proyecto: el archivo no debería traer colores propios.

**2. Standalone o con panel.** Hoy los textos se editan en el bloque `CONFIG` del `<script>` o por
query string. Lo natural acá sería que los escriba el operador desde el panel. Dos caminos:

- **Barato**: reutilizar el `lower` que ya existe (`nombre` = sede, `cargo` = dirección) y sumarle
  el pin y el ciclo `hold`/`gap`. No toca `estado.js` ni inventa un tipo de gráfica nuevo.
- **Caro**: tipo de gráfica nuevo. Toca `overlay.html`, `overlay.css`, `overlay.js`, `estado.js`,
  los **dos** paneles y `panel.js` — con el problema conocido de que los paneles comparten
  `panel.js` sin guardas, así que hay que correr `taller/scripts/verificar-paneles.ps1`.

Cuando quede resuelto, la gráfica se muda a `gc/` y esta carpeta desaparece.

## Cosas que ya se aprendieron, para no volver a descubrirlas

- **`backdrop-filter: blur()` no hace nada en OBS.** El video vive en otra capa del compositor, no
  detrás del documento. Por eso el fondo es un degradado sólido y no un vidrio esmerilado.
- **La casilla "Archivo local" de OBS descarta el query string.** Para pasar parámetros hay que
  destildarla y pegar la URL `file:///...` completa en el campo *URL*.
- **Las fuentes son las del sistema.** Este archivo no usa `gc/css/fuentes.css`; al integrarlo,
  engancharlo ahí y ganamos Outfit/Mulish incrustadas en base64.
- El ciclo se midió con tres vueltas seguidas: el período es exactamente `hold + gap`, sin deriva.

## Parámetros de hoy

`?venue=` `?address=` `?lead=` `?hold=` (30) `?gap=` (30, `0` lo deja fijo) `?delay=` (0.4)
