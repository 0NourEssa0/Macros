# AutoKey Studio — Macro Hub

The public macro library for [AutoKey Studio](https://sologrinding.com/). Every
macro in here shows up inside the app under **Macros ▸ ◈ Macro Hub**, where it can
be searched, read, configured, installed and launched — nobody has to download a
JSON file or know this repository exists.

## Layout

```
index.json                                generated catalogue — the app reads this first
games.json                                shared game presentation (icons, full titles)
macros/<game-slug>/<macro-slug>/
    manifest.json                         game, title, description, tags, author, version…
    macro.json                            the macro itself, exactly as the editor saves it
```

One folder per macro, grouped by game. The folder path *is* the macro's id:
`macros/roblox/afk-anti-idle` → `roblox/afk-anti-idle`.

## How the app reads it

1. It fetches `index.json` — one request for the entire library.
2. If `index.json` is missing or a macro was added by hand on github.com, it falls
   back to walking the tree and reading every `manifest.json` directly. Either way
   the macro appears; the index is only there to make the common case fast.
3. The result is cached on the user's machine for five minutes, and the cache is
   served if GitHub is unreachable, so the hub still opens offline.

`index.json` is regenerated automatically on every publish. If you edit a manifest
by hand, regenerate it:

```bash
python3 -c "import macro_hub as h; h.rebuild_local_index('.', h.config()['repo'])"
```

## Publishing

The ordinary way is from inside the app: **Macro Hub ▸ ↥ Publish a macro**. Fill in
the form, press publish, and the app writes both files, regenerates the index, and
pushes — either through a local clone of this repository or through the GitHub API
with a token, whichever is set up in **Macro Hub ▸ ⚙**.

Adding one by hand works too: create the two files under `macros/<game>/<macro>/`,
regenerate the index, and open a pull request.

## What makes a good listing

- **Describe what it does, not what it is.** "Taps space every couple of minutes so
  Roblox never disconnects you" beats "anti-idle macro".
- **Tag it the way someone would search for it** — the game, the activity, the
  problem it solves. `keywords` catches everything else; it is searched but never shown.
- **Expose the numbers people will want to change** as `config` entries, so a
  non-technical user can tune the macro without opening the timeline.
- **Say which mode it needs.** `requires.mode` and `requires.app` are applied
  automatically when someone launches your macro.

See [SCHEMA.md](SCHEMA.md) for every field.

## Licence

Each macro carries its own `license` field. Everything published by *AutoKey Studio*
is MIT.
