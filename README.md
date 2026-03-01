# Love Forever — How This HTML Is Made

#Made by Re

This document explains how `index.html` is built, the maths used for the animation, and what (if any) components you need.

---

## 1. How the HTML File Is Made

### Overview

The page is a **single, self-contained HTML file**. Everything runs in one file:

- **HTML** — One container `<div id="ui">`; all visible content is created by JavaScript.
- **CSS** — Embedded in a `<style>` block (layout, colors, animations).
- **JavaScript** — Embedded in a `<script>` block; it builds 100 “love” elements and adds them to the page.

There are **no external libraries**, **no images**, and **no custom fonts**. You only need a modern browser.

### Structure

```
index.html
├── <head>
│   ├── meta (charset, viewport)
│   └── <style> … </style>   ← all CSS
├── <body>
│   ├── <div id="ui"></div>  ← empty container
│   └── <script> … </script> ← all JS
└── </body>
```

### How the “I love you” Elements Are Created

1. The script reads `document.getElementById('ui')`.
2. A loop runs **100 times** (`N = 100`).
3. Each iteration creates:
   - A wrapper `div` with class `love` and a CSS variable `--i` (1 to 100).
   - A horizontal mover `div` with class `love_horizontal`.
   - A vertical mover `div` with class `love_vertical`.
   - A text `div` with class `love_word` and text `"I love you"`.
4. They are nested: `love_word` inside `love_vertical` inside `love_horizontal` inside `love`, then each `love` is appended to `#ui`.

So the DOM is built entirely in JavaScript; the HTML only has the one empty `#ui` div.

### Styling Highlights

- **Background**: Black (`#000`), full viewport height, no scroll.
- **Font**: System stack `system-ui, -apple-system, "Segoe UI", Roboto, Arial, sans-serif` — no download needed.
- **Colors**: Pink `#ea80b0` for text, white for the last element, with `text-shadow` for glow.
- **Layout**: Each `.love` is centered with `position: absolute`, `top: 50%`, `left: 50%`, and `margin: -225px 0 0 -225px` so the animation area is centered (see maths below).

---

## 2. The Maths Used in This Code

### 2.1 Centering the Animation Area

Each `.love` block is positioned so its “logical” box is **450px × 450px** and centered in the viewport:

- `top: 50%; left: 50%` — place the top-left of the block at the center of the screen.
- `margin: -225px 0 0 -225px` — pull it back by half of 450px in both directions.

So the **center of the 450×450 area** is the **center of the viewport**. All motion is defined relative to this box.

### 2.2 Staggered Animation Delay

Each of the 100 elements gets a different delay so they don’t move in sync:

- **Formula**: `animation-delay: calc(var(--i) * -300ms)`
- **Meaning**: For element index `i` (1 to 100), delay is `-300 × i` milliseconds.

So delays are **-300ms, -600ms, -900ms, … -30000ms**. The negative value means the animation appears to have already been running; combined with different `--i`, the 100 “I love you” lines are **staggered** and look like a continuous wave.

### 2.3 Horizontal Motion

- **Keyframes**: `translateX(0px)` → `translateX(450px)`.
- **Duration**: 10 seconds, `infinite`, `alternate` (so it goes 0 → 450 → 0 → 450 …).
- **Effect**: Each line moves 450px right, then 450px left, repeatedly. With the center at 225px, the visible range is 0 to 450px from the block’s left edge.

### 2.4 Vertical Motion (Bounce Path)

The vertical animation uses a **keyframe path** that looks like a bounce/sine-like motion rather than a single formula in the code. Keyframe percentages and `translateY` values (in px) are:

| Keyframe % | translateY (px) | Role in path        |
|------------|------------------|----------------------|
| 0, 50, 100 | 180              | “Rest” height        |
| 10, 40     | 45               | Approach / leave     |
| 15, 20, 30, 35 | 4.5          | Near top             |
| 18, 32     | 0                | Top of bounce        |
| 22         | ~34.6            | Down from top        |
| 24, 26     | ~64.3, 112.5     | Going down           |
| 25         | 112.5            | Lower peak           |
| 71, 79     | ~428.6           | Near bottom          |
| 72.5, 77.5 | ~441.2           | Bottom approach      |
| 75         | 450              | Bottom of path       |

So the **maths** here are:

- **Range**: Vertical motion from **0px** (top of the 450px box) to **450px** (bottom).
- **Shape**: The exact numbers (e.g. 34.61538, 64.28571, 428.57143, 441.17647) are chosen to make the motion smooth and bouncy (likely from a curve or hand-tuned). There is **no separate math library**; the “math” is just these keyframe values.
- **Duration**: 20 seconds, `infinite`, `linear` (constant speed along the keyframe timeline).

Together, horizontal and vertical keyframes make each “I love you” move in a **rectangular/bouncy area** (0–450px in both X and Y) around the center of the screen.

### 2.5 Text Transform

- `transform: translateY(-100%) rotateZ(-30deg)` — moves the text up by its own height and rotates it -30°. So the text sits and tilts above its “anchor” point in the animation.

---

## 3. Components Required & How to “Download” Them

**You do not need to download any extra components.** The project uses only:

- **HTML5**
- **CSS3** (animations, transforms, `calc()`, custom properties)
- **Vanilla JavaScript** (DOM: `getElementById`, `createElement`, `appendChild`, `setProperty`)

### What’s Already in the File

| Component        | Source                    | Download? |
|-----------------|---------------------------|-----------|
| HTML structure  | In `index.html`           | No        |
| CSS             | In `<style>` in `index.html` | No     |
| JavaScript      | In `<script>` in `index.html` | No    |
| Fonts           | System fonts (see above)  | No        |
| Images          | None                      | —         |
| Libraries (e.g. jQuery, React) | None              | No        |

### What You Need on Your Machine

- A **modern browser** (Chrome, Firefox, Edge, Safari) that supports:
  - CSS `@keyframes`
  - CSS `transform` and `animation`
  - CSS `calc()` and custom properties (`var(--i)`)

No Node.js, no npm, no build step. Just open the file or serve the folder:

```bash
# From the project folder (e.g. index)
start index.html
# Or run a local server, e.g.:
python -m http.server 8000
# Then open http://localhost:8000
```

### If You Want to Learn More About the Technologies

- **HTML**: [MDN HTML introduction](https://developer.mozilla.org/en-US/docs/Learn/HTML/Introduction_to_HTML)
- **CSS animations**: [MDN CSS animations](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Animations)
- **CSS transform**: [MDN transform](https://developer.mozilla.org/en-US/docs/Web/CSS/transform)
- **JavaScript DOM**: [MDN DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model)

No “download” of components is required; the only “component” is this single `index.html` file and a browser that supports the features above.
