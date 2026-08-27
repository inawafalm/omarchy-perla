# Perla

**Give Omarchy a voice.**

Talk naturally. Perla works out what you want, acts across your desktop, and
tells you when it's done — it is not a dictation box. Voxtype stays your
dictation tool; Perla is for conversations, actions, and spoken results.

![The Perla orb listening on a dark Omarchy desktop, sound rippling outward from it](preview.png)

| Keyboard | Your voice |
|---|---|
| ~45 words/min | ~150 words/min |

An orb in the bar, a local Rust daemon doing the realtime voice session.

## Install

```sh
omarchy plugin add https://github.com/inawafalm/omarchy-perla --enable
```

Click the pearl in your bar, press **Set up Perla**, and paste your own OpenAI
or xAI key when the panel asks. (Already export `OPENAI_API_KEY`? Setup offers
to reuse it and you skip that step.)

Setup opens a terminal so you can watch it: it downloads a checksummed
`perla-d` into `~/.local/bin`, enables `perla.service` for your user, and only
touches pacman for packages you're actually missing. Turn on **Computer use**
first if you also want Perla to see the screen, click, and type.

`omarchy plugin add` deliberately runs nothing — no hooks, no sudo. That's why
the last step is a button you press rather than something that happens quietly.

<details>
<summary>Running setup yourself instead</summary>

```sh
~/.config/omarchy/plugins/nawaf.perla/bin/perla-setup --help
```

`--with-computer-use` adds the harness, `--from-source` compiles instead of
downloading, `--import-env-key` copies an existing key from your environment,
`--yes` skips the prompt. Re-running is safe — it installs only what's missing.

Optional hotkeys for `~/.config/hypr/bindings.conf`:

```ini
bind = SUPER ALT, SPACE, exec, perla-d toggle-listen
bind = SUPER SHIFT, BackSpace, exec, sh -c 'perla-d mute; omarchy-harness stop'
```

The second is a panic switch — mutes Perla, stops computer use. Perla does not
take Voxtype's F9 or Super+Ctrl+X.
</details>

## Using it

Left-click the orb for listen/mute. Right-click for settings: provider, voice
model, how chatty the spoken progress is, and reply language. Changes reload
the daemon and reconnect an active session automatically.

## Remove

Right-click the orb → **Uninstall the daemon…**, or:

```sh
~/.config/omarchy/plugins/nawaf.perla/bin/perla-uninstall
omarchy plugin remove nawaf.perla
```

Your key and history survive on purpose; `--purge` deletes those too.

## Privacy

Bring your own key — there are no credentials or Perla servers in this repo.
Your key goes to the daemon over stdin, never a process argument, and lands in
`~/.config/perla-voice/config.toml` at mode `0600`, written atomically and
never through a symlink. Audio goes straight from your machine to the provider
you picked, billed to your account. The local debug trail stays local unless
you press **Copy debug log**.

Computer use can see the screen and type as your Linux user, and plugins run as
unsandboxed QML inside `omarchy-shell` — read the source, and keep the panic
switch handy. Cost controls are on by default: efficient model, bounded
context, short replies, idle shutdown.

## Development

```sh
node test-model.js
cargo test --manifest-path perla-voice/Cargo.toml --workspace
uv run --with pytest --with ./omarchy-harness python -m pytest omarchy-harness/tests
```

MIT. Copyright 2026 Nawaf.
