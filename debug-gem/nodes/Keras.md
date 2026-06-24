---
tags: [ai, ml, deep-learning, framework, python]
aliases: [keras]
---
# Keras

High-level deep learning API. Originally standalone, now the official high-level API of [[TensorFlow]] (`tf.keras`). Also available as standalone Keras 3 supporting multiple backends (TensorFlow, PyTorch, JAX).

## Known For These Error Patterns
- [[Offset & Boundary Errors]] — Input shape mismatch, incompatible layer dimensions, label out of range
- [[Resource Exhaustion Errors]] — OOM during training (batch too large), model too large for GPU
- [[Configuration & Environment Errors]] — Backend misconfiguration, Keras 2 vs 3 API differences, `tf.keras` vs standalone keras import confusion
- [[State & Lifecycle Errors]] — Model not compiled before training, layers not built, wrong training mode

## Common Errors
- `ValueError: Input 0 of layer "X" is incompatible with the layer: expected shape=(None, Y), found shape=(None, Z)` — Input shape doesn't match layer expectation. Check your data preprocessing.
- `ValueError: Shapes (None, 1) and (None, 10) are incompatible` — Output shape mismatch. Usually loss function vs output layer (categorical_crossentropy needs one-hot labels).
- `AttributeError: module 'keras' has no attribute 'X'` — API changed between versions. Check Keras 2 vs 3 migration guide.
- `RuntimeError: You must compile your model before training/testing` — Call `model.compile(optimizer, loss, metrics)` first.
- `ValueError: No gradients provided for any variable` — Model output disconnected from loss. Check model architecture.

## Common Gotchas
- `tf.keras` vs `keras` — in TF2, always use `tf.keras`. Standalone `keras` and `tf.keras` diverged (fixed in Keras 3).
- Loss function + label format — `sparse_categorical_crossentropy` for integer labels, `categorical_crossentropy` for one-hot
- `model.fit()` verbose output — loss going to NaN usually means learning rate too high or data has issues
- Input shape — first dimension is batch size (use `None`). Don't include batch dim in `input_shape`.
- `model.save()` format — `.h5` (legacy) vs `.keras` (new). SavedModel for serving.
- Custom layers need `get_config()` for serialization — otherwise model can't be saved/loaded
- `training` argument in `call()` — controls Dropout/BatchNorm behavior. `model.evaluate()` uses `training=False`.

## Related
- [[TensorFlow]] — Keras is TF's high-level API
- [[PyTorch]] — alternative framework (lower-level)
- [[CUDA]] — GPU backend
- [[MOC — AI & ML]]

## My Notes

