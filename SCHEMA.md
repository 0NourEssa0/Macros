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
| `game.platform` | string | `Roblox`, `Steam`, `PC`, `Minecraft`, `Browser`, `Mobile`, `Other`. |
| `game.url` | string | Link to the game. |
| `tags` | string[] | Shown on the card and as sidebar filter chips. Max 12. |
| `keywords` | string[] | Searched, never displayed. Put the words people actually type here. |
| `author.name` | string | **Required.** Publisher name or handle. |
| `author.url` | string | Link shown on the detail page. |
| `author.github` | string | GitHub handle, if you want the credit. |
| `version` | string | `1.0.0`. Bump it and the app offers everyone an **Update**. |
| `license` | string | `MIT`, `CC0`, `CC BY 4.0`, `All rights reserved`. |
| `created_at` / `updated_at` | ISO 8601 | Set automatically when published from the app. |
| `requires.mode` | `pid` \| `global` \| `""` | Applied automatically on Launch. |
| `requires.app` | string | Process name the macro drives, e.g. `RobloxPlayer`. |
| `requires.platform` | string | Free text — what the user needs to have. |
| `stats.steps` | number | Item count, computed on publish. |
| `stats.tools` | string[] | Tool kinds used, computed on publish. |
| `config` | object[] | The knobs a user can set before installing. See below. |
| `macro_file` | string | Defaults to `macro.json`. |

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
