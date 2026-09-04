# Game API Image Generation AI

Self-hosted image-generation service for `game-api.online`.

## What this service does

- Runs a local Diffusers text-to-image model.
- Exposes a Stable-Diffusion-WebUI-compatible endpoint at `POST /sdapi/v1/txt2img`.
- Supports `x-api-key` authentication when `IMAGE_API_KEY` is configured.
- Returns generated PNG images as base64 data URLs.
- Provides `GET /` and `GET /health` endpoints.

## Important Render note

This repository is configured for a CPU Render web service using the `standard` compute plan (1 CPU / 2 GB RAM in Render's current plan mapping). The selected `segmind/tiny-sd` model is about 1.06 GB on Hugging Face, so this is a lightweight prototype and image generation may be slow on CPU. A larger memory-optimized Render plan is recommended if the service runs out of memory. Render currently documents compute plans by CPU/RAM and does not list GPU compute plans in the standard web-service catalog.

## Environment variables

- `MODEL_ID` — default: `segmind/tiny-sd`
- `IMAGE_API_KEY` — shared secret for API authentication
- `DEFAULT_STEPS` — default: `12`
- `MAX_STEPS` — default: `24`
- `DEFAULT_WIDTH` — default: `512`
- `DEFAULT_HEIGHT` — default: `512`
- `MAX_WIDTH` — default: `768`
- `MAX_HEIGHT` — default: `768`

## Local run

```bash
pip install -r requirements.txt
uvicorn app.main:app --host 0.0.0.0 --port 10000
```

## Test

```bash
curl -X POST http://localhost:10000/sdapi/v1/txt2img \
  -H "Content-Type: application/json" \
  -H "x-api-key: YOUR_IMAGE_API_KEY" \
  -d '{"prompt":"a futuristic Game API dashboard","steps":8,"width":512,"height":512}'
```

The first generation loads the model into memory and downloads the model files from Hugging Face if they are not already present.
