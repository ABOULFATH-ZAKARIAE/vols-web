# vols-web

Next.js (App Router) + TypeScript + Tailwind CSS. Tested with Vitest and React Testing Library. Deployed on Vercel.

Work on this repo through the team: `/ship-repo <goal>`. The Tech Lead plans first and waits for approval. Team rules live in `~/.claude/team/standards.md`; stack rules live in `.claude/rules/`.

## Commands

| Task                            | Command                 |
| ------------------------------- | ----------------------- |
| Dev server                      | `npm run dev`           |
| Lint                            | `npm run lint`          |
| Typecheck                       | `npm run typecheck`     |
| Tests                           | `npm test`              |
| Tests + coverage (80% enforced) | `npm run test:coverage` |
| Production build                | `npm run build`         |
| Format check                    | `npm run format:check`  |

The definition of done requires lint, typecheck, `test:coverage` and `build` to all pass.

## Structure

```
src/
├── app/            routes, layouts, Route Handlers (app/api/**/route.ts), Server Actions
├── components/     reusable UI; one component per file, PascalCase
├── lib/            pure logic, data access, validation schemas (most unit tests live here)
└── types/          shared TypeScript types
```

Tests sit next to the code they test: `button.tsx` → `button.test.tsx`.

## Conventions

- **Server Components by default.** Add `'use client'` only for state, effects, event handlers or browser APIs, and keep client components small and at the leaves.
- **Fetch data on the server** (Server Components, Route Handlers, Server Actions). Never call a secret-bearing API from the client.
- **Validate every input** at the boundary with Zod: Server Action arguments, Route Handler bodies and query params, form data, and external API responses.
- **Server Actions and Route Handlers check authentication and authorization** themselves; middleware alone is not enough.
- Use `next/image`, `next/font` and `next/link`; set `metadata` on every page.
- Imports use the `@/` alias (`@/components/button`).
- Accessible by default: semantic HTML, labels on inputs, alt text, visible focus.

## Design

- `DESIGN.md` is the design spec and `design/mockups/` holds the approved mockup. The **designer** agent owns both, plus the design tokens in the `@theme` block of `src/app/globals.css`.
- UI follows the spec and uses the tokens (Tailwind classes generated from them). No hard-coded colors, font sizes, spacing or shadows.
- Every screen has empty, loading, error and success states. WCAG 2.2 AA is required.

## Environment variables

- Secrets (API keys, database URLs) are **server-only**: no prefix, read only in server code.
- `NEXT_PUBLIC_*` variables are shipped to every browser. Never put a secret in one.
- Every variable the app reads is listed in `.env.example` with a placeholder. Real values go in `.env.local` (git-ignored) and in the Vercel dashboard.

## Testing

- Unit-test logic in `src/lib/` and Client Components with Vitest + React Testing Library.
- Vitest cannot render **async Server Components**. Keep their logic in `src/lib/` functions and test those; cover the page itself with an end-to-end test.
- E2E (optional, for key user flows): add Playwright with `npm init playwright@latest` and put tests in `e2e/`.
- Query by role and label (`getByRole`, `getByLabelText`), not by class or test id, unless there is no accessible name.

## Deployment

Vercel: import the GitHub repo, set the environment variables from `.env.example`, deploy. CI in `.github/workflows/ci.yml` runs lint, typecheck, tests with coverage and the build on every push and pull request.
