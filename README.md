<div align="center">

### The Council

</div>

<div align="center">

[![HTML](https://img.shields.io/badge/HTML-%23E34F26.svg?logo=html5&logoColor=white)](#) [![CSS](https://img.shields.io/badge/CSS-639?logo=css&logoColor=fff)](#) [![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript&logoColor=000)](#) [![Hugging Face](https://img.shields.io/badge/Hugging%20Face-FFD21E?logo=huggingface&logoColor=000)](#)

</div>

---

### Overview

The Council is a brutalist, WhatsApp-style group chat where you ask one question and four characters reply, each fully in character and none of them agreeing. A slot-machine Randomize deals a new squad from a cast of 48 (philosophers, desi relatives, a 1% battery, a mosquito, a haunted doll…), and every reply can be played aloud in a voice cast for that character, layered with effects and background sounds. Replies come from an LLM behind a Cloudflare Worker, the background music follows the mood of your question, and any round can be exported as a 1080×1920 story image.

---

### Demo

Live Demo: [shauryachopra.dev/thecouncil](https://shauryachopra.dev/thecouncil)

---

### Project Structure

```
the-council/
├── index.html   # the entire front end: UI, 48-member roster, music + sound engine, voice playback, story export
├── worker.js    # Cloudflare Worker: council replies, emotion detection, text-to-speech routing
└── README.md
```

---

### Tech Stack

- **Front end:** a single HTML file with no build step: vanilla JavaScript, CSS (Archivo + Space Mono fonts)
- **Audio:** [Tone.js](https://tonejs.github.io/) for the mood-based music, sound effects, character stings and real-time voice effects (ghost, robot, gramophone, tannoy…)
- **Story export:** [html2canvas](https://html2canvas.hertzen.com/) renders the chat as a 1080×1920 PNG
- **Replies:** Groq chat completions (default model `openai/gpt-oss-20b`)
- **Mood detection:** Hugging Face Inference API (`j-hartmann/emotion-english-distilroberta-base`)
- **Voices (with automatic fallbacks):**
  - English: Azure AI Speech (styled neural voices), then Cloudflare Workers AI Deepgram Aura-2, then Sarvam Bulbul v3 in Indian English, then Aura-1
  - Hindi / Hinglish: Sarvam Bulbul v3, with romanised Hindi transliterated to Devanagari first
  - Browser speech synthesis as a last resort

---

### Setup

The front end is static, so just open or host `index.html`. The worker needs deploying once:

1. Create a Cloudflare Worker and paste in `worker.js`.
2. Add the secrets and binding listed under Configuration.
3. Deploy, then set `WORKER_URL` near the top of the script in `index.html` to your worker's URL.

To run the front end locally, serve the folder with any static server, for example:

```bash
npx live-server
```

---

### Usage

1. Type a question in the composer and press **Send** (or Enter).
2. Hit **Randomize** to spin a new squad of four.
3. Press the speaker button on any reply, or **Play the chat**, to hear the voices.
4. Press **Screenshot this chat** to download a story-sized image of the round.

For debugging, run these in the browser console:

```js
councilVoiceCheck()                     // which engine and voice actually speaks for a few members
councilVoice('A Pirate Captain', 'Arr') // hear any member's voice, effect and background
councilSoundTest()                      // beep + air horn, plus the audio state
```

---

### Configuration

Worker secrets, variables and bindings (set in the Cloudflare dashboard under Settings):

| Name | Type | Purpose |
|---|---|---|
| `GROQ_API_KEY` | Secret | Generates the council's replies |
| `GROQ_MODEL` | Variable (optional) | Overrides the default `openai/gpt-oss-20b` |
| `HF_TOKEN` | Secret | Emotion detection that picks the background music |
| `AI` | Workers AI binding | Deepgram Aura-2 / Aura-1 English voices |
| `AZURE_SPEECH_KEY` | Secret | Azure styled English voices (first choice) |
| `AZURE_SPEECH_REGION` | Variable | Azure region code, e.g. `centralindia` |
| `SARVAM_KEY` | Secret | Hindi voices, transliteration and the English fallback |

Every voice provider is optional. Whatever isn't configured is skipped, and the chain falls through to the next one.

In `index.html`:

- `WORKER_URL`: your deployed worker
- `SITE_URL`: the link printed on the story image
- `level` in `state`: the fixed tone sent to the worker (1 sane … 5 feral, default 3)

---

<div align="center">

Built by [Shaurya Chopra](https://shauryachopra.dev/)

</div>
