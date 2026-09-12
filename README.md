# AutoKey Studio — Macro Hub

The public macro library for [AutoKey Studio](https://sologrinding.com/). Every
macro in here shows up inside the app under **Macros ▸ ◈ Macro Hub**, where it can
be searched, read, configured, installed and launched — nobody has to download a
JSON file or know this repository exists.

## Layout

```
index.json                                     generated catalogue — the app reads this first
games.json                                     shared game presentation (icons, full titles)
macros/<game-slug>/<macro-slug>/
    manifest.json                              latest metadata + the full version list
    macro.json                                 the latest version's macro
    versions/<version>/manifest.json           every release, frozen and kept forever
    versions/<version>/macro.json
```

One folder per macro, grouped by game. The folder path *is* the macro's id:
`macros/fisch/fisch-macro` → `fisch/fisch-macro`.

**Every version is kept.** Publishing an update adds a `versions/<v>/` folder and
moves the top-level copy forward; it never rewrites or removes an older release.
In the app, the detail page lists every version and any of them can be installed.

## How the app reads it

1. It fetches `index.json` — one request for the entire library.
2. If `index.json` is missing or a macro was added by hand on github.com, it falls
   back to walking the tree and reading every `manifest.json`. Either way the macro
   appears; the index is only there to make the common case fast.
3. The result is cached on the user's machine for five minutes, and the cache is
   served if GitHub is unreachable, so the hub still opens offline.

## How macros get in here

**From the app: Macro Hub ▸ ↥ Publish a macro.** Fill in the form and press publish.
The app sends the macro to the SoloGrinding server, which validates it and commits
it here. There is nothing to install, clone or connect — being signed into AutoKey
Studio is the whole requirement.

The server is also what enforces the rules that keep the library honest:

- the first account to publish a macro **owns** it, and only that account can
  publish an update to it;
- a **version number is used once** — an update has to be a new, higher number;
- `author` and `owner` are stamped from the signed-in account, so nobody can
  publish under someone else's name.

**This repository is the only store.** The server keeps no copy of the library in
a database: ownership, versions, stars and pins are all read from the files here,
and every publish rebuilds `index.json` from the macro folders that actually exist.
So a macro folder added by hand on github.com is listed, and a folder deleted here
is gone from the app. A hand-added `manifest.json` needs an `owner` for anyone to
publish updates to it from the app; without one it can only be changed here.

To refresh the catalogue straight after a hand edit, without waiting for the next
publish:

```bash
python3 tools/build_macro_index.py ~/Documents/AutoKeyStudio-Macros
```

See `MACRO_HUB_SERVER_SPEC.md` in the app repo for the publish endpoint.

## What makes a good listing

- **Describe what it does, not what it is.** "Taps space every couple of minutes so
  Roblox never disconnects you" beats "anti-idle macro".
- **Tag it the way someone would search for it** — the game, the activity, the
  problem it solves. `keywords` catches everything else; it is searched but never shown.
- **Expose the numbers people will want to change** as `config` entries, so a
  non-technical user can tune the macro without opening the timeline.
- **Say which mode it needs.** `requires.mode` and `requires.app` are applied
  automatically when someone launches your macro.
- **Write release notes.** They show next to the version in the app, and they are how
  someone decides whether to take an update.

See [SCHEMA.md](SCHEMA.md) for every field.

## Licence

Each macro carries its own `license` field. Everything published by *AutoKey Studio*
is MIT.
