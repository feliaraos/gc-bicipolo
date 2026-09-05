# horayclima — contexto del proyecto

Notas de decisiones ya tomadas y probadas, no preferencias sueltas: deshacerlas rompe
algo concreto.

## Qué hay en esta carpeta

- **`franja-inferior.html`** — la gráfica buena, la que sale al aire. Es la fusión de los
  dos archivos de abajo, hecha el 2026-09-02.
- `reloj-clima.html` — el original de hora + temperatura. Se conserva como referencia;
  ya no se desarrolla.
- `cintillo-sede.html` (en `../cintillo-sede/`) — el original de la franja de sede. Misma
  situación.
- **`previa.html`** — banco de pruebas. Se abre con doble clic y muestra la franja sobre
  un fondo que imita la cámara, con botones para saltar a cada tanda, cambiar el fondo
  (cancha / claro / cuadros, para verificar el contraste en los dos extremos) y correr el
  ciclo en modo rápido. Sirve para revisar sin montar nada en OBS. No sale al aire.
- **`comentarios.html`** — el chat en vivo de YouTube, de a un comentario. Gráfica aparte
  que acompaña a la franja: se monta como una segunda Browser Source y se prende en las
  pausas. Ver la sección propia más abajo.
- `pruebas/` — capturas sueltas de comparaciones. Se puede borrar sin consecuencias.
- `ejemplo.jpg`, `ejemplo2.jpg` — capturas de la franja de CHV que sirvieron de
  referencia de diseño.
- `logo nacional 2026.png`, `PNG rojo video Laura-06.png` — marca del torneo. De acá sale
  el rojo `#ED1F24`.

Los dos originales quedan porque cada uno tiene comentarios que explican por qué su
mitad está hecha así, y esos comentarios ya viajaron completos a la fusión. Si en algún
momento sobran, se borran sin miedo.

## La idea de la franja

Es **un solo HTML** con **dos bloques de vidas separadas**, calcado de la franja de CHV
de `ejemplo2.jpg`:

    +----------------------------------+   +---------------+--------+
    |  slot de contenido — ROTA        |   | clima         | hora   |
    +----------------------------------+   +---------------+--------+

- **El slot de la izquierda rota entre cinco tandas** que se turnan la misma caja, cada
  una con su propio rótulo:

      0  "En vivo desde el"        Estadio Municipal Juan Antonio Ríos    7 s
      1  "Con el apoyo de"         muni + corporación de deportes        14 s
      2  "Con la colaboración de"  Corro + Indie                         14 s
      3  "Con la colaboración de"  Cinnalove + Office Designs            14 s
      4  "Transmite"               foco                                   7 s

  y vuelve a empezar. Una vuelta completa dura unos 56 s.

  **Las tandas 2 y 3 comparten rótulo a propósito**: no son dos grupos, es uno solo
  repartido en dos páginas. Ver más abajo.

  **Las duraciones de las tandas de logos no se escriben a mano: son 7 segundos por logo**
  (`porLogo`), calculados contando los items. Así, al agregar o sacar un auspiciador, el
  tiempo se acomoda solo y nadie tiene que acordarse. La de la sede es aparte (`segSede`,
  10 s) porque es texto y no logos.

  La sede bajó de 10 a 7 s el 2026-09-03: es la única tanda que no lleva logos y su texto
  se lee de una pasada, así que no necesitaba el tiempo extra.

  Se partió de 5 s por logo y se subió a 7 por el tiempo de lectura: hay un costo fijo de
  atención —notar que la franja cambió, mover la vista— que no depende de cuántos logos
  haya y se come más o menos un segundo y medio. A 5, la tanda de un solo logo quedaba
  corta, y justo esa es la que lleva dos líneas de texto y una dirección web. Subiendo el
  por-logo en vez de meter una excepción, la regla queda en una sola línea. Queda un
  `segMin` de 7 s de red, que con estos valores no llega a actuar.

  **La franja entra una sola vez, al principio**, con toda la animación: el canto rojo
  desplegándose, el pin cayendo con su rebote, los textos escalonados. Después de eso el
  lienzo **no se mueve más en toda la transmisión**: los cambios de tanda son un fundido
  del contenido y nada más (`FUNDIDO_MS`, 300 ms).

  La primera versión hacía salir y entrar la franja entera en cada cambio. Con cuatro
  tandas rotando eso era demasiado movimiento para una gráfica que además comparte
  pantalla con un reloj fijo. Y tiene un beneficio de fondo: **si el lienzo no se mueve,
  no hay nada que pueda saltar** — se acabó de raíz toda la familia de problemas de
  alturas distintas entre tandas.

  El guion está en la constante `TANDAS`, arriba del script. Cada auspiciador es un
  objeto `{ logo, nombre, bajada, dato, escala }` y agregar uno es sumarlo a la lista de
  su tanda. La `bajada` es opcional: sin ella el bloque queda en dos líneas.

  **Van separadas por grupo y no todos los logos juntos** por dos razones: ocho logos en
  una franja no se leen, y separándolos cada uno queda bien atribuido — el municipio
  presta el espacio, otros pusieron los premios, el streaming lo pone la casa. Meterlos a
  todos bajo un solo "Con el apoyo de" sería más cómodo y menos justo.

  **Un grupo que no cabe se parte en dos páginas con el mismo rótulo.** Es lo que pasa hoy
  con los cuatro que pusieron premios (tandas 2 y 3). Está explicado abajo, en "Los cuatro
  de los premios van en dos páginas".

  Con `?tanda=N` queda una sola fija, sin ciclo, para revisarla sin esperar el turno.
- **El bloque de la derecha no se va nunca.** Entra una vez al arranque y se queda toda
  la transmisión.

Que sean dos bloques y no una barra sola es lo importante del diseño, y es lo que hace
que el requisito "el clima y la hora quedan fijas" salga gratis. Cuando el slot se
oculta **conserva su espacio en el flex**, así el bloque de la derecha nunca salta de
lugar.

**La franja flota**, no toca los bordes: 4rem laterales y 3.5rem abajo de safe area.
Es para que calce con el resto de las gráficas del torneo, que son cajas sueltas sobre
el video.

**El slot ocupa todo el ancho hasta el bloque de clima.** Se probó la variante ajustada
al texto y se descartó por dos razones: los logos de los auspiciadores van a necesitar
todo el espacio que haya, y con la caja angosta el nombre del estadio se partía en dos
líneas y se veía más chico.

**Las dos pasadas tienen que medir lo mismo de alto.** `.texto` lleva un
`min-height: 5.25rem` justamente para eso. Sin él, la pasada de la sede medía 82,89 px y
la de apoyos 77,58 px a 1920 de ancho; como el bloque de clima y hora se estira a la
altura del más alto, esos 5,3 px de diferencia lo hacían saltar cada vez que cambiaba la
pasada. El número sale de la pasada más alta —rótulo .95 + margen .38 + título 2.24 +
margen .3 + bajada 1.31 = 5.18rem— redondeado hacia arriba. **Si alguna vez el título
llega a dos líneas, hay que subir ese valor** o el salto vuelve.

**La dirección de la sede va a 1.4rem**, no a 1.05. A 1.05 se perdía contra un título de
2rem que le lleva el cuádruple de peso, y es justo el dato que hace falta para llegar al
recinto. Cabe de sobra: el presupuesto vertical de esa tanda lo fija la de logos, que es
más alta, así que el bloque queda en 5,62rem contra un `min-height` de 6,65 y hay sitio
hasta 1.9rem si algún día hace falta.

**El bloque de la derecha va en líneas apiladas**, no en columnas: ciudad / ícono +
temperatura / máxima y mínima, y al lado el reloj con la hora y el AM/PM debajo. Antes
iba en dos columnas y con el AM/PM al costado: sobraba altura, quedaba un vacío
desproporcionado contra los bordes, y el AM/PM tan lejos de la hora se leía como algo
suelto en vez de como parte del reloj.

**Cuándo sale al aire:** solo *entre* partidos. Durante el juego la pantalla lleva
únicamente cronómetro, marcador, moscas del canal y avisos del partido. Por eso la
franja puede permitirse más peso visual y texto más largo que un overlay permanente.

## La paleta es la del torneo, no la del GC

Decisión tomada el 2026-09-02: este archivo **no** usa las variables del GC.

- `--f-rojo: #ed1f24` — muestreado de los dos PNG de marca (dan `#ED1F24` y `#ED2025`).
- `--f-papel: #f2eee3` — el crema de las cortinas de video en loop.
- `--f-tinta: #16203f` — el azul marino de las salpicaduras de esas mismas cortinas.

Fondo **claro** y no oscuro: la referencia de TV chilena usa cajas blancas sobre cámara
y se leen mejor que un negro translúcido, además de amarrar con el papel de las
cortinas. Esto entierra la discusión que quedaba abierta en
`../cintillo-sede/LEEME.md` entre el `emerald-400` de BPT1 y el azul del tema
`bicipolo.json`: no gana ninguno de los dos.

**Ojo con los íconos del clima:** no son los colores de `reloj-clima.html`. Allá el
fondo era oscuro y el sol iba amarillo `#ffc247`; sobre este crema el amarillo
desaparece, así que el sol pasó al naranjo de la cortina y la luna al azul marino.

## Restricciones que no se negocian

1. **`html` y `body` con `background: transparent !important`.** Si cualquiera de los dos
   queda con color, OBS pinta un rectángulo gris sobre la cámara.
2. **Animar solo `opacity` y `transform`.** Las resuelve la GPU sin rehacer layout. Nada
   de animar `width`, `top`, `filter` ni `box-shadow` mientras el PC codifica video.
3. **Scripts clásicos, sin `type="module"`.** Chromium bloquea los módulos ES cargados
   desde `file://`, que es como la Browser Source de OBS abre un archivo local.
4. **Cero dependencias de red que no sean la API del clima.** Nada de librerías por CDN
   ni fuentes remotas: desde `file://` no cargan, y cada una es algo más que puede
   fallar justo al aire. Los íconos son SVG escritos en el archivo.
5. **`backdrop-filter: blur()` es un no-op en OBS.** El video vive en otra capa del
   compositor, no detrás del documento. Los fondos van sólidos.

## La API del clima

`api.open-meteo.com` — sin API key, sin registro.

Se eligió sobre OpenWeatherMap y WeatherAPI por dos razones: no hay token que se filtre
en un HTML compartido ni cuenta que se venza el día del torneo, y responde
`access-control-allow-origin: *`, que es lo único que permite consultarla desde
`file://` (ahí el Origin es `null` y casi cualquier otra API rebota). **Reverificado el
2026-09-02** mandando `Origin: null`: sigue devolviendo `*`.

Consumo medido: ~850 B por consulta (con mínima y máxima, 587 B), ~9 KB/hora pidiendo
cada 10 min. Es el 0,00025% de un stream de 8.000 kbps. No hay nada que optimizar acá.

El `<link rel="preconnect">` del `<head>` adelanta DNS y handshake TLS: sin él, la
primera consulta demora unos segundos visibles. **No lo borres.**

## Comportamiento al aire (lo que más importa)

- **Nunca aparece un error en pantalla.** Si la consulta falla, el bloque de clima
  conserva el último valor conocido (guardado en `localStorage`), apenas atenuado. El
  error solo va a la consola.
- **Si el dato guardado tiene más de 3 horas, el bloque no se muestra.** Mejor sin
  temperatura que con una temperatura mentirosa. Si nunca llega el clima, queda el reloj
  solo y la franja igual funciona.
- **El bloque fijo entra una sola vez y ya completo**, esperando el primer clima, con un
  tope de 1,5 s. Así al aire no se ve el reloj solo y después la caja creciendo de golpe.
  Si la red está caída, la hora sale igual y a tiempo.
- **El reloj va en formato de 12 horas con AM/PM**, que es lo que se pidió. El AM/PM va
  notoriamente más chico y alineado arriba: es una aclaración, no parte del número.
- **La hora usa `timeZone: 'America/Santiago'` explícita**, no la del sistema. Si el
  notebook de transmisión quedó con la zona horaria mala (pasa con equipos prestados), al
  aire igual sale la hora correcta, con horario de verano resuelto por el navegador.
- **Los íconos distinguen día y noche** vía `is_day` de la API: un sol al aire en un
  partido de las 21:00 se ve pésimo.
- **El ciclo del slot no tiene deriva:** una vuelta dura exactamente `hold + gap`.

## Configuración

Todo por querystring, para que el operador ajuste desde el campo URL de OBS sin editar
el archivo:

    franja-inferior.html?sede=Estadio%20Juan%20Antonio%20R%C3%ADos&gap=0

| Parámetro | Para qué | De fábrica |
|---|---|---|
| `sede` | nombre del recinto | Estadio Municipal Juan Antonio Ríos |
| `direccion` | línea inferior | Salomón Sack Nº 331, Independencia… |
| `rotulo` | el rótulo chico de arriba | En vivo desde el |
| `logos` | reemplaza todo por una tanda de prueba; cada entrada acepta `:escala` | — |
| `rotulologos` | rótulo de esa tanda de prueba | Con el apoyo de |
| `fondologos=1` | tarjeta blanca detrás de cada logo | apagado |
| `segsede` | segundos de la tanda de la sede | 10 |
| `porlogo` | segundos que suma cada logo | 7 |
| `segmin` | piso: ninguna tanda dura menos | 7 |
| `tanda` | fija una sola tanda, sin ciclo (`0` = la sede) | — |
| `demora` | segundos antes de la primera entrada | 0.4 |
| `pos` | `arriba` manda la franja al borde superior | abajo |
| `lat` `lon` `tz` `ciudad` | de dónde es el clima | Independencia |
| `seg=1` | mostrar segundos en el reloj | no |
| `h12=0` | volver al reloj de 24 horas | va en AM/PM |
| `diag=1` | cartel de diagnóstico en pantalla | apagado |
| `parpadeo=1` | hacer latir los dos puntos | no |
| `minmax=0` | ocultar mínima y máxima del día | se muestran |
| `clima=0` | apagar el clima, dejar solo el reloj | encendido |
| `cada` | cada cuántos minutos se pide la temperatura | 10 |

Las coordenadas de fábrica son del centro de la comuna de Independencia
(`-33.4172, -70.6647`), no del estadio; a esta escala el modelo no distingue, así que
afinarlas es cosmético.

## Lo que se aprendió al probarlo en OBS (2026-09-02)

La primera versión se veía bien en Chrome y mal en OBS. Las dos causas:

- **El nombre del estadio se cortaba.** El archivo usaba `system-ui`, que en Chrome
  resuelve a Segoe UI pero en el CEF de OBS caía en otra fuente con métricas distintas,
  y el texto ya no entraba en la caja. Se arregló por los dos lados: **las tipografías
  van incrustadas en base64** (copiadas de `gc/css/fuentes.css`, Outfit para títulos y
  Mulish para cuerpo), así Chrome y OBS miden igual; y **el título y la bajada envuelven
  en vez de truncar**, así aunque una fuente mida distinto de lo previsto el texto nunca
  desaparece. Un nombre cortado al aire es un error que ve el público; una segunda línea
  no lo es.

  Si se regeneran las fuentes en `gc/`, hay que volver a copiarlas acá a mano.

- **El clima no cargaba, y no era culpa de OBS.** Primero se descartó lo obvio: el
  `fetch` desde `file://` funciona en Chrome con y sin `--allow-file-access-from-files`,
  y la API devuelve `access-control-allow-origin: *` con `Origin: null`. La causa real
  apareció al probar: **Open-Meteo estuvo devolviendo HTTP 503 durante unos minutos** y
  después volvió sola (15 de 15 consultas OK al rato). O sea, en el navegador se pegó un
  momento bueno y en OBS uno malo. No era CORS ni el CEF.

  El defecto de verdad era del archivo: **una sola consulta fallida lo dejaba sin
  temperatura hasta el siguiente ciclo**, diez minutos después. Ahora una falla se
  reintenta con esperas crecientes —10, 20, 40, 60, 60 s— y el ciclo normal resetea ese
  contador, así que después de una caída larga la vuelta siguiente recupera sus
  reintentos rápidos.

  De paso quedó un segundo camino: si `fetch` falla se reintenta con **XMLHttpRequest**,
  que en los CEF viejos suele pasar donde `fetch` rebota. Cuesta veinte líneas y saca de
  encima toda una familia de fallas que solo aparecerían al aire.

  **Si algo raro vuelve a pasar, abrir la franja con `?diag=1`**: aparece un cartel negro
  arriba a la izquierda con el user agent, el protocolo, si existe `fetch`, el ancho real
  de la Browser Source y el error exacto de cada intento. Es la única forma cómoda de leer
  un error dentro de OBS, donde no hay consola a mano. El cartel **nunca** aparece sin ese
  parámetro.

  Plan B si el diagnóstico no alcanza: servir la carpeta por HTTP con `gc/servidor.ps1` y
  apuntar la Browser Source a `http://localhost:...` en vez de al archivo.

## Los logos de los auspiciadores

Están en `auspicios/`, y los originales que llegaron quedaron en `auspicios/originales/`.

**Los del directorio de trabajo están reprocesados**: renombrados sin espacios ni
mayúsculas (van dentro de un query string) y reescalados a 300 px de alto. El original de
Office Designs medía **6201×6005 px**, o sea unos 149 MB en memoria descomprimidos para
dibujar 80 píxeles en pantalla — en un notebook que está codificando video eso no da lo
mismo. La carpeta pasó de 3 MB a 416 KB. Si llega material nuevo, hay que pasarlo por lo
mismo.

**Los logos que llegaron son casi todos cuadrados** (muni 1.04, deportes 1.09, office
1.03, corro 1.29, indie 0.86); los apaisados son foco, 4:1, y cinnalove, 2.60:1. Eso importa porque a un logo cuadrado
la altura le fija también el ancho: en el primer intento iban a 3,2rem y quedaban en 53 px
de ancho, con el texto de adentro ilegible al aire. Por eso ahora van a **5rem** y la
franja entera es más alta.

**Igualar por altura no iguala el peso visual**: a la misma altura, un logo cuadrado se ve
más grande que uno apaisado. Cada auspiciador acepta una `escala` que es corrección óptica
a ojo, no hay fórmula.

**En las tandas de auspiciadores el rótulo es una columna a la izquierda** y los logos van
centrados en lo que queda. La tanda de la sede no cambia: sigue con el rótulo arriba y todo
alineado a la izquierda, porque es texto corrido y se lee de izquierda a derecha.

Antes el rótulo iba en su propia línea arriba, y esa línea se comía 1,65rem del presupuesto
vertical (0,95 de texto más 0,7 de margen). Pasándolo al costado, esa altura queda entera
para los logos: subieron de 5 a **6,4rem**, o sea de 80 a 102 px, un 28% más grandes, **sin
que la franja crezca** — medido, las dos tandas siguen dando 139,19 px exactos.

Se probó antes dejarlo flotando en la esquina superior, fuera del flujo, que gana la misma
altura. **No sirve**: con los logos centrados, la tanda del municipio deja 176 px libres a
cada lado y el rótulo mide 180 — se pisaban. Como columna no puede chocar nunca, sin
importar cuántos logos haya.

El bloque de texto de cada auspiciador va alineado a la izquierda: el nombre y el @ tienen
anchos distintos, y centrados entre sí quedan escalonados.

**El nombre del auspiciador va a 1,6rem**, el 80% del título de la sede (2rem). Es la
información principal de *su* lámina y tiene que pesar como tal, pero no puede ir al mismo
tamaño que ese título: allí hay un solo texto y acá hay dos o tres compitiendo entre sí y
con su propio logo, y el ancho tampoco daría cuando entre el tercer auspiciador.

**Cada logo va acompañado de su nombre tipografiado**, y eso no es decoración. Los archivos
que llegaron son sellos institucionales — el de la Corporación de Deportes es un círculo
con el texto curvo en el borde — y a cualquier tamaño que entre en una franja inferior ese
texto no se lee. Con el nombre al lado, el logo pasa a ser un ícono y la lectura la
resuelve la tipografía, que para eso está. De paso llena el ancho que sobraba.

En la tanda del municipio se usa **`muni-escudo.png`**, el escudo solo, y no la versión que
trae el texto adentro: teniendo el nombre al lado, esa versión repetía lo mismo dos veces.

**En la tanda de foco el orden se invierte:** la url va en la línea del nombre y el giro
abajo, al revés que en los demás. El logo ya dice FOCO, así que repetirlo al lado no suma,
y lo que hay que llevarse de esa lámina es la dirección — puesta como nombre queda en
1.6rem/800 en vez de los 1.05rem grises del renglón de abajo.

La línea de abajo de cada uno lleva el **@ de Instagram**, no la URL completa: en pantalla
un `@_corro_` se lee y se recuerda, `https://www.instagram.com/_corro_/` no.

**El logo de foco se cambió el 2026-09-02** por una versión con el lettering en oscuro: la
anterior era gris plateada con degradado y sobre el crema quedaba lavada. Al reemplazar un
original hay que volver a generar el de trabajo — los de `auspicios/` no se actualizan
solos.

Si algún logo llega sin transparencia, `?fondologos=1` le pone una tarjeta blanca a
**todos**, así quedan parejos en vez de que uno se vea como un recuadro pegoteado.

## Montaje en OBS

- **Desactivar *Shutdown source when not visible* y *Refresh browser when scene becomes
  active***, o el overlay se recarga entero cada vez que se vuelve a la escena y el
  ciclo del slot arranca de nuevo.
- **La casilla "Archivo local" descarta el query string.** Para pasar parámetros hay que
  destildarla y pegar la URL `file:///...` completa en el campo *URL*.

### Tamaño de la Browser Source: 1920 × 1080 y Ctrl+R

**Dejarla del porte del lienzo y restablecer la transformación.** Es la opción sin pasos
manuales: la franja cae sola donde tiene que caer y es imposible que dos escenas queden a
distinta altura, porque no hay nada que posicionar.

Se probó recortarla a 1920 × 220 y **no vale la pena**. El ahorro es chico: CEF repinta por
rectángulos sucios, así que lo único que se repinta cada segundo es el reloj y ese
rectángulo mide igual en los dos casos; las animaciones son `transform` y `opacity` sobre
la caja, no sobre el lienzo. Lo único que cambia de verdad es la textura —8,3 MB por búfer
contra 1,7— y una pasada de mezcla alfa a pantalla completa en vez de un quinto. Medible,
pero no es lo que bota cuadros.

Y recortarla trae un problema propio: hay que subir la fuente a mano en cada escena. El
2026-09-03 pasó justo eso — al bajar el navegador de 1080 a 220, el ítem se quedó con el
cuadro delimitador de "ajustar al lienzo" que traía de antes y OBS centró los 220 px al
medio de la pantalla. No era la medida, era el cuadro heredado; Ctrl+R lo arregla.

**Para que no dependa del pulso de nadie: la franja va en su propia escena** y esa escena se
agrega como fuente en las demás. La posición vive en un solo lugar.

Eso además es lo único que sí cuesta caro acá: **una instancia de navegador por fuente, no
por escena.** Duplicar la franja como fuente nueva en cinco escenas levanta cinco CEF en
paralelo; con la escena anidada —o con *Añadir existente*— es uno solo compartido. Ahí se
juega un orden de magnitud. En los pixeles transparentes, nada. Si alguna vez se caen
cuadros, el orden para revisar es: cuántas instancias hay corriendo, qué está animando y a
qué frecuencia, y recién al final el tamaño de la fuente.

### La laptop que corre esto

Es una **HP 15-ef1018la**: Ryzen 5 4500U (6 núcleos, 15 W), 16 GB DDR4-2666 en 2 módulos
(doble canal), Radeon Vega 6 integrada, SSD NVMe de 256 GB. Es la misma laptop que corre el
GC de foco.cl en el mismo evento.

Analizado el 2026-09-03: la duda era si el HTML era muy pesado y convenía cambiarlo por un
video con alpha o por PNG encima. **No y no.** El costo no es el HTML, es tener una
instancia de CEF viva por Browser Source, anime o no. Un video con alpha sería peor:
decodifica en CPU para siempre —el mismo recurso que pelea con el encoder— y aun así
necesitaría un Browser Source encima para la hora y el clima.

Lo que sí decide si aguanta, en orden:

1. **Encoder AMD HW H.264, nunca x264.** El 4500U es Renoir con VCN 2.1 (encoder por
   hardware). Si esa opción no aparece en OBS es driver viejo de HP.
2. **Recortar cada Browser Source a su tamaño real.** La Vega 6 no tiene VRAM propia, usa
   la RAM del sistema y esa banda la comparten CPU, compositing, sources y encoder. Por
   eso el análisis de más arriba de si vale la pena recortar la Browser Source a 1920×220
   (no vale, ver esa sección) se hizo con esto en mente.
3. **Apagar la previa de OBS y no usar Modo Estudio.** Con el proyector en el monitor HDMI
   no se pierde monitoreo.
4. **Térmica.** Chasis plástico y 15 W: enchufada, máximo rendimiento, superficie dura.

Estimado con las cuatro gráficas del torneo (franja, comentarios, mosca e info de
partidos) puestas a la vez: CPU 25-40%, GPU con holgura, RAM sin problema. Los riesgos de
caída de verdad no son el peso de estos archivos: el SSD llenándose si se graba (~4,5
GB/hora a 1080p30), los dos USB de captura compartiendo controlador (cortes de audio), y
un reinicio del driver AMD por el proyector a pantalla completa junto con la aceleración
por hardware de las Browser Sources — se mitiga con el proyector en ventana.

Si alguna vez hace falta la fuente acotada, los números están medidos. La franja **no usa
el alto para nada**: la escala sale solo del ancho (`html { font-size: calc(100vw / 120) }`,
o sea 1rem = 16 px a 1920) y la caja está anclada por `bottom: 3.5rem`. Alto de más queda
transparente arriba y no corre la gráfica ni un pixel; alto de menos la corta. Medido en
Chrome el 2026-09-03 recorriendo todas las tandas, a 1920 de ancho:

| | caja | + los 3,5rem de abajo | a 1920 |
|---|---|---|---|
| sede en una línea y todas las tandas de logos | 8,7rem | 12,2rem | **196 px** |
| sede en dos líneas (un nombre largo) | 9,9rem | 13,4rem | **215 px** |

Así que 1920 × 200 queda al filo —entran los 196 px de hoy, pero no un título que
envuelva— y el número correcto es **220**, pegada al borde de abajo: en 1080p, posición
X = 0, Y = 860, alineación arriba-izquierda y sin cuadro delimitador, **escrito en Editar
transformación**, no arrastrado. A otro ancho, alto = ancho ÷ 8,7.

Los 56 px transparentes que sobran abajo de la caja no son un error de encuadre: son la
safe area de TV, y son los que hacen que la fuente se pueda alinear al borde sin cálculos.
Con `?pos=arriba` es lo mismo al revés — misma altura, alineada al borde de arriba.

## Los cuatro de los premios van en dos páginas

El 2026-09-03 llegaron dos auspiciadores más para el grupo de los premios —**Cinnalove**
(`@cinnalove.cl`, rollos de canela) e **Indie** (`@indie.entrueno`, café de especialidad)—
y quedaron cuatro. Se midió si cabían los cuatro en una sola tanda: **no caben.**

Medido a 1920 con los logos ya procesados. Al slot le quedan **1099 px** para la fila,
después del rótulo (259 px) y del bloque de clima y hora. Los cuatro piden **1309 px**:

| | logo | nombre + @ | total |
|---|---|---|---|
| Corro | 132 | 75 | 225 |
| Office Designs | 106 | 174 | 298 |
| Cinnalove | 256 | 120 | 394 |
| Indie | 88 | 133 | 239 |
| separaciones (3 × 51) | | | 153 |
| | | | **1309** |

Sobran 210 px, un 19%. Y no se acomodan solos: `.auspicio-nombre` va con
`white-space: nowrap`, así que en vez de reflowear se montan — el nombre de uno queda
encima del logo del siguiente y el último `@` se corta contra el borde. La captura está en
`pruebas/4logos-en-una.png`. Bajarle la escala a Cinnalove tampoco alcanza: aun a .7
quedan 1246 px pedidos contra 1099, y de paso su lettering vuelve a ser ilegible, que es
justo lo que arregló subir los logos de 5 a 6,4rem.

Así que van en **dos tandas con el mismo rótulo**, "Con la colaboración de". No es un
grupo nuevo: los cuatro aportaron lo mismo, e inventarles una distinción para justificar
la separación sería mentira. Con el rótulo repetido la columna de la izquierda no se mueve
y el cambio se lee como lo que es, la vuelta de página de una sola lista.

**El tiempo no fue argumento en la decisión:** como la duración se calcula a 7 s por logo,
las dos opciones dan lo mismo, 59 s de vuelta (10 + 14 + 28 + 7 juntos, 10 + 14 + 14 + 14 + 7
separados).

**El fundido tuvo que aprender el caso.** El cambio de tanda apaga `.contenido-cuerpo`
entero, rótulo incluido, y con dos tandas seguidas que dicen lo mismo ese rótulo se
apagaba y se encendía para volver a decir exactamente lo mismo: al aire eso no se lee como
un cambio de tanda, se lee como un parpadeo. Ahora `cambiarTanda` compara el rótulo de la
que viene con el de la que sale y, si son iguales, usa la clase `cambiando-pagina`, que
funde `#tandas` en vez de `.contenido-cuerpo` — el rótulo se queda quieto y solo pasan los
logos. **No se puede resolver desde el hijo**: la opacidad se la lleva `.contenido-cuerpo`
y ningún descendiente puede deshacerla. Sale de comparar los textos y no de una marca en
la tanda, así que vale para cualquier grupo que se parta en el futuro sin que nadie tenga
que acordarse de marcarlo.

Verificado el 2026-09-03 corriendo una vuelta entera en modo rápido: sin errores de JS,
`cambiando-pagina` se dispara una sola vez por vuelta y las cuatro tandas miden 139,19 px
exactos, o sea el bloque de clima no salta.

### Las tres líneas de cada auspiciador

Desde el 2026-09-03 cada uno lleva **nombre, bajada y @**, y no solo nombre y @:

    Corro                        Cinnalove
    Aventura & Vida              Rollos de canela Gourmet | Pastelería inclusiva
    @_corro_                     @cinnalove.cl

**La bajada va en la tinta oscura y no en el gris del @**, porque es información y no un
pie de foto: el público no conoce estas marcas y "Café de especialidad" es lo que le dice
de qué se trata. La jerarquía entre las tres líneas la hacen el tamaño y el peso
—1.6/800, 1.15/500, 1.05/500— y recién la tercera cambia de color. Con las tres del mismo
tono el bloque se leía como un párrafo.

**No cambió la altura de la franja.** El bloque de texto pasó de 31 a 77 px, pero el que
manda sigue siendo el logo con sus 102 px, así que las cinco tandas siguen midiendo
139,19 px y el reloj no se mueve. Medido el 2026-09-03.

### La separación entre logos va por tanda

Dentro de un auspiciador el logo y su texto están a 1.1rem, así que la separación entre
uno y otro es lo único que dice dónde termina el primero y empieza el segundo. Empezó en
3.2rem, donde la razón entre las dos era de 2,9× y las cuatro piezas se leían como una
fila pareja en vez de como dos grupos.

**No es un solo número global.** El `gap: 5.5rem` del CSS es el piso, y lo usa la tanda
del municipio, que es la más apretada de todas: con dos nombres largos le quedan 98 px de
holgura a 1920 y subirla más la deja sin red. Las demás van sobrando de ancho y no tienen
por qué quedar castigadas por ella, así que **cada tanda puede pedir la suya con el campo
`separacion` de `TANDAS`, en rem**, que se aplica como `style.gap` del grupo. Las dos de
colaboración van a 9.

Holguras medidas a 1920 después de eso:

| tanda | separación | disponible | pide | holgura |
|---|---|---|---|---|
| muni + deportes | 5.5rem | 1208 | 1110 | **98 px** |
| Corro + Indie | 9rem | 1121 | 719 | 402 px |
| Cinnalove + Office Designs | 9rem | 1121 | 942 | 179 px |
| foco | 5.5rem | 1271 | 587 | 684 px |

**Las dos láminas de colaboración llevan la misma separación a propósito:** son dos
páginas de una misma lista, y si cambiara de una a otra el corte se vería como un
reacomodo en vez de como una vuelta de página.

### Los dos que llegaron

- **Cinnalove** — el original bueno es **`originales/cinnalove.cl-sitio.png`**, que llegó
  el 2026-09-03 y reemplaza al primero. Su bajada quedó en "Rollos de canela Gourmet" a
  secas: la bio completa de instagram agrega " | Pastelería inclusiva" y se estiraba
  demasiado para la lámina.

      magick originales/cinnalove.cl-sitio.png -resize x300 \
             -fill "#374151" -colorize 100 -strip PNG32:cinnalove.png

  **Es otro dibujo, no el mismo más grande:** el primero era el corazón arriba y CINNALOVE
  en una línea; este es un lockup horizontal, corazón a la izquierda y CINNA / LOVE
  apilado. La proporción casi no cambia (2.60 contra 2.69) pero **la letra ocupa mucho más
  dentro de la caja**, así que a la misma altura pesa bastante más. Por eso bajó de
  `escala: .85` a **`.75`**, que es donde queda pareja con Office Designs; probadas también
  1 y .85, y a 1 aplasta a su vecina.

  **`originales/cinnalove.cl.png`, el primero, quedó obsoleto** y no hay que reprocesarlo.
  Era un recorte hecho sobre un JPEG y tenía el **11,1%** de sus píxeles en alfa 6–40: una
  nube de puntitos alrededor del corazón que al aire se leía como un mal recorte. Eran los
  bloques de 8×8 de la compresión, convertidos en píxeles semitransparentes al borrar el
  fondo. Se limpiaba con `-channel A -level 20%,100% +channel` puesto antes del
  `-colorize`, pero ni así se arreglaba el trazo, escalonado por lo mismo, ni sus
  269×100 px, que eran exactamente los ~102 px que ocupa en pantalla a 1080p y nada más.
  **El de ahora mide 914×352, tiene el halo en 0,3% y son dos colores planos**, así que no
  necesita umbral de alfa: entra derecho al reprocesado normal.

- **Indie** — 264×306, reescalado a 259×300 como el resto. Es un pájaro sobre una
  bicicleta, que en un torneo de bicipolo no podría calzar mejor.

## Los logos de colaboración van en gris, no en la tinta

Decisión del 2026-09-03. **Corro, Indie, Office Designs y Cinnalove están repintados a
`#374151`** — el `gray-700` de Tailwind, **8,9 : 1** de contraste sobre el crema.

    magick logo.png -fill "#374151" -colorize 100 logo.png

`-colorize 100` reemplaza el color de cada píxel **sin tocar el alfa**, así que el
antialias del contorno se conserva entero. Los originales sin retocar siguen en
`originales/`, que es de donde hay que partir para rehacer cualquiera.

**Por qué gris y no la tinta del tema.** Se probó primero `#16203f`, con el argumento de
que es el mismo color del nombre que va al lado y así logo y texto se leen como una pieza.
Al mirarlo no funcionó: **con el logo y su texto exactamente del mismo color, la lámina se
lee como un bloque plano** y el logo pierde su condición de imagen. En un gris que no es
el color de ningún texto de la franja, el logo queda un paso atrás del nombre —que es
donde corresponde, porque el nombre es la información— y la lámina recupera profundidad.

**Por qué este gris y no un neutro.** El primer intento fue `#3d3d3d`, gris puro. Se veía
bien pero quedaba fuera de tono: medidos en HSL, todos los colores de la franja son fríos
—la tinta a 225°, el gris del @ a 221°— y un neutro de saturación 0 era lo único del
cuadro sin tono. El `gray-700` de Tailwind cae en **217° con 19% de saturación**, o sea
prácticamente la misma familia que el `#5d6880` del @, solo que más oscuro: es el escalón
que le faltaba a la paleta, no un color nuevo.

De dónde sale la escala: **el proyecto `torneos bicipolo`** (en
`../../../torneos bicipolo/`) usa la escala `gray` de Tailwind en su frontend. No hay ahí
una paleta gráfica de grises definida —las miniaturas del torneo son rojo `#ED2025` y
crema `#FBF8F5`, y los grises aparecen solo como chrome de pantallas de admin—, pero es la
escala que ese proyecto ya usa, y da la casualidad de que su tono es el mismo que el de
esta franja.

**Por qué el 700 y no otro escalón.** Probados los cuatro sobre la lámina real:

| gris | contraste | |
|---|---|---|
| `#1f2937` (gray-800) | 12,7 : 1 | tan cerca de la tinta que vuelve el problema de origen |
| **`#374151` (gray-700)** | **8,9 : 1** | **el elegido** |
| `#4b5563` (gray-600) | 6,5 : 1 | la microtipografía de Corro empieza a ceder |
| `#3d3d3d` (neutro) | 9,4 : 1 | legible, pero es el único sin tono en toda la franja |

El techo por abajo lo ponen los trazos más finos del grupo —"BICICLETA & VIDA" de Corro y
los rayos de la bici de Indie—, que la compresión de video se come antes que nada. De
referencia en la misma franja: los nombres dan 13,8 : 1 y el @ y la dirección, 4,8 : 1. El
logo queda entre medio, que es exactamente su lugar en la jerarquía. La comparación quedó
en `pruebas/escala-de-grises.png`.

**Dos excepciones, y las dos a propósito:**

- **Los municipales no se tocan.** El escudo de la comuna y el sello de la Corporación de
  Deportes son marcas institucionales a todo color y repintarlas sería impresentable.
  Además su tanda funciona bien justamente por el contraste: logos coloridos, texto fuerte.
- **`foco.png` tampoco**, y por dos razones distintas. Una: no es una silueta plana, tiene
  43.432 px de gris medio `#939393` y 15.486 de `#646464` que son el cono plateado, así que
  `-colorize` lo aplasta en una mancha. Se probó `+level-colors "#374151",white`
  —restringido con `-channel RGB`, o se lleva puesto el alfa y queda un recuadro sólido—
  que sí conserva el volumen. Y ahí aparece la segunda razón, que es la que manda: **lo
  aclara justo hacia lo que se corrigió el 2026-09-02**, cuando se cambió el logo por esta
  versión de lettering oscuro porque la plateada quedaba lavada sobre el crema. Descartado.
  La prueba quedó en `pruebas/foco-gris.png`.

## comentarios.html — el chat de YouTube

Segunda gráfica, archivo aparte, misma familia visual: mismo crema, mismo canto rojo,
mismas fuentes incrustadas. Va arriba de la franja y contra el mismo borde derecho, así
las dos quedan alineadas por el canto.

Para qué: entre partido y partido, rescatar lo que dijo la gente que está mirando. Se
prende la fuente en la pausa y se apaga cuando vuelve el juego.

**Lo que más importa: si no hay ningún comentario nuevo, no se muestra nada.** La página
queda transparente y vacía, no aparece una caja diciendo "sin comentarios". Esa es toda
la gracia — el operador prende la fuente sin tener que revisar antes si había algo.

### Se puede sin servidor porque googleapis devuelve el Origin

Misma verificación que se le hizo a open-meteo, mandando `Origin: null`, que es lo que
manda un archivo local:

    HTTP/1.1 403 Forbidden
    Access-Control-Allow-Origin: null

Google devuelve el origen de vuelta en vez de rebotarlo, así que desde `file://` se puede
leer la respuesta. **Verificado el 2026-09-03.**

### La clave va en la URL, no en el archivo

Es la única diferencia real con el clima: open-meteo no pide token, y YouTube sí. La
clave se pasa por query string (`?key=`), igual que el resto de la configuración, y por lo
tanto vive en el campo URL de la fuente de OBS —o sea en el scene collection de esa
máquina—. El HTML se puede compartir, versionar o mandar por correo sin la credencial
adentro. Se saca gratis en `console.cloud.google.com`: proyecto nuevo, habilitar
**YouTube Data API v3**, crear una credencial del tipo clave de API. Sin restricción de
sitio web: desde `file://` no hay referer que valga.

Consumo: `liveChat/messages` cuesta 5 unidades de cuota por consulta y la cuota diaria
gratis son 10.000. Consultando cada 30 s son 600 unidades por hora, o sea unas ocho horas
seguidas por día, y se renueva sola. Espaciar más no pierde mensajes: cada consulta trae
todo lo que se dijo desde la anterior.

### Por qué no hay panel para elegir cuál sale

Porque no hay servidor. En OBS el `localStorage` del dock está aislado del de la Browser
Source, así que una ventana de control no tiene cómo hablarle a la fuente que está al
aire — ese fue justamente el motivo por el que el GC de foco terminó con un
`servidor.ps1`. La aprobación uno por uno vive allá (sección "Chat en vivo" del panel).

Acá lo que reemplaza a la moderación son los filtros, todos por query string: sin links
de fábrica, largo máximo, palabras vetadas, nombres ignorados. Con diez personas mirando,
todas conocidas, alcanza de sobra. El control que queda es el bueno: prender y apagar la
fuente en la escena.

### Comportamiento al aire

- **Nunca aparece un error en pantalla.** Si la API falla, no se muestra nada y el error
  va a la consola, o al cartel de `?diag=1`. Una caída de red se reintenta sola a los 5,
  15 y 45 s; una clave mala deja de insistir, porque no se arregla insistiendo.
- **Lo ya mostrado se recuerda en `localStorage`, por video.** Sin eso, cualquier recarga
  de la fuente en medio de la transmisión volvería a sacar al aire todo lo que ya había
  salido. Con `?olvidar=1` se arranca en blanco, que es lo que se quiere al ensayar.
- **Un comentario se marca como visto al encolarlo, no al mostrarlo.** Si no, la consulta
  siguiente —que llega mientras ese mismo mensaje espera su turno— lo volvería a encolar y
  saldría dos veces seguidas.
- **`?demo=1` llena la cola con comentarios inventados**, sin clave, sin internet y sin
  transmisión. Es el equivalente de `?logos=` en la franja: sirve para dejar montada y
  probada la fuente en OBS antes del evento, que es cuando conviene descubrir que la
  casilla "Archivo local" se estaba comiendo el query string.
- **El ciclo no tiene deriva:** cada comentario dura exactamente `hold + gap`, medido
  entre arranques de animación. Mismo criterio que el slot de la franja.

### Los 14rem de separación están medidos

La franja va a 3,5rem del borde y mide unos 9rem de alto, o sea que su canto superior
queda a 12,5rem. La tarjeta va a 14rem, con 1,5rem de respiro. **Con 11,5rem se le montaba
encima** — se vio en una captura de las dos juntas el 2026-09-03. Si la franja cambia de
alto, `?abajo=` corre la tarjeta sin tocar el archivo.

La comprobación quedó en `pruebas/comentarios-sobre-franja.png`, hecha con la tanda de
logos, que es la más alta de las cinco.

### Configuración

| Parámetro | Para qué | De fábrica |
|---|---|---|
| `key` | clave de API de YouTube | **obligatorio** |
| `video` | URL de la transmisión, o el ID pelado | **obligatorio** |
| `rotulo` | el rótulo chico de arriba | En el chat |
| `hold` | segundos que cada comentario queda en pantalla | 8 |
| `gap` | segundos de respiro entre uno y el siguiente | 1.2 |
| `cada` | cada cuántos segundos se le pregunta a YouTube | 30 |
| `demora` | segundos antes del primero | 0.4 |
| `pos=izq` | manda la tarjeta al borde izquierdo | derecha |
| `abajo` | rem desde el borde inferior | 14 |
| `ancho` | ancho de la tarjeta, en rem | 42 |
| `maxlargo` | descarta comentarios más largos que esto | 140 |
| `minlargo` | descarta los más cortos | 2 |
| `links=1` | permite comentarios con direcciones web | no |
| `veta` | palabras vetadas, separadas por coma | — |
| `ignora` | nombres a ignorar, separados por coma | — |
| `desde` | solo comentarios de los últimos N minutos | 0 (todos) |
| `repetir=1` | vuelve a mostrar los ya mostrados cuando se acaban | no |
| `olvidar=1` | arranca sin memoria de lo ya mostrado | no |
| `demo=1` | comentarios inventados, sin clave ni internet | — |
| `diag=1` | cartel de diagnóstico en pantalla | apagado |

Igual que con la franja: **la casilla "Archivo local" de OBS descarta el query string**.
Hay que destildarla y pegar la URL `file:///...` completa en el campo URL.

**En `?video=` conviene pegar el ID pelado** (los once caracteres, `dQw4w9WgXcQ`) y no la
URL entera. La URL funciona —el `?` de adentro no molesta— pero si trae un `&list=` o un
`&t=`, ese `&` corta el query string de esta página y el resto de los parámetros se
pierden en silencio. El ID no tiene ese problema nunca.

## Pendientes

- **`previa.html` tiene los índices de las tandas escritos a mano** y no puede adivinarlos:
  el iframe es `file://`, o sea otro origen, y no hay forma de preguntarle. Al agregar o
  sacar una tanda en `TANDAS` hay que ir a corregir esa lista o los botones quedan
  apuntando a la de al lado. Ya pasó el 2026-09-03 al entrar la segunda lámina de
  colaboración: el botón "Transmite" seguía en `tanda=3` y lanzaba la que ahora ocupa ese
  lugar.

- **Ningún rótulo nombra los premios ni dice "Auspician".** La tanda de Corro y Office Designs
decía "Premios cortesía de" y el usuario la cambió el 2026-09-03 por **"Con la
colaboración de"**: "cortesía de" suena a traducción del inglés, y además prefiere no
nombrar lo que donaron. Ningún rótulo lleva dos puntos al final. El municipio presta el espacio, un par de empresas
  donaron premios y el streaming lo pone la casa: nadie aportó dinero, así que "Auspician"
  quedaba grande y "Colaboran" quedaba vago. Cada tanda dice lo que corresponde.
- **`comentarios.html` y los originales (`cintillo-sede.html`, `reloj-clima.html`) siguen
  con las fuentes del sistema.** `franja-inferior.html` ya las lleva incrustadas en base64
  (Outfit/Mulish). Si se quiere el mismo blindaje en `comentarios.html`, hay que volver a
  copiarlas a mano — desde el 2026-09-04 este proyecto y el GC de foco.cl son dos repos
  separados (`gc-bicipolo` y `gc-overlays`), así que ya no hay una carpeta `gc/` común de
  la que tirar.
- **La integración con el GC de foco.cl queda descartada por ahora.** Este proyecto se
  separó de ese repo el 2026-09-04 y vive solo, como páginas HTML sueltas que no hablan
  con `panel.js`, `estado.js` ni `canal.js`. La pregunta que quedaba abierta en
  `../cintillo-sede/LEEME.md` —reutilizar el `lower` del GC o crear un tipo de gráfica
  nuevo— ya no aplica salvo que en algún momento se decida volver a unir los proyectos.
