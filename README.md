# Voice AI Assistant (Asterisk + Gemini)

A self-hosted AI voice assistant over a real phone call. Built with Asterisk PBX, FastAGI (Python), Google Gemini 2.5 Flash, and gTTS. Supports Russian and English — user selects language via DTMF during the call.

**Stack:** Asterisk · Python · Docker Compose · Gemini API · gTTS · SpeechRecognition · ffmpeg

---

## How it works

1. You call extension **9000** via any SIP client (tested with Zoiper)
2. Asterisk plays a bilingual menu: press `1` for English, `2` for Russian
3. After selection, the AI listens to your speech, transcribes it via Google STT, sends it to Gemini 2.5 Flash, and plays back the response as audio
4. Conversation loops until you hang up

```
Zoiper (SIP) → Asterisk (port 5060) → FastAGI (port 4573) → Python app
                                                                ├── Google STT
                                                                ├── Gemini 2.5 Flash
                                                                └── gTTS → ffmpeg → Asterisk → your ear
```

---

## Requirements

- Docker + Docker Compose
- A Google Gemini API key → [get one here](https://aistudio.google.com/app/apikey)
- A SIP softphone (e.g. [Zoiper](https://www.zoiper.com/en/voip-softphone/download/current))

---

## Quick Start

### 1. Clone the repo

```bash
git clone https://github.com/Tricksteridze/asterisk-gemini-voice
cd voice-ai-asterisk
```

### 2. Set your API key

```bash
cp .env.example .env
# Edit .env and paste your Gemini API key
```

### 3. Start the containers

```bash
docker compose up --build
```

Both containers use `network_mode: host`, so no port mapping needed.

### 4. Configure Zoiper

| Field    | Value       |
|----------|-------------|
| Username | `1001`      |
| Password | `password123` |
| Domain   | `127.0.0.1` |
| Port     | `5060`      |
| Protocol | `SIP (UDP)` |

### 5. Make a call

Dial **9000** in Zoiper. You'll hear the language menu — press `1` or `2`, then start talking.

---

## Project Structure

```
voice-ai-asterisk/
├── app/
│   ├── main.py          # FastAGI server + call logic
│   └── Dockerfile
├── config/              # Asterisk config files
│   ├── pjsip.conf       # SIP endpoint: extension 1001
│   └── extensions.conf  # Dialplan: extension 9000 → AGI
├── records/             # Shared volume for audio files (auto-populated)
├── docker-compose.yml
├── .env.example
└── README.md
```

---

## Environment Variables

| Variable         | Description              |
|------------------|--------------------------|
| `GEMINI_API_KEY` | Your Google Gemini API key |

---

## Planned improvements

- [ ] Reduce latency (streaming STT / TTS)
- [ ] PDF as a knowledge base for the AI
- [ ] More languages
- [ ] Persistent conversation context per caller

---

## License

MIT
