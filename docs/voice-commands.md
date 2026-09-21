# Voice Commands

Voice command mode lets you trigger actions by speaking. Press **Alt+Win** (default) to start recording, speak a trigger phrase, then press the stop key. If the transcription matches a trigger, the associated action runs.

## Configuration

Voice commands are configured in `commands.yaml`, located in your config folder:
- **Windows:** `%APPDATA%\whisperkey\commands.yaml`
- **macOS:** `~/.whisperkey/commands.yaml`

You can open this folder from the system tray menu: **Open Config Folder**.

## Format

Each command has a **trigger** phrase or **match_regex** and exactly one action: `run`, `hotkey`, `type`, `web_search`, `open_url`, or `rephrase`.

```yaml
commands:
  # Send a keyboard shortcut
  - trigger: "undo"
    hotkey: "ctrl+z"

  # Deliver pre-written text
  - trigger: "my email"
    type: "user@example.com"

  # Run a shell command
  - trigger: "open notepad"
    run: 'notepad.exe'
```

| Field | Description |
|-------|-------------|
| **trigger** | Phrase to match (case-insensitive, punctuation ignored) |
| **match_regex** | Python regex; named groups become template parameters |
| **run** | Shell command (`cmd.exe` on Windows, `/bin/sh` on macOS) |
| **hotkey** | Keyboard shortcut to send (e.g. `ctrl+z`, `win+left`) |
| **type** | Pre-written or parameterized text to deliver to the active window |
| **web_search** | Search query; supports template parameters |
| **open_url** | Open an HTTP(S) URL without routing it through the shell |

## Matching

- Triggers are matched as substrings — saying "please open notepad" matches `"open notepad"`
- Longer triggers are checked first, so `"open notepad plus plus"` won't accidentally match `"open notepad"`
- First match wins

## Dynamic commands with parameters

`match_regex` can capture parts of the spoken phrase with Python named groups.
Captured values become template parameters such as `${query}` or `${text}`.

```yaml
  # "abra o chrome e pesquise como rodar IA localmente"
  - match_regex: '^(?:abra|abre) (?:o )?chrome e (?:pesquise|procure)(?: por)? (?P<query>.+)
### Hotkey commands

Send keyboard shortcuts to the active window. Use `+` to combine keys.

```yaml
  - trigger: "undo"
    hotkey: "ctrl+z"
  - trigger: "select all"
    hotkey: "ctrl+a"
  - trigger: "snap left"
    hotkey: "win+left"
  - trigger: "show desktop"
    hotkey: "win+d"
```

### Type commands

Deliver pre-written text to the active window using the same method as transcription (clipboard paste or direct typing, depending on your `clipboard.delivery_method` setting).

If you stop recording with the **auto-send key** (Alt by default), Enter is sent after the text — useful for chat apps and terminals.

```yaml
  - trigger: "my email"
    type: "user@example.com"
  - trigger: "my address"
    type: "123 Main Street, City, State 12345"
```

### Shell commands

Run any shell command. The command runs asynchronously — it won't block the app.

```yaml
  - trigger: "open notepad"
    run: 'notepad.exe'
  - trigger: "open browser"
    run: 'start https://www.google.com'
  - trigger: "open downloads"
    run: 'explorer "%USERPROFILE%\Downloads"'
  - trigger: "lock screen"
    run: 'rundll32.exe user32.dll,LockWorkStation'
```

## Hotkey

The command hotkey and stop key are configured in your user settings (`user_settings.yaml`):
```yaml
hotkey:
  command_hotkey: "alt+win | macos: fn+command"
  stop_key: "ctrl | macos: fn"
  auto_send_key: "alt | macos: option"
```

- **stop_key** — stops recording and executes the matched command
- **auto_send_key** — same as stop key, but also sends Enter after `type` commands (ignored for `run` and `hotkey`)

Both keys are shared between transcription and command modes.

    web_search: '${query}'
    engine: "google"
    browser: "chrome"

  # "digite bom dia, vou verificar isso hoje"
  - match_regex: '^(?:digite|escreva) (?P<text>.+)
### Hotkey commands

Send keyboard shortcuts to the active window. Use `+` to combine keys.

```yaml
  - trigger: "undo"
    hotkey: "ctrl+z"
  - trigger: "select all"
    hotkey: "ctrl+a"
  - trigger: "snap left"
    hotkey: "win+left"
  - trigger: "show desktop"
    hotkey: "win+d"
```

### Type commands

Deliver pre-written text to the active window using the same method as transcription (clipboard paste or direct typing, depending on your `clipboard.delivery_method` setting).

If you stop recording with the **auto-send key** (Alt by default), Enter is sent after the text — useful for chat apps and terminals.

```yaml
  - trigger: "my email"
    type: "user@example.com"
  - trigger: "my address"
    type: "123 Main Street, City, State 12345"
```

### Shell commands

Run any shell command. The command runs asynchronously — it won't block the app.

```yaml
  - trigger: "open notepad"
    run: 'notepad.exe'
  - trigger: "open browser"
    run: 'start https://www.google.com'
  - trigger: "open downloads"
    run: 'explorer "%USERPROFILE%\Downloads"'
  - trigger: "lock screen"
    run: 'rundll32.exe user32.dll,LockWorkStation'
```

## Hotkey

The command hotkey and stop key are configured in your user settings (`user_settings.yaml`):
```yaml
hotkey:
  command_hotkey: "alt+win | macos: fn+command"
  stop_key: "ctrl | macos: fn"
  auto_send_key: "alt | macos: option"
```

- **stop_key** — stops recording and executes the matched command
- **auto_send_key** — same as stop key, but also sends Enter after `type` commands (ignored for `run` and `hotkey`)

Both keys are shared between transcription and command modes.

    type: '${text}'
```

Named captures are trimmed before expansion. They also work inside `then:` macro steps.
For shell `run:` actions, dynamic values are treated as untrusted input and keep the
existing confirmation/safety path. Prefer `web_search` and `open_url` for browser
automation because they do not execute the spoken text through a shell.

### Web search

```yaml
  - match_regex: '^pesquise (?P<query>.+)
### Hotkey commands

Send keyboard shortcuts to the active window. Use `+` to combine keys.

```yaml
  - trigger: "undo"
    hotkey: "ctrl+z"
  - trigger: "select all"
    hotkey: "ctrl+a"
  - trigger: "snap left"
    hotkey: "win+left"
  - trigger: "show desktop"
    hotkey: "win+d"
```

### Type commands

Deliver pre-written text to the active window using the same method as transcription (clipboard paste or direct typing, depending on your `clipboard.delivery_method` setting).

If you stop recording with the **auto-send key** (Alt by default), Enter is sent after the text — useful for chat apps and terminals.

```yaml
  - trigger: "my email"
    type: "user@example.com"
  - trigger: "my address"
    type: "123 Main Street, City, State 12345"
```

### Shell commands

Run any shell command. The command runs asynchronously — it won't block the app.

```yaml
  - trigger: "open notepad"
    run: 'notepad.exe'
  - trigger: "open browser"
    run: 'start https://www.google.com'
  - trigger: "open downloads"
    run: 'explorer "%USERPROFILE%\Downloads"'
  - trigger: "lock screen"
    run: 'rundll32.exe user32.dll,LockWorkStation'
```

## Hotkey

The command hotkey and stop key are configured in your user settings (`user_settings.yaml`):
```yaml
hotkey:
  command_hotkey: "alt+win | macos: fn+command"
  stop_key: "ctrl | macos: fn"
  auto_send_key: "alt | macos: option"
```

- **stop_key** — stops recording and executes the matched command
- **auto_send_key** — same as stop key, but also sends Enter after `type` commands (ignored for `run` and `hotkey`)

Both keys are shared between transcription and command modes.

    web_search: '${query}'
    engine: "google"
    browser: "chrome"
```

Supported engines: `google`, `bing`, `duckduckgo`, and `youtube`.
Supported explicit browser aliases are `chrome`, `edge`, and `firefox`.
If an explicit browser is not found, Whisper Local falls back to the system default.

### Open URL

```yaml
  - trigger: "open dashboard"
    open_url: "https://example.com/dashboard"
    browser: "edge"
```

Only `http://` and `https://` URLs are accepted.

## Command Types

### Hotkey commands

Send keyboard shortcuts to the active window. Use `+` to combine keys.

```yaml
  - trigger: "undo"
    hotkey: "ctrl+z"
  - trigger: "select all"
    hotkey: "ctrl+a"
  - trigger: "snap left"
    hotkey: "win+left"
  - trigger: "show desktop"
    hotkey: "win+d"
```

### Type commands

Deliver pre-written text to the active window using the same method as transcription (clipboard paste or direct typing, depending on your `clipboard.delivery_method` setting).

If you stop recording with the **auto-send key** (Alt by default), Enter is sent after the text — useful for chat apps and terminals.

```yaml
  - trigger: "my email"
    type: "user@example.com"
  - trigger: "my address"
    type: "123 Main Street, City, State 12345"
```

### Shell commands

Run any shell command. The command runs asynchronously — it won't block the app.

```yaml
  - trigger: "open notepad"
    run: 'notepad.exe'
  - trigger: "open browser"
    run: 'start https://www.google.com'
  - trigger: "open downloads"
    run: 'explorer "%USERPROFILE%\Downloads"'
  - trigger: "lock screen"
    run: 'rundll32.exe user32.dll,LockWorkStation'
```

## Hotkey

The command hotkey and stop key are configured in your user settings (`user_settings.yaml`):
```yaml
hotkey:
  command_hotkey: "alt+win | macos: fn+command"
  stop_key: "ctrl | macos: fn"
  auto_send_key: "alt | macos: option"
```

- **stop_key** — stops recording and executes the matched command
- **auto_send_key** — same as stop key, but also sends Enter after `type` commands (ignored for `run` and `hotkey`)

Both keys are shared between transcription and command modes.
