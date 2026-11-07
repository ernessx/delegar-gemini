# delegar-gemini: a Claude Code skill to delegate work to Gemini

[Version en espanol](README.md)

A Claude Code skill plus a small script that lets Claude **delegate routine tasks to Gemini** through the `agy` terminal (Antigravity CLI) using your Gemini subscription. Claude launches the task and only reads a short report with a verdict, instead of spending its own tokens on repetitive work.

## What it does
The script:
1. Sends your prompt to Gemini with `agy --model <model> -p <prompt>`.
2. Optionally runs **your own check command** against real state once Gemini finishes.
3. Stores prompt, response and verdict in a log and prints a short report (VERIFICADO / FALLO LA COMPROBACION / SIN COMPROBACION AUTOMATICA, i.e. verified / check failed / no automatic check).

Key idea: do not trust what Gemini *says* it did; **verify against real state** with a deterministic command.

## Requirements
- Linux, macOS or **Windows 10/11** with Python 3 (on Windows `python --version` must work in cmd or PowerShell), Claude Code, and `agy` installed and logged in with an account that has a Gemini subscription (check with `agy models`).

## Install
```bash
git clone https://github.com/ernessx/delegar-gemini.git && cd delegar-gemini
mkdir -p ~/.local/bin ~/.claude/skills/delegar-gemini
cp scripts/delegar-gemini ~/.local/bin/ && chmod +x ~/.local/bin/delegar-gemini
cp skill/SKILL.md ~/.claude/skills/delegar-gemini/SKILL.md
```
Make sure `~/.local/bin` is on your `PATH`. If `agy` is not on PATH: `export DELEGAR_AGY=<PATH>/agy`.

### Install on Windows (PowerShell)
```powershell
git clone https://github.com/ernessx/delegar-gemini.git; cd delegar-gemini
New-Item -ItemType Directory -Force "$env:USERPROFILE\.local\bin", "$env:USERPROFILE\.claude\skills\delegar-gemini" | Out-Null
Copy-Item scripts\delegar-gemini, scripts\delegar-gemini.cmd "$env:USERPROFILE\.local\bin\"
Copy-Item skill\SKILL.md "$env:USERPROFILE\.claude\skills\delegar-gemini\SKILL.md"
```
Add `%USERPROFILE%\.local\bin` to your user PATH and open a new terminal. If `agy` is not found: `$env:DELEGAR_AGY = "C:\path\to\agy.exe"`. `delegar-gemini.cmd` is a shim that calls Python with the script.

## Usage
```bash
delegar-gemini libre prompt.md --comprobar "test -s notes/summary.md" --nombre "summary"
delegar-gemini historial
```
On Windows the check command cannot use `test`; for example `--comprobar "if exist notes\summary.md (exit 0) else (exit 1)"` in cmd, or a portable `python -c` one-liner.

Optional env vars: `DELEGAR_AGY`, `DELEGAR_MODELO` (default `gemini-3.8-flash-high`), `DELEGAR_CWD`, `DELEGAR_SALIDA`, `DELEGAR_TIMEOUT`.

## Trust policy
Delegate: repetitive, bounded, command-verifiable tasks. Do not delegate: unclear-cause diagnosis, wiring/safety, design decisions, purchases, infrastructure changes, anything risky.
1. Review the first run of any new task type by hand.
2. Afterwards, a VERIFIED verdict needs no manual review.
3. If the check fails or is missing, review and fix it yourself.
4. Never give Gemini generic commands (`cat`, `grep`, `sudo`); expose a narrow tool instead. Never put secrets in prompts.

## Why `agy` with a subscription, not the free API
The free Gemini API tier requires sharing your data with Google. The subscription via `agy` is the private channel.

## Known limitations / to verify
- This is a generalized version of the author's original script, which also had recipes tied to personal infrastructure (not included). The generalized script **was tested against a real `agy` on Linux** (small task, VERIFICADO verdict).
- **Windows is untested.** The script is written to be cross-platform (paths, UTF-8, `agy.exe`/`agy.cmd`) but has **not been run on Windows** (Linux only so far); open an issue if something fails. Very long prompts may hit Windows' ~32,000-character command-line limit.
- To verify: exact `agy` flags in your version (`agy --help`), available models, behavior with very large prompts, and the format of `agy`'s permission settings.
- Checks are your responsibility; the script only runs the command you pass.

## License
MIT, see `LICENSE`.
