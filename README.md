# pi-usage

Helpers and a ready-to-use model configuration for the [PI Coding Agent](https://pi.dev/).

The included [`models.json`](models.json) configures PI to use a local [Ollama](https://ollama.com/) server. It is intended to make a practical mix of capable local coding models available with minimal setup.

## Included configuration

The included [`models.json`](models.json) configures PI's Ollama provider for the OpenAI-compatible endpoint at `http://127.0.0.1:11434/v1`.

| Model | Ollama ID | Input | Thinking levels |
| --- | --- | --- | --- |
| GPT-OSS 20B | `gpt-oss:20b` | Text | Low, medium, high |
| Gemma 4 26B | `gemma4:26b-a4b-it-qat` | Text, images | Off, high |
| Qwen 3.6 35B | `qwen3.6:35b-a3b` | Text, images | Off, max |

All three models use a [`65536` context window](#set-the-context-length) and support reasoning. The listed thinking levels are the only levels PI exposes for each model.

## Quick start

1. Install the [PI Coding Agent](https://pi.dev/) and [Ollama](https://ollama.com/download).
2. Copy the configuration and system prompt into PI's agent directory:

   ```sh
   mkdir -p ~/.pi/agent
   cp models.json ~/.pi/agent/models.json
   cp SYSTEM.example.md ~/.pi/agent/SYSTEM.md
   ```

3. Pull one or more of the models listed below and ensure the Ollama server is running.
4. Start PI and select an available model.

Do not commit a copy of the configuration containing a personal API key.

## Custom System Prompt

The included [`SYSTEM.example.md`](SYSTEM.example.md) provides a clean, concise custom system prompt for PI. Copy it to `~/.pi/agent/SYSTEM.md` to replace the default system prompt and keep the model context clean and focused.

## Extensions

Install these extensions with the PI CLI:

```sh
pi install git:github.com/lamartinecabral/pi-web-search
pi install git:github.com/lamartinecabral/pi-all-tools
```

- [`pi-web-search`](https://github.com/lamartinecabral/pi-web-search) adds `web_search` and `web_fetch` tools. It requires a compatible local browser; set `CHROME_PATH` if PI cannot locate one automatically.
- [`pi-all-tools`](https://github.com/lamartinecabral/pi-all-tools) enables PI's built-in `read`, `write`, `edit`, `bash`, `ls`, `find`, and `grep` tools by default.

## Model guide

| Model | Memory usage |
| --- | --- |
| **GPT-OSS 20B** (`gpt-oss:20b`) | 12 GB |
| **Gemma 4 26B** (`gemma4:26b-a4b-it-qat`) | 16 GB |
| **Qwen 3.6 35B** (`qwen3.6:35b-a3b`) | 25 GB |

Memory use varies by hardware and Ollama settings; the figures above are approximate requirements for this configuration's `65536` context window.

### Thinking levels

`thinkingLevelMap` controls which reasoning levels PI presents in its menu and how they map to the model's supported values. A `null` value removes that level from PI's menu. The configuration only exposes levels that produce coherent behavior for each model, even when Ollama accepts additional values that the model does not meaningfully honor.

- **Qwen 3.6 35B:** Supports reasoning off, but every other level produces extremely long reasoning. The configuration therefore keeps only `off` and `max`.
- **GPT-OSS 20B:** Has distinct behavior at `low`, `medium`, and `high`, but cannot turn reasoning off.
- **Gemma 4 26B:** Can turn reasoning off, but all other levels behave the same, at a level comparable to GPT-OSS 20B's `high`. The configuration therefore keeps only `off` and `high`.

## Using local models with Ollama

### Pull the models

Pull only the models you plan to use:

```sh
ollama pull gpt-oss:20b
ollama pull gemma4:26b-a4b-it-qat
ollama pull qwen3.6:35b-a3b
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

## Customizing the configuration

Edit [`models.json`](models.json) before copying it, or edit `~/.pi/agent/models.json` afterward. When adding a local model, use its exact Ollama ID and set its capabilities and context window to match the model you have installed.
