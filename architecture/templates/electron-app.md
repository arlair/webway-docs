# Electron App Template

Standard architecture for Electron desktop applications with SvelteKit.

## Projects Using This Template

- enlucent-app

---

## Tech Stack

| Layer        | Technology                |
| ------------ | ------------------------- |
| Desktop      | Electron                  |
| UI Framework | SvelteKit + Svelte 5      |
| Styling      | TailwindCSS 4 + DaisyUI 5 |
| Build        | electron-builder          |

---

## Required Packages

| Package                                          | Purpose                |
| ------------------------------------------------ | ---------------------- |
| [@eldarlabs/core](../packages/core.md)           | Utilities and schemas  |
| [@eldarlabs/svelte-ui](../packages/svelte-ui.md) | Svelte 5 UI components |
| [@eldarlabs/sveltekit](../packages/sveltekit.md) | SvelteKit utilities    |

---

## Folder Structure

```
src/
├── main/                 # Electron main process
│   ├── index.ts          # Main entry point
│   ├── preload.ts        # Preload script (context bridge)
│   └── ipc/              # IPC handlers
│       └── handlers.ts
│
├── renderer/             # SvelteKit renderer
│   ├── routes/           # SvelteKit routes
│   ├── lib/              # Shared utilities
│   └── app.html
│
└── shared/               # Shared between main/renderer
    └── types.ts          # IPC message types
```

---

## IPC Communication

### Type-Safe IPC

```typescript
// shared/types.ts
export interface IpcChannels {
  "file:read": { path: string };
  "file:write": { path: string; content: string };
}

export interface IpcResponses {
  "file:read": { content: string };
  "file:write": { success: boolean };
}
```

### Main Process Handler

```typescript
// main/ipc/handlers.ts
import { ipcMain } from "electron";
import type { IpcChannels, IpcResponses } from "../../shared/types";

ipcMain.handle("file:read", async (_, args: IpcChannels["file:read"]) => {
  const content = await fs.readFile(args.path, "utf-8");
  return { content } satisfies IpcResponses["file:read"];
});
```

### Renderer Invocation

```typescript
// renderer/lib/ipc.ts
export async function readFile(path: string) {
  return window.electronAPI.invoke("file:read", { path });
}
```

---

## Preload Script

```typescript
// main/preload.ts
import { contextBridge, ipcRenderer } from "electron";

contextBridge.exposeInMainWorld("electronAPI", {
  invoke: (channel: string, data: unknown) => {
    return ipcRenderer.invoke(channel, data);
  },
  on: (channel: string, callback: Function) => {
    ipcRenderer.on(channel, (_, ...args) => callback(...args));
  },
});
```

---

## Build & Distribution

```bash
# Development
npm run dev

# Build for current platform
npm run build

# Build for all platforms
npm run build:all
```

### electron-builder Config

```json
// package.json
{
  "build": {
    "appId": "com.example.app",
    "productName": "App Name",
    "directories": {
      "output": "dist"
    },
    "mac": {
      "target": ["dmg", "zip"]
    },
    "win": {
      "target": ["nsis", "portable"]
    }
  }
}
```

---

## Security Considerations

1. **Context Isolation**: Always enabled
2. **Node Integration**: Disabled in renderer
3. **Preload Scripts**: Use contextBridge for safe API exposure
4. **CSP**: Configure Content Security Policy

---

## Related Documentation

- [Coding Guidelines](../../coding-guidelines.md) - Code style rules
- [Testing Strategy](../../testing.md) - Testing approach
