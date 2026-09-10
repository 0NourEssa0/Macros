# manifest.json

Every field the app reads. Only `title`, `description`, `game.name` and
`author.name` are required — everything else has a sensible default, and an
unknown field is preserved but ignored.

| Field | Type | Meaning |
|---|---|---|
| `schema` | number | Format version. Currently `1`. |
| `id` | string | `<game-slug>/<macro-slug>`. Must match the folder path. |
| `path` | string | `macros/<id>`. |
| `slug` | string | The macro half of the id. |
| `title` | string | **Required.** Shown as the card headline. |
| `description` | string | **Required.** One or two sentences; the card body. |
| `details` | string | Long description shown on the detail page. Newlines are kept. |
| `game.name` | string | **Required.** Short game name — becomes a sidebar filter. |
| `game.title` | string | Full title, e.g. `Fisch (Roblox)`. |
| `game.slug` | string | URL-safe game name; the first path segment. |
| `game.icon` | string | One emoji — the tile in the library. |
| `game.icon_url` | string | Optional image URL used instead of the emoji. |
| `thumbnail` | string | **Optional.** `https://` link to a screenshot or banner. Shown across the top of the card and above the detail page. A non-https link is dropped when read, and one that fails to load removes itself rather than showing a broken tile. |
| `game.platform` | string | `Roblox`, `Steam`, `PC`, `Minecraft`, `Browser`, `Mobile`, `Other`. |
| `game.url` | string | Link to the game. |
| `tags` | string[] | Shown on the card and as sidebar filter chips. Max 12. |
| `keywords` | string[] | Searched, never displayed. Put the words people actually type here. |
| `author.name` | string | The publishing account. **Stamped by the server** — not read from the app. |
| `author.url` | string | Link shown on the detail page. |
| `author.github` | string | GitHub handle, if you want the credit. |
| `owner` | string | The account that owns the listing; only it may publish updates. **Stamped by the server.** An owner starting with `@` is a reserved official listing. |
| `version` | string | The latest release, e.g. `1.0.0`. Bump it and the app offers everyone an **Update**. |
| `versions` | object[] | Every release, newest first. See below. |
| `license` | string | `MIT`, `CC0`, `CC BY 4.0`, `All rights reserved`. |
| `created_at` / `updated_at` | ISO 8601 | Set automatically when published from the app. |
| `requires.mode` | `pid` \| `global` \| `""` | Applied automatically on Launch. |
| `requires.app` | string | Process name the macro drives, e.g. `RobloxPlayer`. |
| `requires.platform` | string | Free text — what the user needs to have. |
| `stats.steps` | number | Item count, computed on publish. |
| `stats.tools` | string[] | Tool kinds used, computed on publish. |
| `config` | object[] | The knobs a user can set before installing. See below. |
| `macro_file` | string | Defaults to `macro.json`. |

## versions

```json
"versions": [
  { "version": "1.1.0", "published_at": "2026-09-10T18:00:00Z",
    "notes": "Handles the new reel bar colours.", "steps": 18, "author": "marina" },
  { "version": "1.0.0", "published_at": "2026-08-02T09:12:00Z",
    "notes": "First release.", "steps": 16, "author": "marina" }
]
```

Newest first. Each entry has a folder under `versions/<version>/` holding the manifest
and macro exactly as they were at that release, so the app can install any of them —
the detail page lists them all and the Configure sheet has a version picker.

`notes` is what the publisher wrote in **What changed in this version**; it is shown
beside the version in the app, so it is worth filling in.

A listing published before versioning existed carries only `version`; the app reads it
as a one-entry history rather than failing.

## config entries

```json
{ "key": "idle_seconds", "label": "Seconds between presses", "type": "number",
  "default": "120", "help": "Anything under the idle timeout.",
  "var": "idle_seconds", "options": [] }
```

| Field | Meaning |
|---|---|
| `key` | Identifier. Also the `{{key}}` placeholder name. |
| `label` | What the user sees. |
| `type` | `text`, `number`, `bool`, or `select` (with `options`). |
| `default` | Applied on install when the user changes nothing. |
| `help` | One line under the field. |
| `var` | Name of a **Variable** tool in the macro whose value this sets. |
| `options` | Choices, for `type: "select"`. |

There are two ways a value reaches the macro, and you can use both:

1. **`var`** — the value is written into the matching `Variable` tool.
2. **`{{key}}` placeholders** — any string anywhere in `macro.json` containing
   `{{key}}` has it substituted. This is how a number field such as a wait can be
   made configurable:

```json
{ "kind": "wait", "seconds": "{{idle_seconds}}" }
```

Defaults are always applied, so a placeholder can never survive into an installed
macro unresolved.

If a manifest declares no `config` at all, every **named Variable** in the macro
becomes a knob automatically.

# macro.json

The macro exactly as **Save macro** writes it — a JSON array of tool objects
(`step`, `wait`, `loop`, `cond`, `trackbar`, …). Export one from the app
(**Export .json**) if you want a starting point. Sounds and trained models are
embedded in the file, so a published macro is self-contained.
