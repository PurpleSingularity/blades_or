# Blades in the Dark — Owlbear Rodeo extension

Dice roller and progress clocks for *Blades in the Dark* and other Forged in the Dark games, embedded in Owlbear Rodeo.

## What's in here

| File | Purpose |
|---|---|
| `manifest.json` | Tells Owlbear Rodeo how to load the extension |
| `index.html`    | The whole popover UI (HTML + CSS + JS in one file, loads the OBR SDK from `esm.sh`) |
| `icon.svg`      | Toolbar icon |

No build step. Three static files, host them anywhere with HTTPS.

## Features

**Dice roller** — Action / Resistance / Fortune. Pick 0–6 dice; pick Position and Effect for action rolls. Outcomes follow the BitD rulebook:
- 2+ sixes → critical
- 6 → full success
- 4–5 → partial
- 1–3 → bad outcome
- 0 dice → roll 2d6, take the lowest (no crits possible)
- Resistance: stress = 6 − highest, crit clears 1

**Progress clocks** — 4/6/8/12-segment clocks rendered as the iconic BitD pie chart. Tick up/down, rename inline, delete. Synced to all players via room metadata, so everyone sees the same clocks update in real time.

**Sync model**
- Clocks live in `OBR.room.setMetadata` under `com.maksym.blades-or/clocks` — persists for the room.
- Dice rolls are broadcast (ephemeral) on channel `com.maksym.blades-or/dice-roll` — appear in everyone's recent-rolls feed and spotlight, attributed by player name and color.

## Hosting (GitHub Pages — easiest)

1. Create a new public repo, e.g. `blades-or`.
2. Drop `manifest.json`, `index.html`, `icon.svg` into the root.
3. **Settings → Pages → Build from branch → `main` / root**.
4. Wait ~30 seconds for the deploy.

Your manifest URL will be:
```
https://<your-username>.github.io/blades-or/manifest.json
```

That's the URL you give to Owlbear Rodeo.

> Other hosts work identically — Cloudflare Pages, Netlify, Vercel, even an S3 bucket with public read. Only requirement: HTTPS, and `manifest.json` + `index.html` + `icon.svg` reachable at the same origin.

## Installing in Owlbear Rodeo

1. Open Owlbear Rodeo → your profile.
2. **Add Extension** → paste your manifest URL.
3. In an existing room: open the room's extension menu and enable it. In a new room: tick it in the Create Room dialog.
4. Click the new icon in the top-left action bar to open the popover.

## Local testing without OR

Just open `index.html` in a browser — it detects the missing SDK and runs in standalone mode (a banner shows "no room sync available"). Useful for iterating on layout. Note: clocks won't persist between reloads in standalone mode.

## Customizing

- **Localization (Ukrainian, etc.)** — all UI strings are in plain text in `index.html`. Search-and-replace, or wrap them in a small `t(key)` function with a dictionary if you want a switcher. Roll outcome labels are in `outcomeLabel()`.
- **Color theme** — CSS variables at the top of the `<style>` block. The aesthetic is "letterpress dossier": sooty black, brass, faded crimson. Swap `--brass` and `--crimson` for a different feel.
- **Add roll types** — `Indulge Vice`, `Engagement`, `Long-Term Project Tick`, etc. all use the same dice engine. Add a new tab and a small case in `outcomeLabel()`.
- **Dice modifier chips** (Push +1d / Devil's Bargain +1d / Assist +1d) — easy v1.1: a row of toggle chips that increment the displayed pool before rolling.

## Known limits / things I deliberately didn't build

- **No GM-only permissions on clocks.** Anyone in the room can tick or delete. BitD groups are usually small and trusted, and players have their own project clocks anyway. If you want to lock delete to GM, gate the `del` action on `state.me.role === "GM"`.
- **No persistent roll history.** The feed is ephemeral — late joiners only see rolls from when they connected. To persist, push roll payloads into `OBR.room.setMetadata` instead of broadcast (and trim — room metadata caps at 16kB total).
- **Not a 3D dice roller.** Pip dice, instant reveal. Pair it with the dddice OR extension if you want physics.

## License

MIT. Do whatever you like.
