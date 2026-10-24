#### Adding a Font From Google

Self-host the `.ttf` — if you already have the file there is no reason to hit Google's CDN (it's a third-party request and breaks offline).

1. Drop the file under `src/assets/fonts/` so Vite fingerprints and bundles it.

```
src/assets/fonts/Slabo27px-Regular.ttf
```

2. Define it with `@font-face` in a stylesheet (the first CSS file this project had was `src/index.css`).

```css
@font-face {
    font-family: 'Slabo 27px';
    src: url('./assets/fonts/Slabo27px-Regular.ttf') format('truetype');
    font-weight: 400;
    font-style: normal;
    font-display: swap;
}

:root {
    font-family: 'Slabo 27px', serif;
}

/* form controls do NOT inherit font-family by default */
input,
button,
textarea,
select {
    font: inherit;
}
```

3. Import the stylesheet once in `src/main.jsx`.

```jsx
import './index.css';
```

4. `:root` makes it global, and the family name must match the `@font-face` exactly.

Gotchas that actually bit me

1. **THE BIG ONE** — inside `@font-face`, `font-family` is a *descriptor* that **names** the font, so it must be a **single name with no comma list**. Writing `font-family: 'Slabo 27px', serif;` makes the whole `@font-face` rule invalid and the browser **silently drops it** — the font never registers and `:root` falls back to `serif`. The comma-separated fallback stack belongs **only on the usage side** (`:root`), never in the `@font-face`. Correct: `font-family: 'Slabo 27px';`
2. **Form controls don't inherit the font.** `:root { font-family }` reaches normal elements (`h1`, `p`), but `input` / `button` / `textarea` / `select` keep the OS UI font unless you explicitly add `font: inherit`.
3. **One weight means faux bold.** With only `-Regular.ttf`, anything bold by default (`h1`, `button`) is *synthetically* bolded. Proper fix: add `-Bold.ttf` and a second `@font-face` block with `font-weight: 700`.
4. `font-display: swap` avoids a flash of invisible text. `.ttf` is heavy — convert to WOFF2 for production and list both in `src:` with `format()` guards.