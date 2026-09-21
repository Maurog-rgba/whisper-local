# Local Voice Assistant — Windows quick test

This branch extends Whisper Local with parameterized local voice commands such as:

- "abra o chrome e pesquise como fazer uma IA rodar localmente"
- "pesquise no youtube raspberry pi whisper"
- "pesquise como instalar ollama"
- "digite bom dia, vou verificar isso hoje"
- "abra o chrome"

Everything in this first version runs locally. Web searches build an HTTP(S) URL locally and open it in the requested browser; the spoken query is not executed through a shell.

## 1. Clone the fork and switch to the feature branch

```powershell
git clone https://github.com/Maurog-rgba/whisper-local.git
cd whisper-local
git switch feature/local-voice-assistant
```

If the repository is already cloned:

```powershell
git fetch
git switch feature/local-voice-assistant
git pull
```

## 2. Create the Python environment

Python 3.11–3.13 is supported.

```powershell
py -3.12 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -e .
```

## 3. Reset voice-command defaults if needed

Whisper Local copies `commands.defaults.yaml` to your user config only when
`commands.yaml` does not already exist.

If you have run Whisper Local before and want the new Portuguese examples:

```powershell
Remove-Item "$env:APPDATA\whisperkey\commands.yaml" -ErrorAction SilentlyContinue
```

Do not delete the file if it contains custom commands you want to keep. In that case,
copy the new examples from `src/whisper_key/commands.defaults.yaml` manually.

## 4. Recommended CPU settings for the first test

Run once:

```powershell
whisper-local --setup
```

For a CPU-only machine, start with:

- model: `tiny` for lowest latency, or `base` for better accuracy
- compute type: `int8`
- language: Portuguese (`pt`) if most commands will be in Portuguese
- audio host: WASAPI on Windows

## 5. Check the installation

```powershell
whisper-local --doctor
```

Then launch:

```powershell
whisper-local
```

## 6. Test voice commands

Default command-mode hotkey on Windows: **Alt+Win**.

Hold/start command recording, speak a phrase, then use the configured stop key
(default: **Ctrl**).

Try:

```text
abra o chrome e pesquise como fazer uma IA rodar localmente
pesquise no youtube raspberry pi whisper
pesquise como instalar ollama
digite bom dia, vou verificar isso hoje
abra o chrome
```

Expected behavior:

| Phrase | Expected result |
|---|---|
| abra o chrome e pesquise X | Chrome opens a Google search for X |
| pesquise no youtube X | Default browser opens YouTube results for X |
| pesquise X | Default browser opens Google results for X |
| digite X | X is delivered to the current text field |
| abra o chrome | Chrome opens Google |

## 7. If Chrome is not found

The implementation checks the executable in PATH and common Windows install folders
under Program Files, Program Files (x86), and Local AppData. If Chrome is not found,
the URL falls back to the Windows default browser.

## Current scope

This first version deliberately does not implement always-listening wake-word mode yet.
It reuses Whisper Local's existing command hotkey, tray application, local Whisper model,
pause controls, command hot-reload, and optional local Ollama integration.

The next logical step after validating recognition/latency is a low-cost wake-word state:

```text
sleeping -> wake-word detector -> command listening -> Whisper -> action
```

That keeps Whisper from continuously consuming CPU while the assistant is idle.
