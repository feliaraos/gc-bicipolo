# GC Bicipolo

Gráficas para la transmisión del Torneo Nacional de Bicipolo 2026: overlays HTML sueltos,
sin servidor ni panel de control, pensados para montarse directo como Browser Source en OBS.

Este proyecto se separó el 2026-09-04 del repo del GC de foco.cl (`gc-overlays`), donde
había crecido mezclado dentro de una carpeta `taller/`. No dependen de ese proyecto ni de
ningún otro: cada HTML de acá es autocontenido y se abre tal cual, por archivo o por URL
con parámetros.

## Qué hay

- **[`horayclima/`](horayclima/)** — la carpeta activa.
  - `franja-inferior.html` es la gráfica que sale al aire: sede / auspiciadores rotando a
    la izquierda, hora y clima fijos a la derecha. Ver
    [`horayclima/CONTEXTO.md`](horayclima/CONTEXTO.md) para la arquitectura completa,
    todos los parámetros de configuración y las decisiones ya tomadas — es la referencia
    antes de tocar el archivo.
  - `comentarios.html` es el chat en vivo de YouTube, gráfica aparte que acompaña a la
    franja en las pausas.
  - `previa.html` es el banco de pruebas: se abre con doble clic, sin montar nada en OBS.
  - `reloj-clima.html` es el original de hora + clima, ya fusionado en `franja-inferior.html`;
    queda como referencia.
- **[`cintillo-sede/`](cintillo-sede/)** — el original de la franja de sede, también ya
  fusionado en `franja-inferior.html`. Queda como referencia histórica; ver
  [`cintillo-sede/LEEME.md`](cintillo-sede/LEEME.md).

## Cómo se montan

Cada gráfica es su propia Browser Source en OBS, apuntando al archivo local o servida por
HTTP. Los detalles de montaje (tamaño de la fuente, por qué no conviene recortarla, qué
casillas de OBS hay que destildar) están en la sección "Montaje en OBS" de
[`horayclima/CONTEXTO.md`](horayclima/CONTEXTO.md).
