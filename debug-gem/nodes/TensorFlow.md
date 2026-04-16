---
tags: [ai, ml, deep-learning, framework, python]
aliases: [tensorflow, tf, keras]
---
# TensorFlow / Keras

Deep learning framework by Google. Production-focused. Keras is the high-level API.

## Known For These Error Patterns
- [[Offset & Boundary Errors]] — Incompatible shapes, label out of range
- [[Resource Exhaustion Errors]] — OOM when allocating tensor
- [[State & Lifecycle Errors]] — Uninitialized variables (TF1), `@tf.function` tracing issues
- [[Dependency & Import Errors]] — TF1 vs TF2 API confusion, CUDA library not found

## Common Gotchas
- TF2 defaults to eager mode, but `@tf.function` uses graph mode (different behavior)
- Standalone Keras and `tf.keras` diverged — use `tf.keras`
- Mixed precision can cause NaN if loss scaling not configured

## Related
- [[PyTorch]] — alternative framework
- [[CUDA]] — GPU backend
- [[MOC — AI & ML]]

## My Notes

