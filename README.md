# 🤖 YASH'S JARVIS

**Think it. Say it. Done.**

A voice-controlled AI desktop assistant for Windows, macOS and Linux. Talk to it, and it opens apps, searches the web, controls your browser, manages files, sets reminders, watches your screen and more, with a holographic animated face on a HUD.

![Python](https://img.shields.io/badge/python-3.11%20–%203.13-blue)
![Platforms](https://img.shields.io/badge/platform-Windows%20%7C%20macOS%20%7C%20Linux-lightgrey)
![License](https://img.shields.io/badge/license-CC%20BY--NC%204.0-green)

<!-- Add a screenshot or GIF of the HUD here:
![JARVIS HUD](docs/screenshot.png)
-->

---

## ✨ Features

**Voice and interface**
- Real-time voice conversation using **Google Gemini Live**
- PyQt6 HUD with an animated holographic face, drawn in software (no GPU needed)
- Optional **push-to-talk** hotkey and an optional local **"Hey Jarvis"** wake word
- Remembers things between sessions (long-term memory, with save and recall)

**Built-in actions**

| Action | What it does |
|---|---|
| `open_app` | Launch applications and report whether they actually opened |
| `web_search` | Search the web and summarise results |
| `browser_control` | Drive a real browser (Playwright): navigate, click, type |
| `computer_control` / `computer_settings` | Mouse, keyboard, volume, brightness, system settings |
| `desktop_control` | Desktop and window tasks |
| `file_controller` / `file_processor` | Find, move and delete files (to the recycle bin); read PDF, DOCX, PPTX and more |
| `screen_process` | Look at your screen or webcam and answer questions about it |
| `system_status` / `manage_monitor` | CPU, RAM, GPU and background monitoring |
| `reminder` | Set reminders |
| `send_message` | Send messages |
| `weather_report`, `flight_finder` | Weather and flight lookups |
| `youtube_video` / `video_player` | Play or summarise YouTube and local video on the HUD |
| `code_helper` / `dev_agent` | Write, explain and run code; scaffold small projects |
| `game_updater` | Help update games |
| `undo` | Reverse the last reversible action |

**Safety**
- **Confirmation the model can't fake:** irreversible actions (shutdown, restart, risky deletes) show a CONFIRM / CANCEL banner in the UI. Only your click approves them.
- Deletes go to the **recycle bin**, and reversible actions can be **undone**.

**Extensibility**
- **Plugins:** drop a `.py` file into `plugins/` and it's picked up on the next launch (see [Writing a plugin](#-writing-a-plugin)).
- **Bring your own models:** in ⚙ → *ENGINE & PROVIDERS*, replace Gemini with local Whisper (speech-to-text), Piper (text-to-speech), and Ollama, LM Studio, llama.cpp, vLLM, Groq, OpenAI, DeepSeek or OpenRouter for the language model.
- **Phone dashboard:** control JARVIS from your phone over your local network (see [Security](#-security)).

---

## 📋 Requirements

- **Python 3.11 – 3.13** (tested up to 3.13; newer versions may work)
- A free **[Gemini API key](https://aistudio.google.com/apikey)**
- A microphone and speakers
- Internet access for Gemini and web actions (not needed for fully local models)
- ~1 GB of free disk space (Python packages plus Playwright browsers)

**Linux:** a few OS actions use native tools. Install the ones you need:
`pulseaudio-utils` (volume), `brightnessctl` (brightness), `systemd` or `at` (reminders), `xdg-utils` (open URLs).

**macOS:** you'll be asked to grant microphone, screen recording and accessibility permissions on first use.

---

## 🚀 Installation

```bash
# 1. Clone
git clone https://github.com/kosuariyaswanth1903-hub/Jarvis-setup-file-.git
cd Jarvis-setup-file-

# 2. Run the one-time setup (installs dependencies and Playwright browsers)
python setup.py        # Windows: py setup.py

# 3. Launch
python main.py
```

On first launch, a **setup screen** asks for your Gemini API key. Paste it in and you're ready to talk.

> Tip: use a virtual environment so the dependencies stay isolated:
> `python -m venv .venv`, then activate it (`.venv\Scripts\activate` on Windows, `source .venv/bin/activate` elsewhere) before step 2.

**Optional extras** (install only if you want them):

```bash
pip install faster-whisper   # local speech-to-text
pip install piper-tts        # local text-to-speech
pip install openwakeword     # "Hey Jarvis" wake word (or enable it from ⚙ → WAKE WORD)
pip install pandas pydub     # spreadsheet analysis / audio conversion
```

---

## 🎙️ Usage

Just talk. Examples:

- *"Open Spotify."*
- *"Search the web for the latest Python release."*
- *"What's the weather in Mumbai tomorrow?"*
- *"Remind me to call mom at 6 pm."*
- *"What's on my screen right now?"*
- *"Move everything in Downloads that's a PDF into Documents."*
- *"Undo that."*

Open the **⚙ settings** panel to change the voice, the assistant's name, push-to-talk key, wake word, and the engine and providers.

---

## 🔌 Writing a plugin

1. Copy `plugins/_template.py` to `plugins/my_plugin.py` (no leading underscore).
2. Fill in the `PLUGIN` dict (`name`, `description`, `parameters`) and the `run()` function.
3. Restart JARVIS. It's discovered automatically and can be toggled in settings.

If your plugin needs a model, use `core.gemini` (`gemini.text(...)`, `gemini.as_json(...)`) instead of creating your own client. You get timeouts, model fallback and your configured provider for free.

> ⚠️ Plugins run with **full access to your computer**. Only install plugins whose code you've read and trust.

---

## 🔒 Security

JARVIS can control your computer, so treat it carefully.

- **API keys and personal data stay local.** `config/api_keys.json`, `config/certs/`, WhatsApp sessions, OAuth tokens and `memory/long_term.json` are in `.gitignore`. **Never commit or share them.**
- **Phone dashboard:** it serves HTTPS on port **8001** (port 8000 redirects to it) with a self-signed certificate and an extra AES-256-GCM layer keyed from a pairing code. It listens on your whole local network, so only use it on a **trusted network**, never on public Wi-Fi, and don't expose these ports to the internet.
- Keep the pairing code private. Use the *revoke devices* option if a phone is lost.
- Review any plugin before installing it.
- Irreversible actions ask for confirmation in the UI. Read the banner before you click CONFIRM.

---

## 🗂️ Project structure

```
main.py            Entry point: Gemini Live session, tool dispatch
ui.py              PyQt6 HUD and settings
setup.py           One-time dependency installer
core/              Prompt, confirmation gate, undo, wake word, audio, avatar, plugin loader
actions/           Built-in tools (each describes itself with a TOOL dict)
plugins/           Drop-in community plugins (+ _template.py)
providers/         Optional local/alternative STT, LLM and TTS engines
dashboard/         Phone remote (FastAPI, TLS + AES-GCM)
memory/            Config and long-term memory
```

---

## 🛠️ Troubleshooting

| Problem | Fix |
|---|---|
| `Python x.y detected, needs at least 3.11` | Install Python 3.11–3.13 and run `py -3.12 setup.py` (Windows) or `python3.12 setup.py` |
| Browser actions fail | `python -m playwright install chromium firefox` |
| No sound or microphone | Check your OS default devices and permissions, then restart |
| Windows: `pywin32` warning | `pip install --force-reinstall pywin32`, then run `pywin32_postinstall.py -install` |
| Dashboard unreachable from phone | Allow the ports through your firewall and make sure both devices are on the same network |
| A package has no wheel for your Python | Use Python 3.12 or 3.13 |

---

## 🤝 Contributing

Issues and pull requests are welcome. Please don't include API keys, tokens or personal data in issues or commits.

---

## 📄 License and credits

Released under **[CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)**. You may share and adapt it with credit, **for non-commercial use only**. See [`LICENSE`](LICENSE).

- **Developer:** Yaswanth 
- **Original work:** FatihMakes. This project builds on and modifies their original work. Changes were made, and the original creator does not endorse this project.

Provided as-is, without warranty. You are responsible for how you use an assistant that can control your computer.
