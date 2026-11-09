# Paper Boat Voyage

A scroll-driven 3D story. As you scroll, a paper boat travels 38 km from a mountain spring, past a mill village and under a bridge, through the night to a lighthouse at dawn.

**Live demo:** https://akbarov-labs.github.io/paper-boat-voyage/

It is a single `index.html` with no build step. [Three.js r128](https://threejs.org/) loads from cdnjs only after the page is idle.

## How it works

1. **Pin the stage (CSS).** The story section is 8 screens tall (`800svh`). Inside it, a full-screen stage uses `position: sticky; top: 0`, so the canvas stays on screen while you scroll through the extra height.
2. **Turn scroll into eased time (JS).** Scroll progress through the section (0 to 1) is multiplied by 40 to get a target time. The displayed time follows that target smoothly:
   ```js
   time += (target - time) * (1 - Math.exp(-dt * 5));
   ```
3. **Compute the whole frame from `t`.** `render(t)` reads the boat, camera, sky and lights from tables. Nothing stores progress or runs on a clock, so scrolling back plays the story in reverse with no extra code.
4. **Read the same `t` in the HTML.** The clock, distance, caption and chapter bars are ordinary HTML over the canvas, so they always match the scene.

## Make your own story

Most changes are edits to four tables at the top of the script:

| Table | What it controls |
| --- | --- |
| `CHAPTERS` | Chapter start times, titles and caption text |
| `MOVES` | When the boat moves and how far along the river: `[startTime, endTime, startPos, endPos]` |
| `CAMERA` | Camera keyframes: `[time, distance, elevation, azimuth]` |
| `SKY` | Sky colours, sun and fill light, and the night amount that switches on windows and lamps |

## Performance and fallbacks

- The renderer only draws when `t` changes, the section is on screen and the tab is visible.
- Screens narrower than 900px and devices with 4 or fewer CPU cores get a lighter terrain, fewer trees and a lower pixel ratio.
- If the visitor prefers reduced motion, or WebGL fails, the page shows the chapters as a plain list instead. With reduced motion, a button still lets the visitor load the 3D scene.

## Run locally

Open `index.html` in a browser, or serve the folder:

```bash
python3 -m http.server 8000
```

## Credits

The technique was studied on [choparexpress.uz](https://choparexpress.uz/). The scene, story and code in this repository are original.
