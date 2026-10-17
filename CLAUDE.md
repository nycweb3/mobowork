# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A one-page personal brand site for 安迪老師 (Mobowork), a 技術命理顧問 offering tarot, 八字/紫微 and 易經 + AI consulting. All copy is Traditional Chinese (`lang="zh-Hant"`). There is no build step, framework, package manager or test suite: everything lives in `index.html` (inline CSS and JS). `logo.svg` is a standalone copy of the brand mark.

## Running it

Open `index.html` directly, or serve the folder so `fetch` and the dialog behave as in production:

```bash
python3 -m http.server 8765
```

Fonts (Noto Serif TC / Noto Sans TC) load from Google Fonts, so previews need network access.

## Structure of index.html

- `<style>`: design tokens on `:root` (palette `--porcelain`, `--mist`, `--celadon`, `--ink`, `--brass`; type scale `--fs-*`; `--gutter`, `--max`), then styles per section, then responsive overrides at 960px and 720px, then `prefers-reduced-motion`. Add new section styles before the `@media` blocks so the breakpoint overrides still win.
- Sections in order: hero, `#about`, `#services`, `#process`, `#trust`, `#contact`, then the booking `<dialog id="bookDialog">` outside `<main>`.
- `<script>` is one IIFE with three parts marked `// ----- … -----`:
  - **Hexagram**: the hero's interactive 易經 hexagram. Trigrams are stored bottom-to-top (1 = 陽, 0 = 陰); `KW[upper][lower]` maps to the King Wen number and `NAMES[n - 1]` to the name. The six `.bar` rows are built once and rendered with `flex-direction: column-reverse`, so index 0 is 初爻 at the bottom.
  - **Nav**: sticky header border on scroll, mobile menu toggle.
  - **Booking dialog**: any element with `data-book` opens it; `data-close` closes it. Validation rules live in `RULES` (keyed by input id, with error text in `#<id>-err`). On submit the form POSTs JSON to `FORM_ENDPOINT` (FormSubmit AJAX), which forwards the booking by email. Keys starting with `_` are FormSubmit options (`_subject`, `_template`, `_honey` honeypot); the `email` key becomes the reply-to.

## Things to keep in mind

- Bookings go through the form, which forwards to nycweb9@protonmail.com via `FORM_ENDPOINT` (until it is replaced with the FormSubmit alias issued after activation). The only visible email is the deliberately low-key nycweb3@protonmail.com in the footer; keep it out of the hero and contact sections.
- FormSubmit only forwards after the owner clicks the activation link sent on the first real submission. Test the form flow by stubbing `window.fetch` in the browser rather than sending real submissions.
- The logo is drawn on a 31×31 grid (stroke 3, pitch 7, crossing gap 1) as one `<path>`; it is inlined in the nav (rendered at 31px so each unit is one pixel), as the data-URI favicon in `<head>`, and in `logo.svg`. Change all three together.
- Numbered markers are used only in `#process`, which is a real sequence; the services are deliberately unnumbered.
