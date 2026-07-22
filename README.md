# pi-usage

Helpers and a ready-to-use model configuration for the [PI Coding Agent](https://pi.dev/).

The included [`models.json`](models.json) configures PI to use either Google AI Studio or a local [Ollama](https://ollama.com/) server. It is intended to make a practical mix of capable hosted and local coding models available with minimal setup.

## Included configuration

| Provider | Models | Connection |
| --- | --- | --- |
| Google AI Studio | Gemma 4 31B | Google Generative Language API |
| Ollama | Gemma 4 26B, Gemma 4 E2B, Gemma 4 E4B, GPT-OSS 20B | `http://127.0.0.1:11434/v1` |

All configured models support reasoning. Gemma models accept text and images; GPT-OSS 20B is configured for text input.

## Quick start

1. Install the [PI Coding Agent](https://pi.dev/) and, for local models, [Ollama](https://ollama.com/download).
2. Copy the configuration into PI's agent directory:

   ```sh
   mkdir -p ~/.pi/agent
   cp models.json ~/.pi/agent/models.json
   ```

3. Configure at least one provider:
   - For Google AI Studio, replace `<your-gemini-api-key>` in the `_google.apiKey` field of `~/.pi/agent/models.json` with a Gemini API key from [Google AI Studio](https://aistudio.google.com/app/api-keys).
   - For Ollama, pull one or more of the models listed below and ensure the Ollama server is running.
4. Start PI and select an available model.

Do not commit a copy of the configuration containing a personal API key.

## Model guide

| Model | Best fit | Notes |
| --- | --- | --- |
| **Gemma 4 31B** (`gemma-4-31b-it`) | High-quality hosted coding | Available through Google AI Studio; requires a Gemini API key. |
| **Gemma 4 26B** (`gemma4:26b-a4b-it-qat`) | General local coding | Designed to fit in 16 GB VRAM. |
| **GPT-OSS 20B** (`gpt-oss:20b`) | General local coding | Designed to fit in 16 GB VRAM. |
| **Gemma 4 E2B / E4B** (`gemma4:e2b-mxfp8`, `gemma4:e4b-mxfp8`) | Lightweight tool use | Good choices for file reading and web-search tasks. |

Qwen models are intentionally absent because their long reasoning traces are a poor fit for typical local inference speeds.

## Using local models with Ollama

### Pull the models

Pull only the models you plan to use:

```sh
ollama pull gemma4:26b-a4b-it-qat
ollama pull gemma4:e2b-mxfp8
ollama pull gemma4:e4b-mxfp8
ollama pull gpt-oss:20b
```

The names must match the entries in the `ollama.models` array of [`models.json`](models.json). Add or remove entries there as your local model collection changes.

### Start Ollama

If Ollama is not already running as a background service, start its local API server:

```sh
ollama serve
```

PI connects to the OpenAI-compatible endpoint at `http://127.0.0.1:11434/v1`.

### Keep a model loaded

Ollama can unload idle models quickly. Before a PI session, load the model with a longer keepalive period:

```sh
ollama run gemma4:26b-a4b-it-qat --keepalive=30m ""
```

Replace the model ID with the local model you intend to use.

### Set the context length

Each local model in this configuration has a `contextWindow` of `65536`. In Ollama's system-tray menu, open **Settings**, find **Context length**, and set it to `65536` or higher, subject to your hardware's memory limits.

## Troubleshooting

| Symptom | Check |
| --- | --- |
| A local model is unavailable in PI | Run `ollama list`, then confirm its exact ID appears in `ollama.models`. |
| PI cannot reach a local model | Ensure `ollama serve` is running and that `http://127.0.0.1:11434/v1` is reachable. |
| Conversations lose earlier context | Increase Ollama's context length to at least `65536`, if your hardware allows it. |
| Gemma 4 31B cannot authenticate | Verify the `_google.apiKey` value is a valid Gemini API key. |

## Customizing the configuration

Edit [`models.json`](models.json) before copying it, or edit `~/.pi/agent/models.json` afterward. The two provider sections are independent, so you can use Google AI Studio, Ollama, or both. When adding a local model, use its exact Ollama ID and set its capabilities and context window to match the model you have installed.