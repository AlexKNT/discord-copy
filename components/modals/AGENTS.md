# Modals

## Overview

Every app modal is mounted once by the provider and opened through a zustand store, so no component renders a dialog from its own local state.

## Key files

| File | Owns |
|---|---|
| `hooks/use-modal-store.ts` | The `ModalType` union, `ModalData` and the `onOpen` / `onClose` actions |
| `components/providers/modal-provider.tsx` | Mounts every modal after hydration |
| `*-modal.tsx` | One dialog each, reading `isOpen` and `type` from the store |

## Conventions

- To add a modal: add its name to the `ModalType` union, add any needed fields to `ModalData`, create `components/modals/<name>-modal.tsx`, then render it in `modal-provider.tsx`. Open it with `onOpen("<name>", data)`.
- A modal renders only when `isOpen && type === "<name>"`. When it is done it calls `onClose()`, and forms also call `form.reset()`. After a successful write, call `router.refresh()`.
- Modals are client components. The provider waits for mount before rendering to avoid a hydration mismatch.

## Gotchas

- `modal-provider.tsx` lists every modal by hand. Forgetting the list entry leaves a new modal wired to the store but never rendered.

_Drafted by /audit from the repo, worth a quick human pass. Edit freely: once a line stops matching this draft, later runs treat it as curated and will flag rather than overwrite it._
