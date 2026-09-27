# Realtime socket layer

## Overview

The socket.io server lives on the Next.js Pages API router and broadcasts message creates, edits and deletes to a room per chat. The client hooks listen on those events and patch the TanStack Query cache, so a sent message appears without a refetch.

## Key files

| File | Owns |
|---|---|
| `io.ts` | Creates the socket.io server on `res.socket.server.io`, path `/api/socket/io` |
| `messages/index.ts` | Creates a channel message, emits the create event |
| `messages/[messageId].ts` | Edits and deletes a channel message, emits the update event |
| `direct-messages/index.ts` | Creates a direct message, emits the create event |
| `direct-messages/[directMessageId].ts` | Edits and deletes a direct message, emits the update event |
| `hooks/use-chat-socket.ts` | Client listeners that patch the query cache |
| `components/providers/socket-provider.tsx` | Opens the client socket and exposes `useSocket` |

## Conventions

- Keep these handlers in the Pages Router. socket.io attaches to the Node HTTP response socket, which an App Router route handler cannot reach.
- `io.ts` sets `bodyParser: false` and creates the server once behind `if (!res.socket.server.io)`. Keep both.
- Authorize with `currentProfilePages(req)`, not `currentProfile()`. The latter is for the App Router and returns nothing on a Pages API request.
- Event key contract, which both sides must match:
  - create: `chat:{chatId}:messages`
  - update and delete: `chat:{chatId}:messages:update`
  - `chatId` is a channelId for channels and a conversationId for direct messages.
- The handler verifies membership, writes to the database, then emits. The socket never writes data itself.

## Gotchas

- The client connects to `process.env.NEXT_PUBLIC_SITE_URL`, which is not in `.env` yet. Add it (for example `http://localhost:3000`) or the socket never connects.
- `use-chat-socket.ts` types the cache data as `any`, so keep the `MessageWithMemberWithProfile` shape in step with the `include` used in the handlers.

_Drafted by /audit from the repo, worth a quick human pass. Edit freely: once a line stops matching this draft, later runs treat it as curated and will flag rather than overwrite it._
