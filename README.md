# uaRO on Apple Silicon, via Whisky

**This repo is one file that installs the uaRO Windows game on your Mac by talking to an AI, not by reading instructions yourself.**

[`SKILL.md`](./SKILL.md) is a self-contained playbook written *for an AI coding agent* (Claude Code, OpenAI Codex, or GitHub Copilot) to read and execute, not for a human to follow by hand. Hand it the file, say "install uaRO," and it drives the whole thing end to end. Curious what that actually involves? Open `SKILL.md` — it's all in there.

## New in v0.20.3: AzzyAI is now supported

The uaRO installation skill now includes built-in support for **AzzyAI**.

AzzyAI is an optional add-on that allows your **mercenary** or **homunculus** to automatically find and attack monsters while you play.

When setting up uaRO from scratch, the skill will ask whether you also want to install AzzyAI after the main game installation is complete.

[See the AzzyAI installation and repair options below.](#updating-an-existing-install-or-adding-azzyai)

## The problem this solves

[uaRO](https://uaro.net/) (a Ragnarok Online private server) is a Windows-only game protected by **Gepard Shield 3.0** anti-cheat. The obvious approach — run it in a **Windows 11** VM (VMware Fusion) — turned into a dead end:

- More virtual CPUs → Gepard threw an error almost immediately.
- Fewer virtual CPUs → avoided the error, but the game stuttered badly within seconds.
- Disabling Windows Defender, or whitelisting the game instead → no effect either way.
- Every Visual C++ Redistributable from 2005 through 2026, x86/x64/ARM → no effect.
- Every graphics tweak (resolution, texture quality, DirectX version, graphics device, windowed vs. fullscreen) → no effect.

None of it mattered, because the actual blocker was never performance — it was Gepard Shield not working well with the **Windows 11 on ARM** processor that a VM on Apple Silicon has to run.

## The fix, and what made it hard

**Whisky** (a lightweight macOS Wine wrapper) sidesteps the problem: it translates the game's x86 Windows calls directly on macOS, no ARM Windows kernel involved. But Whisky itself is discontinued and broken in non-obvious ways:

- Its Homebrew cask and Wine-runtime download endpoint are both dead upstream.
- A required internal config file has to match an exact schema, or Whisky silently rejects it.
- iCloud Drive sync can relocate freshly-written files out from under an in-progress install.
- The game's patcher hard-depends on a Wine component (Gecko) with a one-shot install prompt.
- The Windows installer crashes under Rosetta unless two specific bytes in it are patched.

`SKILL.md` is every one of those fixes, plus the verification to catch it if any fails silently again — found by actually running the whole process once, end to end, on a real machine.

## How to actually run this

You'll drive this by talking to an AI — through its desktop app, or its command-line tool. **Claude, ChatGPT, and GitHub Copilot can all complete this install** — pick whichever one you already have. No preference? Start with **ChatGPT**.

**Option A — Desktop app (no Terminal needed):**
A normal Mac app, like any other — you click around in a window, no typing commands. Good if you've never used Terminal (the black command-line window under Applications → Utilities) before.

1. Download one of these and sign in:
   - [Claude](https://claude.com/download)
   - [ChatGPT](https://chatgpt.com/download)
   - [GitHub Copilot](https://github.com/features/ai/github-app)
2. Click the **Code** tab (Claude), **Codex** tab (ChatGPT), or the **+** next to **Sessions** (GitHub Copilot). It'll ask you to pick a folder — just pick your **Documents** folder, it doesn't matter which one for this.
3. Paste this whole thing into the chat box:
   ```
   Fetch SKILL.md from https://github.com/jirukouya/auRO-whisky-macOS-setup and follow it step by step to install uaRO on this Mac. Stop after each step and show me the progress table before continuing.
   ```

**Option B — Terminal:**
A command-line tool (`claude`, `codex`, or `copilot`) that you type into Mac's built-in Terminal app instead of clicking a window. Good if you're already comfortable there.

1. **Open Terminal:**
   - Press **⌘ Cmd + Space**, type `Terminal`, then press **Return** — or
   - Open **Finder → Applications → Utilities → Terminal**.

   Then use one of these:
   - [Claude Code](https://claude.com/claude-code) — `claude`. Install it with:
     ```
     curl -fsSL https://claude.ai/install.sh | bash
     ```
   - [OpenAI Codex CLI](https://github.com/openai/codex) — `codex`. Install it with:
     ```
     curl -fsSL https://chatgpt.com/codex/install.sh | sh
     ```
   - [GitHub Copilot CLI](https://github.com/features/copilot/cli) — `copilot`. Install it with:
     ```
     curl -fsSL https://gh.io/copilot-install | bash
     ```
2. **Paste this whole thing:**
   ```
   Fetch SKILL.md from https://github.com/jirukouya/auRO-whisky-macOS-setup and follow it step by step to install uaRO on this Mac. Stop after each step and show me the progress table before continuing.
   ```
3. **From there, just answer what it asks.** It'll tell you before anything you need to personally do — logging into the uaRO download page, clicking through the installer wizard, typing your Mac password if macOS asks for it — and it won't move to the next step without checking with you first.

## Updating an existing install or adding AzzyAI

Already installed uaRO with this skill before? Open an AI session the same way as above, then choose the option you need below.

### Update the existing uaRO / Whisky installation

Use this if uaRO is already installed and you want the AI to check for newer fixes:

```
Fetch SKILL.md from https://github.com/jirukouya/auRO-whisky-macOS-setup and check my existing uaRO installation against the latest fixes. Apply only the fixes that are missing.
```

The skill will detect what's out of date and only touch what's actually missing — it won't reinstall anything that's already working. See [CHANGELOG.md](./CHANGELOG.md) for what's changed release to release.

### Install AzzyAI

Use this if uaRO is already installed and you want your mercenary or homunculus to automatically attack monsters:

```
Please read and follow this AzzyAI guide step by step:

https://github.com/jirukouya/auRO-whisky-macOS-setup/blob/main/AZZYAI_FIXES.md

I already have uaRO installed. Help me install and configure AzzyAI for my mercenary or homunculus. I am not technical, so explain each step in simple language, make a backup before changing existing files, and stop for my confirmation before continuing.
```

### Fix AzzyAI

Use this if AzzyAI is already installed, but your mercenary or homunculus follows you without attacking:

```
Please read and follow this AzzyAI guide:

https://github.com/jirukouya/auRO-whisky-macOS-setup/blob/main/AZZYAI_FIXES.md

AzzyAI is already installed on my uaRO setup, but my mercenary or homunculus follows me without attacking. Check the installation first, then apply the required uaRO targeting fixes. I am not technical, so explain each step simply and stop for my confirmation before continuing.
```

The AI will guide you through the steps and will tell you when you need to log in to the game and type `/merai` or `/hoai`.

## What you'll need

- A Mac with **Apple Silicon** (M1 or later) on **macOS 14 (Sonoma) or newer** — Whisky itself requires both; there's no path through this skill on an Intel Mac. Confirmed working as far up as **macOS 26 (Tahoe)** by a real install — the big jump in Apple's own version numbering (14 → 15 → 26) isn't a compatibility gap, "or newer" genuinely means newer.
- Roughly **15-20GB of free disk space**.
- A **uaRO account** — the installer download sits behind a login wall on uaRO's own site, so getting the installer file itself is always a manual, logged-in step no AI can do on your behalf.

## Uninstalling

Same idea, in reverse: hand the AI this same `SKILL.md` and ask it to uninstall uaRO. Pick how much to undo:

| Level | Removes |
|---|---|
| 1 | Just the game |
| 2 | + the Wine bottle |
| 3 | + Whisky itself |
| 4 | + shared infra (Homebrew, Rosetta) |

## Status

**Public.** These Whisky fixes are useful beyond just uaRO, so this repo's [Releases](../../releases) archive Whisky.app and its Wine runtime in case the upstream sources ever disappear for good.

## Changelog

Full version history lives in [CHANGELOG.md](./CHANGELOG.md) — check `SKILL.md`'s own frontmatter for the current version number rather than this file, so there's only one place that can go stale instead of two.

## Disclaimer

This is unofficial. Run it at your own risk:

- **No guarantees.** This is a personal project, shared as-is — see [`TROUBLESHOOTING.md`](./TROUBLESHOOTING.md) for what's already known to be imperfect, including one unresolved crash.
- **This patches a third-party binary.** The uaRO installer gets a few bytes changed to work around a Rosetta translation bug. A backup is made automatically first, but it's still altering someone else's executable.
- **Real changes to your Mac.** This installs Homebrew, Rosetta, Whisky, three apps in `/Applications`, and optionally a small `uaro-cli` command-line helper in `/opt/homebrew/bin` — all reversible (see *Uninstalling* above), but not a sandboxed trial run.
- **No data collection.** Nothing here collects, transmits, or stores your personal data, credentials, or usage. Every login along the way (your Mac's admin password, your uaRO account) is handled directly by you — never by this skill or the AI running it.

## Acknowledgments

- **@45rn0d3u5** on the uaRO Discord, who wrote and shared the original [`install-uaro-mac` reference](https://docs.google.com/document/d/1ISi_iijWQuf5AeAh-ITtLYWm-My444x--d7rvQLiaL8/edit?tab=t.0) this skill was built on top of.
- **[Isaac Marovitz](https://github.com/IsaacMarovitz)**, creator of [Whisky](https://getwhisky.app/), the Wine wrapper this entire install path depends on.

## License

[MIT](./LICENSE) — free to use, modify, and share; provided as-is, with no warranty.
