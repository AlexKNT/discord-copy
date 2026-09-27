# Chat surfaces

## Overview

Renders a chat, either a channel or a direct message: the message feed with infinite scroll, the header, and the send input. Both surfaces reuse the same components through props rather than forking them.

## Key files

| File | Owns |
|---|---|
| `chat-messages.tsx` | The feed. Builds the query and event keys and wires the three hooks |
| `chat-item.tsx` | One message, including edit and delete actions |
| `chat-input.tsx` | Send form, posts to the socket API route |
| `chat-header.tsx` | Header with the mobile toggle, video button and search |
| `chat-video-button.tsx` | Toggles `?video=true` to open the LiveKit room |
| `chat-welcome.tsx` | Empty state for a chat with no messages |
| `hooks/use-chat-query.ts` | Infinite query with cursor pagination, plus a poll fallback |
| `hooks/use-chat-socket.ts` | Socket listeners that patch the cache |
| `hooks/use-chat-scroll.ts` | Loads older pages at the top, scrolls to the bottom |

## Conventions

- Reuse through the prop contract, not by copying files: `apiUrl`, `paramKey` (`"channelId"` or `"conversationId"`), `paramValue`, `type`, and for the feed `socketUrl` and `socketQuery`.
- Query key is `chat:${chatId}`. Event keys are `chat:${chatId}:messages` and `chat:${chatId}:messages:update`. Keep them identical to the server side, see `pages/api/socket/AGENTS.md`.
- Sending a message posts to `apiUrl` (a `/api/socket/...` route) with `axios`. Do not emit from the client.
- Messages load in batches of 10 (`MESSAGES_BATCH` in `app/api/messages/route.ts`), paginated by message id cursor.
- The feed is a client component. Page data such as the member and server is fetched in the server component and passed down.

## Gotchas

- `use-chat-query.ts` polls every 1000ms while `isConnected` is false. That is the fallback used when the socket is unavailable, so expect slow updates then.
- `use-chat-scroll.ts` only scrolls to the bottom when the reader is already within 100px of it, so it does not yank the view while someone reads older messages.

_Drafted by /audit from the repo, worth a quick human pass. Edit freely: once a line stops matching this draft, later runs treat it as curated and will flag rather than overwrite it._
