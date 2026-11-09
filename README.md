# Country Mile

A calm, endless driving game that runs in the browser. You drive a small car down a winding countryside road that is generated as you go and never ends. There are rolling hills, orchards, pine woods and farmland, and the sky slowly turns from afternoon through golden hour and sunset into a starlit night, then back to dawn.

Distance traveled is your score. Your speed creeps up the longer you drive. Fallen branches and rocks sometimes lie in the road. Hitting one only slows you down a little, and the game never ends on its own. You choose when to pull over and see a summary of your journey.

## Controls

| Action | Desktop | Mobile |
| --- | --- | --- |
| Steer | ← / → or A / D | Drag, or swipe to change lanes |
| Boost | Space, ↑ or W | Swipe up or tap **Boost** |
| Ease off | ↓ or S | — |
| Pull over / resume | Esc or P | Pause button |
| Sound / music | M / N | Corner buttons |

## Technology

- One self-contained `index.html` made of HTML, CSS and vanilla JavaScript. It uses no libraries, frameworks, images or external fonts.
- The road is a segment-projected pseudo-3D view drawn with Canvas 2D.
- All scenery art (trees, barns, hay bales, clouds, obstacles) is drawn in code and cached to offscreen canvases.
- Audio is synthesized live with the Web Audio API: a generative ambient soundtrack, engine hum, tyre noise and soft effects.
- Sound and music settings and your longest journey are saved in `localStorage`.

## Running locally

There's no build step. Open `index.html` in any modern browser, or serve the folder:

```bash
npx serve .
# or
netlify dev --port 8889
```

It deploys to Netlify as a static site (`netlify.toml` publishes the repository root).

## Enjoy here 
https://endless-peace.netlify.app/
