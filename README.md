# StarLuge

Juego espacial 2D en un único archivo HTML (canvas, sin dependencias ni compilación). Exploras un universo generado por semilla, minas, comerciantes, combates piratas, mejoras tu nave, formas una flota de escoltas y puedes jugar **hasta 4 jugadores** en la misma partida.

> Todo el juego está en `index.html`. No hay carpetas: el repositorio se compone solo de los archivos de la raíz.

## Archivos del repositorio

| Archivo | Para qué sirve |
|---|---|
| `index.html` | El juego completo (HTML + CSS + JavaScript). |
| `README.md` | Este documento. |
| `LICENSE` | Licencia MIT. |
| `.gitignore` | Archivos que Git debe ignorar. |
| `.nojekyll` | Evita que GitHub Pages procese el sitio con Jekyll. |

## Jugar en solitario

Abre `index.html` en un navegador moderno (Chrome, Edge, Firefox, Safari). No necesita servidor ni instalación. La partida se guarda automáticamente en el navegador (`localStorage`).

### Controles (ordenador)

| Acción | Control |
|---|---|
| Mover la nave | Clic izquierdo (mantén para seguir el cursor) |
| Disparar a mano | Mantén el clic derecho en el espacio vacío |
| Minar una roca / orbitar y atacar a un enemigo | Clic derecho sobre ella / él |
| Menú de objeto | Mayús + clic izquierdo |
| Acelerador | `W` / `S` (o ▲ / ▼) · sobrecarga hasta el 150 % |
| Frenar | `X` o `Espacio` |
| Mapa estelar | `M` |
| Menú de nave | `N` |
| Inventario | `I` |
| Escoltas (flota) | `F` |
| Fijar objetivo | `T` |
| Zoom | Rueda del ratón · pellizco en pantallas táctiles |

En el modo táctil hay botones en pantalla para las acciones principales.

**Modo creativo:** añade `#cre` al final de la dirección (`index.html#cre`). Da energía infinita e invencibilidad. En el menú de pausa puedes activar o desactivar la invencibilidad.

## Multijugador

El multijugador usa **Supabase Realtime** (canales de difusión y presencia). No hace falta ninguna tabla ni script SQL.

### Cómo funciona

- Una persona crea la partida y es la **anfitriona**. Recibe un código de sala de 6 caracteres.
- Los demás (hasta 3 más) entran con ese código desde el menú **Multijugador**.
- **El anfitrión manda sobre el mundo**: calcula los enemigos, los asteroides, los cometas, los restos de naves, el botín y los eventos, y todos los jugadores ven lo mismo.
- Mientras el anfitrión no esté en la sala, cada jugador sigue en su copia local.

### Qué se comparte y qué es personal

| Compartido por todos | Personal de cada jugador |
|---|---|
| Enemigos, jefes y escoltas | Inventario y minerales |
| Asteroides (si uno mina una roca, desaparece para todos) | Créditos y chatarra |
| Cometas, restos de naves, botín | Mejoras y nave elegida |
| Eventos (tormentas, meteoritos, mercader, flotas) | Flota de escoltas |
| Naves descubiertas en el Codex | Misiones y progreso |
| Anomalías descubiertas | Tu ubicación en el mapa (si no la compartes) |

Las recompensas de matar a un enemigo van al jugador que le ha dado el golpe final.

### PVP o PVE

El anfitrión elige el modo en **Pausa → Multijugador → Modo de juego**. En **PVE** (por defecto) los jugadores son aliados y sus disparos no se hacen daño. En **PVP** los disparos de otros jugadores sí te dañan. El modo se ve en la parte superior de la pantalla («· PVP») y todos reciben el aviso al cambiarlo.

### Ayudarse entre jugadores

Si la nave de un amigo queda inutilizada la verás en gris con «INUTILIZADA»:

- **Clic derecho sobre ella:** si vas con el **Técnico** te acercas y la reparas (unos 6 s, quédate cerca); con cualquier otra nave la **enganchas y la remolcas**. Llévala a una base: al atracar vuelve a estar operativa.
- **Mayús + clic izquierdo:** menú con *Reparar*, *Remolcar* y *Cancelar ayuda*.
- Si te alejas demasiado, el remolque se suelta.

### Dificultad según jugadores

El anfitrión calcula los enemigos. Con 2 jugadores aparecen el doble de piratas (topes y frecuencia) y con más vida y un 50 % más de daño; con 3 y 4 se escala igual (×3, ×4 en cantidad y vida; +100 % y +150 % de daño).

### Dónde se guardan los datos

**Supabase no guarda nada**: solo hace de «cable» en directo (Realtime) mientras jugáis. No hay tablas ni base de datos.

- El **anfitrión** es el dueño del mundo: en su dispositivo se guarda su progreso y la ficha de cada jugador que ha entrado (su nombre y todo lo que ha hecho).
- Los invitados no tienen el mundo: solo anotan en su lista la sala (código y nombre del anfitrión). Su progreso lo guarda el anfitrión y se les devuelve al entrar.
- La sala **no está abierta 24 h**: solo funciona mientras el anfitrión tiene el juego abierto con la sala abierta. Cuando la vuelve a abrir, los invitados entran con el mismo código (o desde su lista) y recuperan su progreso.
- Tu partida en **solitario** es independiente y nunca se toca.
- Si el anfitrión **cierra la sala** (o se desconecta más de ~30 s (tiempo de tregua para cortes de internet; si vuelve antes, no pasa nada)), los invitados son expulsados a su menú.

### Ubicación en el mapa

Los demás jugadores **no aparecen en el mapa ni en el minimapa**, a no ser que pulses **«Mi ubicación: OCULTA/VISIBLE»** en el mapa. Solo se ve a quien lo ha activado. En el espacio, si estás cerca de otro jugador, lo ves igualmente.

### El tutorial

En multijugador no hay modo historia: no se ven el panel de misiones, los capítulos ni las recompensas de misión (las misiones siguen avanzando en segundo plano para desbloquear zonas y dificultad). Los tutoriales de uso sí aparecen. El tutorial de escoltas no se muestra.

### Configurar Supabase (una sola vez)

1. Crea un proyecto gratuito en <https://supabase.com>.
2. En **Project Settings → API** copia la **Project URL** y la clave **anon public**.
3. Abre `index.html`, busca `SUPA_URL` y `SUPA_KEY` y pega ambos valores (la URL base, sin `/rest/v1/`; vale la clave pública `anon` o la «publishable»):

   ```js
   var SUPA_URL='https://TU-PROYECTO.supabase.co',SUPA_KEY='TU_CLAVE_ANON';
   ```

4. Comprueba que **Realtime** está activado (lo está por defecto).

> La clave `anon` está pensada para ir en el navegador, es pública por diseño y puede subirse al repositorio. **Nunca** pongas la clave `service_role`.

Límites del plan gratuito de Supabase: hay un máximo de mensajes por segundo y de conexiones simultáneas. Con 4 jugadores debería quedar por debajo de ese límite.

## Publicar en GitHub Pages

1. Crea un repositorio en GitHub (por ejemplo `StarLuge`).
2. Sube los cinco archivos a la raíz (arrastrarlos en *Add file → Upload files* vale).
3. Ve a **Settings → Pages**, elige *Deploy from a branch*, rama `main`, carpeta `/ (root)` y guarda.
4. En un par de minutos el juego estará en `https://TU-USUARIO.github.io/StarLuge/`.

Con Git en la terminal:

```bash
git init
git add index.html README.md LICENSE .gitignore .nojekyll
git commit -m "StarLuge"
git branch -M main
git remote add origin https://github.com/TU-USUARIO/StarLuge.git
git push -u origin main
```

## Ajustes

- **Tamaño de la interfaz:** empieza en el 135 %. Se cambia en Ajustes.
- **Brillo del universo:** empieza en el 100 %.
- **Volumen.**

## Limitaciones conocidas del multijugador

- La pausa **no detiene el mundo** en multijugador: mientras tienes el menú abierto tu nave no recibe daño.
- Los cometas y los eventos aparecen cerca de un jugador elegido al azar y solo ese jugador recibe el aviso. La flota desconocida también: si el objetivo es el anfitrión en solitario pide peaje como siempre; si es otro jugador, o si hay jugadores a menos de unos 1800 u entre sí, llega como flota hostil con más naves (+3 por cada jugador extra) y los rodea a todos.
- La guardia de Alfa y Omega persigue solo a quien les ataca.
- Las piezas del Cementerio de naves y las misiones son personales.

## Licencia

MIT. Consulta `LICENSE`.
