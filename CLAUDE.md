# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

An HTML slide deck: `presentation.html`. It's a talk titled
*"Who Wants to Be a Programmer — No Bullshit Edition"* by Andriy Kvasnytsya, about AI's
impact on the programming profession and on jobs in general. **All slide content is in
Ukrainian.** There is no build system, no package manager, and no tests. External resources:
Google Fonts (loaded via CDN at runtime) and one local image asset, `meme-it-guy-3am.png`,
referenced relatively by `slide-meme` — keep it alongside the HTML when moving the deck.

## Running / previewing

Open `presentation.html` directly in a browser (double-click, or `start presentation.html`).
Navigate slides with the on-screen PREV/NEXT buttons, the clickable dots, or the keyboard:
Right/Down/Space advance, Left/Up go back.

## Architecture

Everything lives in `presentation.html`: a `<style>` block, the slide markup, and a
`<script>` block at the bottom.

### Slide system (read before adding/removing/reordering slides)

The `slideIds` array in the `<script>` is the **single source of truth** for which slides
exist and in what order. The script reads it to: generate the navigation dots, compute the
`NN / NN` counter, and drive `goTo()` / `changeSlide()`. Each entry must match the `id` of a
`<div class="slide">` in the markup.

To add, remove, or reorder a slide you must change **two** places in sync: the `slideIds`
array and the corresponding `.slide` div. Changing only one breaks navigation.

**Gotcha:** neither the numeric `id`s nor the source (DOM) order reflect display order. The
deck has 16 slides, and display order is whatever the `slideIds` array says — nothing else.
Quirks to expect: `slide-llm` and `slide-meme` are non-numeric ids; `slide-10`/`slide-11` do
not exist (the old singularity slide was merged into `slide-9`); and the profession block
(`slide-3/4/5/5-skills`) lives in the source right after `slide-2` but is *displayed* later,
after the revolutions block (`slide-6/7/8`). Always trust the `slideIds` array order and the
HTML comments, not the numeric ids or source position. Exactly one slide carries the `active`
class as the initial slide. The hardcoded `01 / NN` text in `#counter` is just the pre-JS
initial render — keep its total in sync with the slide count when adding/removing slides.

### Visual design system (CSS)

The terminal / green-CRT aesthetic (scanlines via `body::before`, vignette via `body::after`,
glitch animation on the title) is driven by CSS custom properties in `:root` (`--bg`, `--green`,
`--amber`, `--red`, `--white`, `--grey`, …). Recolor the whole deck by editing those variables
rather than individual rules.

Build slide bodies from the existing reusable component classes instead of inventing new ones:
- `.prompt` — the `$ command --flag` line decorating most slides
- `.slide-title` — section heading
- `.term-list` / `li` — terminal-bullet (`▶`) lists
- `.two-col` + `.col-box` with variants `.danger-box` / `.safe-box` / `.amber-box`
- `.principle` — amber callout box; `.conclusion-text` — final-slide style
- `.big-stat`, `.timeline` (`.tl-item` / `.tl-dot` / `.tl-year`)

Inline semantic color spans set the meaning/tone of words: `.accent` (amber), `.safe` (green),
`.warn` (amber), `.danger` (red), `.dim` (grey). Reuse these rather than hardcoding `color:`
inline (though some one-off inline styles do exist in the markup).
