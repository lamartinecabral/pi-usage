# pi-usage

helpers for [PI Coding Agent](https://pi.dev/)

## models.json

Place the `models.json` file at `~/.pi/agent/models.json`.

> **Note:** The Gemma 4 31B model (`gemma-4-31b-it`) requires a Gemini API key. You can obtain one at https://aistudio.google.com/app/api-keys. Set it as the `apiKey` value in the `_google` provider section.

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
