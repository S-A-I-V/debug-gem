---
tags: [ai, ml, platform, models]
aliases: [huggingface, hf, transformers]
---
# HuggingFace

AI model hub and library ecosystem. Hosts models, datasets, and spaces. `transformers` library for model inference and fine-tuning.

## Known For These Error Patterns
- [[Dependency & Import Errors]] — Model name typo, gated model without auth, missing optional deps
- [[Offset & Boundary Errors]] — Tokenizer/model mismatch, token ID exceeds vocabulary
- [[Resource Exhaustion Errors]] — Model too large for GPU (use quantization, `device_map="auto"`)
- [[Configuration & Environment Errors]] — Wrong model path, `trust_remote_code` requirement

## Common Gotchas
- `trust_remote_code=True` executes arbitrary Python — security risk
- `max_length` vs `max_new_tokens` — total length vs output-only length
- Always load tokenizer and model from same checkpoint
- Padding side matters — some models need left padding (decoder-only)

## Related
- [[LLM]], [[PyTorch]], [[TensorFlow]] — underlying tech
- [[MOC — AI & ML]]

## My Notes

