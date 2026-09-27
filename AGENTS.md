# discord-copy

A Discord style chat app: servers, channels, direct messages, and audio or video rooms.

## Stack

- **Language / Runtime**: TypeScript 5 (strict), Node.js
- **Framework**: Next.js 13.5, React 18. App Router for pages, Pages Router only for the socket.io handlers
- **Key dependencies**: Prisma 5 (MySQL), Clerk (auth), TanStack Query 5, socket.io, LiveKit (audio and video), UploadThing (uploads), Tailwind CSS with shadcn/ui
- **Package manager**: npm

## Build approach

<TBD, set by /scope>

## Commands

```bash
npm install          # install dependencies
npm run dev          # dev server
npm run build        # production build
npm run lint         # lint
npx prisma db push   # push schema (MySQL, no migrations folder)
npx prisma generate  # generate the client
npx prisma studio    # browse data
```

No test runner is configured yet.

## Specs

Stored in `docs/specs/`. Format: `docs/specs/NNNN-title.md`.

## Rules

- New UI lives under `app/(main)/routes/`. The Pages Router exists only for the socket.io handlers in `pages/api/socket`.
- Every route handler and server helper authorizes first: `currentProfile()` in the App Router, `currentProfilePages(req)` in the Pages API. Then scope the query by server or conversation membership before any read or write.
- Writes go through API routes (`app/api/...`). socket.io only broadcasts the result, so never emit a write from the client.
- Forms use `react-hook-form` with `zod` and the shadcn `Form` parts, post with `axios`, then call `form.reset()` and `router.refresh()`.
- UI parts come from shadcn/ui in `components/ui`, composed with `cn()` from `lib/utils.ts`. Import through the `@/` alias, which maps to the repo root.
- Open modals through the `useModal` store, never local state. Toggle video rooms with a `?video=true` search param on the channel route.
- Use the `db` singleton from `lib/db.ts`. Models use uuid ids and `relationMode = "prisma"`, so there are no database level foreign keys.

## Agent skills; the Pages Router exists only for the socket.io handlers in `pages/api/socket`.
- Authorize first in every handler (`currentProfile()` in the App Router, `currentProfilePages(req)` in the Pages API), then scope
- [clerk-nextjs-patterns](.agents/skills/clerk-nextjs-patterns/): `clerk/skills`, Clerk auth patterns for Next.js
- [prisma-client-api](.agents/skills/prisma-client-api/): `prisma/skills`, Prisma Client query API
- [prisma-cli](.agents/skills/prisma-cli/): `prisma/skills`, Prisma CLI, migrate and studio
- [shadcn](.agents/skills/shadcn/): `shadcn-ui/ui`, shadcn/ui component conventions
- [vercel-react-best-practices](.agents/skills/vercel-react-best-practices/): `vercel-labs/agent-skills`, React and Next.js patterns

MCP servers: shadcn (recommended)

## Context files

- [components/chat/AGENTS.md](components/chat/AGENTS.md): chat feed, send loop and scroll behavior
- [components/modals/AGENTS.md](components/modals/AGENTS.md): modal registry and how to add a modal
- [pages/api/socket/AGENTS.md](pages/api/socket/AGENTS.md): socket.io handlers and the realtime event contract

_Drafted by /audit from the repo, worth a quick human pass. Edit freely: once a line stops matching this draft, later runs treat it as curated and will flag rather than overwrite it._
