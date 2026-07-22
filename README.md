# pi-usage

helpers for [PI Coding Agent](https://pi.dev/)

## models.json

Place the `models.json` file at `~/.pi/agent/models.json`.

> **Note:** The Gemma 4 31B model (`gemma-4-31b-it`) requires a Gemini API key. You can obtain one at https://aistudio.google.com/app/api-keys. Set it as the `apiKey` value in the `_google` provider section.

### Model choice

| Model | Why |
|---|---|
| **Gemma 4 31B** (`gemma-4-31b-it`) | High-quality model available for free via Google AI Studio |
| **Gemma 4 26B** (`gemma4:26b-a4b-it-qat`) | Fits in 16 GB VRAM; good enough for general coding tasks |
| **GPT-OSS 20B** (`gpt-oss:20b`) | Fits in 16 GB VRAM; good enough for general coding tasks |
| **Gemma 4 E2B / E4B** (`gemma4:e2b-mxfp8`, `gemma4:e4b-mxfp8`) | Lightweight and capable for tool-calling tasks such as reading files and web searching |

> **Note:** Qwen models are not included because their reasoning traces are excessively long, which makes them impractical for the slow inference speeds typical of local model setups.

## Using PI with Ollama

The `models.json` file includes an `ollama` provider pre-configured to connect to a local Ollama server at `http://127.0.0.1:11434/v1`.

### 1. Install Ollama

Download and install Ollama from https://ollama.com/download.

### 2. Pull the models

Pull whichever models you want to use locally. The `ollama` provider in `models.json` includes these models by default:

```sh
ollama pull gemma4:26b-a4b-it-qat
ollama pull gemma4:e2b-mxfp8
ollama pull gemma4:e4b-mxfp8
ollama pull gpt-oss:20b
```

You can add or remove models from the `ollama.models` array in `models.json` to match the models you have pulled.

### 3. Start Ollama

Ollama runs as a background service. Start it with:

```sh
ollama serve
```

### 4. Load a model with a higher time limit

By default, Ollama unloads a model from memory after a short idle period. Before using PI, load the model with a longer keepalive so it stays available during your session:

```sh
ollama run $MODEL_ID --keepalive=30m ""
```

Replace `$MODEL_ID` with the model you want to use (e.g. `gemma4:27b`). Once running, PI will connect to it automatically using the configured `baseUrl`.

### 5. Increase the context length

To allow Ollama to handle longer conversations, increase the context length in its settings:

1. Click the Ollama icon in the system tray
2. Click **Settings...**
3. Scroll down to **Context length**
4. Set the desired value (should match or exceed the `contextWindow` value in `models.json`)
