# son-of-anton-web

The marketing site for [Son of Anton](https://github.com/cesarnml/son-of-anton), a delivery orchestrator for solo developers and small teams.

**Live:** [sonofanton.vercel.app](https://sonofanton.vercel.app)

## Why a separate repo

Son of Anton ships to consumers as a **git subtree** — every commit in the main repo lands in consuming repos' history on `git subtree pull`. Keeping the website here means consumers never pull website churn. (See [son-of-anton#82](https://github.com/cesarnml/son-of-anton/issues/82).)

## Stack

- [SvelteKit](https://svelte.dev/docs/kit) (Svelte 5) — fully prerendered; the page is static HTML and all content works without JavaScript
- [Tailwind CSS 4](https://tailwindcss.com)
- pnpm
- Deployed on [Vercel](https://vercel.com) — pushes to `main` auto-deploy production; PRs get preview URLs

## Design

"Editorial Brutalist" — paper white canvas, carbon black 2px rules, cobalt and amber accents, zero border radius. Newsreader for headlines, Inter for body, JetBrains Mono for labels and terminal blocks.

All copy is grounded in the actual Son of Anton codebase: the three-gates model, runtime policy flags, boundary modes, and the [Codogotchi](https://codogotchi.app) gate events come from the README and `tools/delivery/codogotchi-gate.ts`, not marketing imagination.

## Development

```sh
pnpm install
pnpm dev        # dev server
pnpm check      # typecheck (svelte-check)
pnpm build      # production build
pnpm preview    # preview the production build
```

The entire site is one route: [`src/routes/+page.svelte`](src/routes/+page.svelte). Theme tokens live in [`src/routes/layout.css`](src/routes/layout.css).
