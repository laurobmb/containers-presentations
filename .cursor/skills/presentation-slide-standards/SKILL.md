---
name: presentation-slide-standards
description: >-
  Defines required conventions for creating or editing slides in this
  project's Red Hat container training decks (Introdução a Containers e
  Podman.html, anatomia_de_containers_kernel_cgroups_e_selinux.html,
  do_kernel_ao_openshift_a_jornada_dos_containers_corporativos.html). Every
  slide must ship with a Presenter Notes entry (detailed command flags,
  terminology, abbreviations, references) and every slide containing a
  terminal/CLI command must include a small JS/CSS typing animation
  illustrating that command. Use when adding, editing, reordering, or
  creating new slides in these presentations.
---

# Presentation Slide Standards

Applies to the 3 slide-deck HTML files in this project. Each deck is a
self-contained `<section class="slide">` carousel driven by `slides`,
`currentSlide`, `totalSlides` and `updateSlideUI()`.

## Rule 1: Every slide needs a Presenter Notes entry

All 3 decks already share this infrastructure — reuse it, don't recreate it:
- A floating button calling `showPresenterNotes()`, placed once inside `<main>`.
- A shared `#notes-modal`, placed once per file.
- A `presenterNotes` array where **index N corresponds to the Nth
  `<section class="slide">` in DOM order** (0-indexed, includes the cover and
  closing slides).

### When adding, removing, or reordering a slide

1. Insert/remove the `<section class="slide">` block at the desired DOM position.
2. Renumber the `<!-- ==================== SLIDE N: ... ==================== -->`
   comments for every slide after the change point.
3. Insert/remove/shift the matching object in `presenterNotes` so its array
   index still matches the new DOM position. Renumber the `// N - ...`
   comments and `Slide N ·` titles in every note after the change point.
4. Verify: `grep -c "title: 'Slide" file.html` must equal the number of
   `<section class="slide">` blocks in that same file.

### Presenter note content requirements

Each note is `{ title, body }`. `body` is an HTML string (paragraphs + an
optional reference list). Every note must give the presenter **more than
what's already on the slide**:

- Extra explanation of the concept, not a restatement of the slide's bullets.
- For any command shown on the slide, walk through its **flags/options** and
  what each one does.
- Spell out **terminology and abbreviations** used on the slide the first
  time they appear (e.g. MAC, DAC, OCI, MCS, UID).
- Practical delivery tips: what to demo live, common pitfalls, questions to
  ask the class.
- A short "Referências" block linking to official docs (docs.podman.io,
  man7.org, kubernetes.io, docs.redhat.com/openshift, OCI spec repos, etc.).

Template:

```javascript
{
    title: 'Slide N · <short label>',
    body: `
        <p>...explicação adicional...</p>
        <p><code class="text-yellow-400">comando --flag</code>: <em>o que a flag faz</em>.</p>
        <div class="mt-3 pt-3 border-t border-rh-border/50">
            <strong class="text-rh-red text-[11px] uppercase tracking-wider block mb-1">Referências</strong>
            <ul class="list-disc list-inside space-y-1 text-[11px]">
                <li><a href="https://..." target="_blank" class="text-blue-400 hover:underline">Label</a></li>
            </ul>
        </div>
    `
}
```

## Rule 2: Every slide with a CLI command needs a small JS/CSS animation

If a new slide shows a terminal command (`podman ...`, `oc ...`,
`kubectl ...`, etc.), don't just print static text — add a small animated
terminal that types the command and its output, matching the existing
pattern used in the "Build de Imagens" slide (Módulo 1,
`Introdução a Containers e Podman.html`, search for `buildAnimGen`) and on
`index.html`.

### Markup

```html
<div class="bg-black border border-rh-border rounded-lg overflow-hidden flex flex-col">
    <div class="flex items-center space-x-2 px-3 py-2 bg-rh-code border-b border-rh-border shrink-0">
        <span class="w-2.5 h-2.5 rounded-full bg-red-500"></span>
        <span class="w-2.5 h-2.5 rounded-full bg-yellow-500"></span>
        <span class="w-2.5 h-2.5 rounded-full bg-green-500"></span>
        <span class="text-[10px] font-mono text-gray-500 pl-1">terminal@rhel-host: ~</span>
    </div>
    <div id="UNIQUE-terminal" class="p-3 font-mono text-[10.5px] leading-relaxed h-[230px] overflow-y-auto"></div>
</div>
```

Give each animated terminal a **unique `id`** (e.g. `build-terminal`,
`rootless-terminal`) — a single deck can have more than one animated slide.

### CSS

Add once per file, only if not already present (check for `.terminal-cursor`
first):

```css
.terminal-cursor {
    display: inline-block;
    width: 0.5em;
    margin-left: 2px;
    background: #EE0000;
    animation: term-blink 0.9s step-end infinite;
}
@keyframes term-blink {
    0%, 50% { opacity: 1; }
    51%, 100% { opacity: 0; }
}
```

### JS engine

Copy the `startBuildAnimation` / `stopBuildAnimation` pair from the "Build de
Imagens" slide (search `buildAnimGen` in `Introdução a Containers e
Podman.html`) and adapt it:

1. Rename `buildAnimGen`, `startXAnimation`/`stopXAnimation`, and the
   terminal element id to match the new slide's topic.
2. Write a `script` array alternating typed input lines
   (`{ prompt: '$'|'#', text, type: true }`) and instant output lines
   (`{ text, color }`), ending with an idle blinking-cursor step
   (`{ prompt: '$', text: '', type: true, idleCursor: true }`) that restarts
   the loop after ~2.5s.
3. Hook start/stop into `updateSlideUI()`: add
   `if (currentSlide === N) { startXAnimation(); } else { stopXAnimation(); }`
   where `N` is that slide's DOM index — update this if slides are reordered.

Keep typing speed ~22-40ms/char with slight randomness; pause ~110-140ms
between printed output lines.

## Verification checklist after any slide change

- [ ] `presenterNotes.length` matches the deck's `totalSlides`
      (`grep -c "title: 'Slide" file.html`).
- [ ] Every `<!-- SLIDE N -->` comment and `// N -` note comment is
      sequential and correct.
- [ ] Every command-containing slide has its own animated terminal with a
      unique element id, hooked into `updateSlideUI()`.
- [ ] HTML validates
      (`python3 -c "from html.parser import HTMLParser; HTMLParser().feed(open(path, encoding='utf-8').read())"`)
      and `ReadLints` reports no errors.
- [ ] Take a Playwright screenshot of the new/changed slide (and its
      Presenter Notes modal) to confirm visually.
