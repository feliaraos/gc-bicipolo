---
name: cintillo-sede-bicipolo
description: Franja inferior con la sede de la transmisión, traída desde el proyecto de bicipolo el 2026-08-30 para integrarla al GC
metadata: 
  node_type: memory
  type: project
  originSessionId: a82cf2dc-4acd-4146-bea5-9e44910e3376
  modified: 2026-08-30T00:00:00.000Z
---

En `taller/cintillo-sede/` hay una franja inferior a todo el ancho con la sede desde donde se
transmite: pin de ubicación animado, `EN VIVO desde el` + nombre del recinto, dirección debajo, y
un ciclo propio de 30 s puesta / 30 s oculta mientras la escena esté al aire.

Se escribió el 2026-08-30 en `c:\torneos.bicipolo` como solución única para el torneo de bicipolo
de ese fin de semana, y el usuario decidió traerla acá para seguir trabajándola con la línea
gráfica del GC. La copia que sale al aire ese fin de semana ya está en su carpeta de streaming;
esta es la que se sigue desarrollando.

**Ya no se desarrolla acá.** El 2026-09-02 se fusionó con `reloj-clima.html` en
`taller/horayclima/franja-inferior.html`, que es la gráfica viva; este archivo queda como
original de referencia. Ver [[franja-inferior-torneo]]. Lo de abajo describe el estado en que
quedó y sigue valiendo como historia.

**Nunca llegó a ser una gráfica del GC**: es una página suelta con paleta propia (negro 80% + acento
`emerald-400`, copiada del overlay `timeline` de la app BPT1 para poder convivir con ella en la
misma escena). El tema `bicipolo.json` de acá es azul sobre fondo claro, así que hay una decisión
de identidad pendiente. Lo demás pendiente —adoptar las variables de tema, enganchar
`css/fuentes.css`, y elegir entre reutilizar el `lower` o crear un tipo de gráfica nuevo— está
detallado en `taller/cintillo-sede/LEEME.md`.

Está en `taller/` a propósito: no se necesita para transmitir y no debe viajar en el pendrive con
`gc/`. Cuando quede integrada, se muda a `gc/` y la carpeta desaparece. Contexto del proyecto de
origen: [[bicipolo-overlays-obs]].

**Hallazgo que sirve para todo el GC**: `backdrop-filter: blur()` no hace nada en OBS. El video
está en otra capa del compositor, no detrás del documento, así que un fondo de vidrio esmerilado
hay que resolverlo con un degradado sólido.
