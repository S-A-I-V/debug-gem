---
tags: [ai, ml, deep-learning, framework, python]
aliases: [pytorch, torch]
---
# PyTorch

Deep learning framework by Meta. Dynamic computation graph, Pythonic API. Dominant in research.

## Known For These Error Patterns
- [[Offset & Boundary Errors]] — Tensor shape mismatches, matrix dimension errors
- [[Type & Casting Errors]] — Dtype mismatches (Float vs Double vs Long)
- [[State & Lifecycle Errors]] — In-place operations breaking autograd, forgetting `model.eval()`/`optimizer.zero_grad()`
- [[Resource Exhaustion Errors]] — CUDA OOM, DataLoader worker killed

## Critical Checklist (Training)
- [ ] `model.train()` before training, `model.eval()` before inference
- [ ] `optimizer.zero_grad()` before each batch
- [ ] `loss.backward()` to compute gradients
- [ ] `optimizer.step()` to update weights
- [ ] `torch.no_grad()` for inference (saves memory)

## Related
- [[CUDA]] — GPU backend
- [[HuggingFace]] — model hub and libraries
- [[TensorFlow]] — alternative framework
- [[MOC — AI & ML]]

## My Notes

