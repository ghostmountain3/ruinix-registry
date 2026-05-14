# Ruinix Model Registry

This repository hosts the official Ruinix model registry.

It stores metadata only. Model weights are not committed here and are not served by this repository. Ruinix resolves entries from `models.yaml`, then downloads the actual model files from Hugging Face or another configured source.

## Raw URL

Use the raw `models.yaml` URL as the Ruinix registry URL:

```text
https://raw.githubusercontent.com/ghostmountain3/ruinix-registry/main/models.yaml
```

Ruinix uses this URL by default. Users can override it with:

```bash
RUINIX_REGISTRY_URL=https://raw.githubusercontent.com/you/private-registry/main/models.yaml ruinix registry update
ruinix --registry-url https://raw.githubusercontent.com/you/private-registry/main/models.yaml registry list
```

## Files

```text
models.yaml
schemas/models.schema.json
```

`models.yaml` contains curated model metadata. `schemas/models.schema.json` describes the expected shape.

## Add A Model

Add an entry under `models`:

```yaml
- id: qwen2.5-coder:1.5b-mlx-4bit
  display_name: Qwen2.5 Coder 1.5B MLX 4-bit
  description: Compact coding model for Apple Silicon.
  source:
    type: huggingface
    repo: mlx-community/Qwen2.5-Coder-1.5B-Instruct-4bit
    revision: main
  format: mlx
  backend: mlx
  family: qwen2
  architecture: qwen2
  quantization: 4bit
  recommended_tier: compact
  context_window: 4096
  max_output_tokens: 1024
  default_preset: code_precise
  tags:
    - coding
    - apple-silicon
  expected_files:
    - config.json
    - tokenizer.json
    - tokenizer_config.json
    - model.safetensors
```

For GGUF models, include `source.filename` and set `backend: llamacpp`.

## Validate

Validate with a JSON Schema tool:

```bash
python3 -m pip install -U check-jsonschema
check-jsonschema --schemafile schemas/models.schema.json models.yaml
```

You can also validate through Ruinix by pointing a local checkout at this file:

```bash
ruinix pull qwen2.5-coder:1.5b-mlx-4bit --registry ./models.yaml --dry-run
```

## Important Rules

- Do not commit model weights.
- Do not claim a model is runnable unless Ruinix has a compatible backend/adapter.
- Prefer `compatibility: experimental` for new architectures until evals and real runs prove stability.
- Use raw GitHub URLs or static hosting for the registry file. A registry server is not required for v1.
