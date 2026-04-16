---
tags: [ai, ml, nlp, generative-ai]
aliases: [llm, large-language-model, gpt, claude, gemini]
---
# LLM (Large Language Model)

AI models that generate text. GPT, Claude, Gemini, Llama, Mistral. Used for chat, code generation, summarization, reasoning.

## Known For These Error Patterns
- [[Resource Exhaustion Errors]] — Context length exceeded, rate limits, token limits
- [[The Silent Failure]] — Hallucination (plausible but wrong output)
- [[Serialization & Encoding Errors]] — JSON parse error on LLM output
- [[Permission & Authorization Errors]] — API key issues, content filter rejections
- [[Connection & Network Errors]] — API timeouts, provider outages

## Common Gotchas
- Hallucination — model generates false information confidently. Mitigate with RAG, grounding, validation.
- Non-deterministic — same prompt can give different outputs. Use `temperature=0` and `seed` for reproducibility.
- Context window limits — input + output tokens must fit. Truncate or chunk.
- Prompt injection — user input can override system instructions. Sanitize inputs.

## Related
- [[HuggingFace]] — model hub
- [[PyTorch]], [[TensorFlow]] — training frameworks
- [[MOC — AI & ML]]

## My Notes

