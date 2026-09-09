---
name: franja-inferior-torneo
description: "La franja inferior del torneo (sede + hora + clima) fusionada el 2026-09-02, su arquitectura de dos bloques y la paleta de marca del torneo"
metadata: 
  node_type: memory
  type: project
  originSessionId: 308049cd-fe99-4ac3-8525-d4521c12383c
  modified: 2026-09-03T00:00:00.000Z
---

`taller/horayclima/franja-inferior.html` es la gráfica buena de la franja inferior del Torneo
Nacional de Bicipolo 2026. Fusiona `reloj-clima.html` (hora + temperatura, Open-Meteo) con
`cintillo-sede.html` (franja de sede con pin animado), que quedan como originales de referencia.

**La arquitectura es lo importante y no es evidente leyendo el archivo rápido:** son **dos
bloques con vidas separadas**, no una barra sola. El slot de la izquierda rota entre dos pasadas
que se turnan la misma caja —la sede 20 s, los auspiciadores ("Con el apoyo de") 30 s— y entre
una y otra la franja sale y vuelve a entrar, para que el cambio se lea como una gráfica nueva. El
bloque de la derecha (clima + reloj) entra una vez y **no se va nunca**, porque el usuario lo pidió
fijo.

El slot corre **cinco tandas**, cada una con su rótulo: la sede (7 s), "Con el apoyo de"
(municipio + corporación de deportes), "Con la colaboración de" dos veces (Corro + Indie, y
Cinnalove + Office Designs desde el 2026-09-03) y "Transmite" (foco). Una vuelta dura 56 s. Las
tandas de logos duran **7 segundos por logo**, calculados contando los items y no escritos a mano,
así al agregar o sacar un auspiciador el tiempo se acomoda solo. Van separadas por grupo porque
ocho logos juntos no se leen y porque así cada uno queda bien atribuido.

**Cada auspiciador lleva tres líneas** —nombre, bajada (qué hace) y el @— y la bajada va en la
tinta oscura, no en el gris del @: el público no conoce estas marcas y "Café de especialidad" es
lo que le dice de qué se trata. La jerarquía la hacen tamaño y peso (1.6/800, 1.15/500, 1.05/500)
y recién la tercera línea cambia de color. **En la tanda de foco el orden se invierte**: la url va
como nombre y el giro abajo, porque el logo ya dice FOCO y lo que hay que llevarse de esa lámina
es la dirección.

**La separación entre logos va por tanda, no global** (campo `separacion` de `TANDAS`, en rem). El
`gap: 5.5rem` del CSS es el piso y lo usa la tanda del municipio, que es la más apretada de todas
y queda con 98 px de holgura a 1920; las de colaboración van a 9rem porque les sobra ancho. Las
dos de colaboración llevan la misma a propósito: son dos páginas de una lista y si cambiara se
vería como un reacomodo.

**Un grupo que no cabe se parte en dos páginas con el mismo rótulo, no en dos grupos.** Los cuatro
que pusieron premios piden 1309 px de ancho natural y al slot le quedan 1099: apretados se pisan,
porque el nombre va con `white-space: nowrap` y no reflowea. Como los cuatro aportaron lo mismo,
inventarles una distinción para justificar la separación sería mentira, así que las dos tandas
repiten "Con la colaboración de" y se leen como la vuelta de página de una sola lista. **El tiempo
no entra en esta decisión**: a 7 s por logo, juntos o separados dan los mismos 59 s. Para que
funcione, `cambiarTanda` compara el rótulo de la tanda que viene con el de la que sale y, si son
iguales, funde `#tandas` en vez de `.contenido-cuerpo` (clase `cambiando-pagina`) — si no, el
rótulo se apagaría y encendería para decir lo mismo, y eso al aire se ve como un parpadeo. No se
puede arreglar desde el hijo: la opacidad se la lleva `.contenido-cuerpo`. **Ningún rótulo dice "Auspician" ni nombra los premios**: el municipio presta el espacio, unas
empresas donaron regalos y el streaming lo pone la casa — **nadie aportó dinero**. El usuario
rechazó "Premios cortesía de" (suena a traducción del inglés) y prefiere no nombrar lo donado.
Ninguno lleva dos puntos al final. El guion está en
la constante `TANDAS`, arriba del script. Cuando el slot se oculta conserva su espacio en el flex, y eso
es lo que evita que el bloque de la derecha salte de lugar. La franja **flota** (4rem laterales,
3.5rem abajo), no toca los bordes, para calzar con el resto de las gráficas del torneo.

**Los cuatro logos de colaboración van repintados a `#374151`**, el `gray-700` de Tailwind
(`magick logo.png -fill "#374151" -colorize 100`, que reemplaza el color sin tocar el alfa, así el
antialias se conserva). Llegaban de distintas procedencias —tres negros puros y Cinnalove en café
claro `#7F5A50`, 5,2:1 contra 18:1 de los otros— y se leían como recortes pegados.

**Se probó primero la tinta del tema `#16203f` y el usuario la rechazó**, con razón: es el mismo
color del nombre que va al lado, y con logo y texto exactamente del mismo color la lámina se lee
como un bloque plano y el logo pierde su condición de imagen. **El segundo intento, un neutro
`#3d3d3d`, también quedaba fuera de tono:** medidos en HSL todos los colores de la franja son
fríos (tinta 225°, gris del @ 221°) y un neutro de saturación 0 era lo único del cuadro sin tono.
El `gray-700` cae en 217°/19%, casi la misma familia que el `#5d6880` del @ pero más oscuro — el
escalón que le faltaba a la paleta. **La escala sale del proyecto `torneos bicipolo`**
(`../torneos bicipolo/`), que la usa en su frontend; sus gráficas propias son solo rojo `#ED2025`
y crema `#FBF8F5`, sin escala de grises definida.

Por abajo el techo lo ponen los trazos finos —la microtipografía de Corro y los rayos de la bici
de Indie—, que la compresión de video se come antes que nada: desde `gray-600` empiezan a ceder.

**El logo de Cinnalove se reemplazó el 2026-09-03** por `originales/cinnalove.cl-sitio.png`
(914×352). El primero era un recorte hecho sobre un JPEG: 11,1% de sus píxeles en alfa 6-40, una
nube de puntitos alrededor del corazón que eran los bloques de 8×8 de la compresión convertidos en
semitransparencias. **Diagnóstico útil para cualquier logo que llegue sucio: mirar el canal alfa
ampliado — si el ruido forma bloques cuadrados, viene de un JPEG y no del recorte.** Se limpiaba
con `-channel A -level 20%,100% +channel`, pero eso no arregla el trazo escalonado ni la falta de
resolución, así que lo que hay que pedir es el archivo de origen, no una limpieza. El nuevo es
además otro dibujo —lockup horizontal con CINNA/LOVE apilado en vez de una línea—: misma
proporción pero mucha más letra dentro de la caja, así que bajó de `escala: .85` a `.75` para
quedar pareja con Office Designs.

**Dos excepciones:** los municipales no se tocan (marcas institucionales a todo color, y esa tanda
funciona justamente por el contraste), y `foco.png` tampoco — no es silueta plana (su cono
plateado tiene 43.432 px de gris medio, `-colorize` lo aplasta) y además llevarlo al gris con
`+level-colors` lo aclara justo hacia lo que se corrigió el 2026-09-02, cuando se cambió por esta
versión de lettering oscuro porque la plateada quedaba lavada. Los originales sin retocar viven en
`auspicios/originales/`.

**Paleta del torneo, distinta a la del GC.** El rojo de marca es **`#ED1F24`**, muestreado de los
dos PNG que el usuario dejó en esa carpeta (la mosca "PNG rojo video Laura-06" y el "logo nacional
2026"). El crema `#f2eee3` y el azul marino `#16203f` salen de las cortinas de video en loop. El
usuario dijo explícitamente que **no es obligatorio seguir la línea de colores del proyecto
overlays**, así que esta gráfica no usa las variables `--gc-*`. Eso zanja la discusión que estaba
abierta en [[cintillo-sede-bicipolo]] entre el emerald de BPT1 y el azul de `bicipolo.json`.

La referencia de diseño es la franja de "Contigo en la mañana" de CHV (`ejemplo.jpg`,
`ejemplo2.jpg` en la misma carpeta): bloque de contenido a la izquierda, módulo de clima + reloj
grande sobre color sólido a la derecha.

**Cuándo sale al aire:** solo *entre* partidos, nunca durante el juego. Por eso puede permitirse
más peso visual que un overlay permanente.

**Dos cosas que costó encontrar probándola en OBS el 2026-09-02:**

- **Nunca usar `system-ui` en una gráfica que va a OBS.** En Chrome resuelve a Segoe UI, pero el
  CEF de OBS caía en otra fuente con métricas distintas y el nombre del estadio se cortaba al aire
  aunque en el navegador se viera entero. La regla del proyecto —tipografías incrustadas en
  base64— existe exactamente para esto. Se copiaron las de `gc/css/fuentes.css` (Outfit para
  títulos, Mulish para cuerpo) dentro del archivo. Además el título y la bajada **envuelven en vez
  de truncar**: un nombre cortado al aire lo ve el público, una segunda línea no.
- **Open-Meteo devuelve HTTP 503 de a ratos y después vuelve sola.** Eso, y no CORS ni el CEF, fue
  lo que dejó la franja sin clima en OBS mientras en el navegador se veía bien. Verificado: el
  `fetch` desde `file://` funciona con y sin `--allow-file-access-from-files`, y la API manda
  `access-control-allow-origin: *` con `Origin: null`. **Antes de culpar a OBS por una consulta que
  no carga, revisar si la API está respondiendo.** El archivo ahora reintenta con esperas
  crecientes (10, 20, 40, 60, 60 s) y cae a XMLHttpRequest si `fetch` falla.

**La franja entra una sola vez y después el lienzo no se mueve más**: los cambios de tanda son
un fundido del contenido. Antes salía y entraba entera en cada cambio, y con tantas tandas era
demasiado movimiento. El beneficio de fondo es que si el lienzo no se mueve, no hay nada que
pueda saltar — antes hubo que igualar la altura de las tandas con un `min-height` porque el
bloque de clima se estiraba a la más alta y saltaba al cambiar.

En las tandas de auspiciadores el rótulo es una **columna a la izquierda** y los logos van
centrados en lo que queda; la de la sede sigue con el rótulo arriba y todo a la izquierda, porque
es texto corrido. Pasar el rótulo al costado liberó su línea completa y permitió subir los logos
un 28% sin que la franja creciera. Dejarlo flotando en la esquina, fuera del flujo, gana lo mismo
pero choca con los logos centrados: como columna no puede chocar nunca. **Cada logo va con su nombre tipografiado al lado.** Los auspiciadores mandaron sellos
institucionales (el de la Corporación de Deportes es un círculo con el texto curvo en el borde):
a cualquier tamaño que entre en una franja inferior ese texto no se lee. Con el nombre al lado el
logo pasa a ser un ícono y la lectura la resuelve la tipografía. La línea secundaria lleva el @ de
Instagram, no la URL completa.

Tiene un `?diag=1` que muestra en pantalla el user agent, el protocolo, el ancho real de la Browser
Source y el error exacto de cada intento — es la única forma cómoda de leer un error dentro de OBS.

Todo lo demás —parámetros, comportamiento ante fallas de red, montaje en OBS— está en
`taller/horayclima/CONTEXTO.md`, que se mantiene al día.
