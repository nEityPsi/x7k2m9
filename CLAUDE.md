# Mis Directos

Web app personal de resultados deportivos en directo para iPhone. Se instala desde Safari con *Compartir → Añadir a pantalla de inicio*.

- Web publicada: https://neitypsi.github.io/x7k2m9/ (GitHub Pages, rama `main`, carpeta raíz).
- Todo el código está en `index.html`: HTML, CSS y JS en un solo archivo, sin dependencias ni paso de compilación.
- Iconos: `icon-180.png` (apple-touch-icon), `icon-512.png` y `icon-32.png`. El icono elegido es un marcador "2 | 1" en líneas blancas sobre verde césped liso.
- `.nojekyll` evita que GitHub Pages procese los archivos.

## Cómo trabajar con el usuario

- Responde en español, de forma concisa y directa.
- Quiere una app muy simple y minimalista: pocos elementos, iconos sencillos y nada de texto que sobre.
- Para cambios de diseño importantes (icono, estética), propón opciones y pregunta antes.
- Un cambio solo se ve en el móvil cuando llega a `main`. Hasta ahora los cambios se subían directamente a `main`.
- Tras publicar, el usuario recarga la web en Safari en 1–2 minutos. Si cambia el icono, debe borrar el acceso de la pantalla de inicio y volver a añadirlo; sube `?v=N` en los enlaces del icono para forzar la recarga.

## Funcionamiento

- **Cabecera:** `●` (solo en directo) · `‹` día anterior · día (Hoy/Ayer/Mañana + fecha; tocar vuelve a hoy) · `›` día siguiente · `✓` (solo finalizados). Los filtros solo aparecen en Hoy, y el filtro elegido se recuerda en `localStorage`. También se puede deslizar a los lados para cambiar de día.
- **Lista:** agrupada por competición. Cada fila muestra local, escudo, marcador o hora, escudo y visitante. Los partidos en directo van en rojo. Los marcadores de 3 cifras usan la clase `.sc.long` para caber.
- **Al tocar un partido:**
  - Fútbol: goleadores con minuto (pen./p.p.) y tarjetas rojas.
  - Baloncesto y NFL: filas con etiqueta central (Balance, Cuartos, Puntos/Rebotes/Asistencias o Yardas pase/carrera/rec.). En la NFL en directo también salen la posesión, el down y la última jugada.
  - Los líderes se piden al pulsar: ESPN `summary`, o el `Boxscore` de la Euroliga.
- **Pie:** hora de la última actualización (tocar actualiza) y botón **Ligas**, que abre el selector.
- **Actualización:** cada 30 s si se ve Hoy. Al abrir la app pinta lo guardado en `localStorage` (`misdirectos.datos.v1`) y lo actualiza por detrás. También deja precargados ayer y mañana.
- **Sin TV:** el usuario pidió quitar la información de canales en todas las competiciones.

## Ligas

Las ligas vienen preconfiguradas en `MY_LEAGUES` y se guardan en `misdirectos.ligas.v4`:

- **Fútbol:** LaLiga, Copa del Rey, Champions, Europa League, Conference, Nations League, Premier, Serie A, Bundesliga, Ligue 1 y Liga Portugal.
- **Baloncesto:** Euroliga, NBA y WNBA.
- **Fútbol americano:** NFL.
- LaLiga Hypermotion también está seleccionada.

El orden de visualización lo marca el array `LEAGUES`: primero el fútbol, luego Euroliga, NFL y NBA, y **LaLiga Hypermotion siempre la última**.

## Fuentes de datos (todas llamadas desde el navegador, sin servidor)

- **ESPN** (fútbol, NBA, WNBA, NFL): `https://site.api.espn.com/apis/site/v2/sports/{sport}/{league}/scoreboard?dates=AAAAMMDD-AAAAMMDD`. Los detalles salen de `/summary?event=ID`. Los eventos se reducen con `slim()` antes de guardarlos.
- **Euroliga:**
  - Fuente principal: `https://api-live.euroleague.net/v2/competitions/E/seasons/E{año}/games` (temporada completa, guardada en `misdirectos.euroliga.v1`).
  - Directo: `https://live.euroleague.net/api/Header?gamecode=N&seasoncode=E{año}`.
  - Respaldo si la principal falla: `https://feeds.incrowdsports.com/provider/euroleague-feeds/v2/competitions/E/seasons/E{año}/games` (por jornada con `?phaseTypeCode=RS&roundNumber=N`).
- No incluye la Liga Endesa (ACB).
- Si ninguna fuente responde, la app entra en "Datos de ejemplo" (`enterDemo()`).

## Notas

- La página lleva `noindex`/`nofollow` y `referrer: no-referrer` para no aparecer en buscadores. No añadas analíticas, cookies ni recursos externos.
- Desde los entornos en la nube de Claude normalmente no se puede llegar a ESPN ni a la Euroliga, porque la red está limitada. Prueba la lógica con datos simulados (por ejemplo jsdom o Playwright con `route.fulfill`) y pide al usuario que la verifique en el móvil.
