---
tags: [gpu, ai, ml, nvidia, compute]
aliases: [cuda, nvidia-cuda]
---
# CUDA

NVIDIA's parallel computing platform for GPU programming. Foundation for all GPU-accelerated deep learning.

## Known For These Error Patterns
- [[Resource Exhaustion Errors]] — `CUDA out of memory` (the #1 ML error)
- [[Offset & Boundary Errors]] — Illegal memory access, device-side assert (usually label index >= num_classes)
- [[Configuration & Environment Errors]] — CUDA version vs GPU architecture mismatch, device mismatch (CPU vs GPU tensors)

## Key Concept
GPU memory is separate from CPU memory. `torch.cuda.empty_cache()` releases cached memory but doesn't free tensors — delete unused tensors first.

## Debug
- `nvidia-smi` — GPU status, memory usage, processes
- `CUDA_LAUNCH_BLOCKING=1` — synchronous execution for accurate error stack traces
- `NCCL_DEBUG=INFO` — multi-GPU communication debugging

## Related
- [[PyTorch]], [[TensorFlow]] — ML frameworks using CUDA
- [[C++]] — CUDA extends C++
- [[MOC — AI & ML]]

## My Notes

