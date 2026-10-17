# Morvarid generative image tools

This branch adds optional OpenAI-powered image editing to the native PhotoCraft desktop app.

## Available commands

- **Edit › Generative Fill** repaints the current selection from a prompt and places the result on a new layer.
- **Image › Expand Canvas** grows every edge by the requested pixel count, generates the missing border, and blends the seam on a new layer.
- **Image › Generate Image** creates a new generated image.
- **Layer › Variations** creates a prompt-guided variation from the visible composite or active layer.

All changes are non-destructive and appear as a single undoable engine command. Fill and Expand support RGB, grayscale, CMYK and Lab documents at 8-, 16- and 32-bit depth through the existing colour-management path.

## API setup

Create an OpenAI API key with image-generation access, then expose it only to the PhotoCraft process:

```sh
OPENAI_API_KEY='your-key' cargo run -p photocraft
```

PowerShell:

```powershell
$env:OPENAI_API_KEY = 'your-key'
cargo run -p photocraft
```

The key is read from process memory, is not stored in a PhotoCraft document or preferences file, and is excluded from errors. Source images, masks, and prompts are sent to the OpenAI Images API when a generative command runs. API usage is billed by OpenAI separately from ChatGPT subscriptions.

The default model is `gpt-image-2.5-sunburst`; automation callers may pass another valid `gpt-image*` model in the command parameters. The web/Wasm build keeps compiling but returns a clear desktop-only error because a browser must not receive a secret API key.

## Limits

- Prompts: 32,000 bytes maximum.
- Inputs: 4,096 pixels per side and 16 megapixels maximum.
- Uploaded PNG image or mask: 20 MiB maximum.
- Returned response: 32 MiB maximum.
- HTTPS requests time out after 180 seconds and do not follow redirects.

The generated mask is guidance to the model; the engine clips and composites the returned pixels to keep edits inside the intended region.
