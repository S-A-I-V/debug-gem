# MOC — AI & ML

> Error patterns specific to machine learning, deep learning, LLMs, and AI systems. This domain is unique because many errors are semantic (wrong output, not crash) and debugging requires understanding both code AND math.

---

## GPU & [[CUDA]] Errors

### Memory Errors
- `CUDA error: out of memory` / `torch.cuda.OutOfMemoryError` → [[Resource Exhaustion Errors]]: GPU VRAM full.
  - **Fix**: Reduce batch size, use gradient accumulation, enable mixed precision (`torch.cuda.amp`), use gradient checkpointing, use `torch.no_grad()` for inference, clear cache with `torch.cuda.empty_cache()`.
  - **Note**: `empty_cache()` only releases cached memory back to CUDA, doesn't free tensors. Delete unused tensors first.
- `RuntimeError: CUDA error: an illegal memory access was encountered` → [[Offset & Boundary Errors]]: Kernel accessed invalid GPU memory.
  - **Common cause**: Label index >= num_classes in loss function. Check your labels.
  - **Debug**: Set `CUDA_LAUNCH_BLOCKING=1` to get accurate stack trace (GPU errors are async by default).
- `RuntimeError: CUDA error: device-side assert triggered` → [[Offset & Boundary Errors]]: CUDA kernel assertion failed.
  - **Common cause**: Same as above — index out of bounds in GPU kernel. Often from `nn.CrossEntropyLoss` with invalid target values.
  - **Fix**: Check that all target labels are in range `[0, num_classes)`. Check for negative indices.
- `RuntimeError: CUDA error: unspecified launch failure` → Various: Generic CUDA kernel failure. Set `CUDA_LAUNCH_BLOCKING=1` for details.
- `RuntimeError: CUDA error: no kernel image is available for execution on the device` → [[Configuration & Environment Errors]]: CUDA toolkit version doesn't support your GPU architecture.
  - **Fix**: Install CUDA version that supports your GPU compute capability. Check NVIDIA compatibility matrix.
- `RuntimeError: CUDA error: CUBLAS_STATUS_ALLOC_FAILED` → [[Resource Exhaustion Errors]]: cuBLAS can't allocate workspace memory.
- `RuntimeError: CUDA error: CUDNN_STATUS_INTERNAL_ERROR` → Various: cuDNN internal error. Often memory-related.
  - **Debug**: Try `torch.backends.cudnn.enabled = False` to isolate.
- `RuntimeError: CUDA error: NCCL error` → [[Connection & Network Errors]]: Multi-GPU communication failure.
  - **Debug**: Set `NCCL_DEBUG=INFO` for detailed logs. Check GPU interconnect (NVLink, PCIe).
  - **Common cause**: Mismatched tensor sizes across GPUs, network issues in distributed training.

### Device Errors
- `RuntimeError: Expected all tensors to be on the same device, but found at least two devices, cuda:0 and cpu` → [[Configuration & Environment Errors]]: Tensor on wrong device.
  - **Fix**: Use `.to(device)` consistently. Check model AND data are on same device.
- `RuntimeError: Input type (torch.cuda.FloatTensor) and weight type (torch.FloatTensor) should be the same` → [[Configuration & Environment Errors]]: Model on CPU, data on GPU (or vice versa).
- `RuntimeError: CUDA is not available` → [[Configuration & Environment Errors]]: CUDA not installed, wrong PyTorch build, or no GPU.
  - **Check**: `torch.cuda.is_available()`, `nvidia-smi`, PyTorch CUDA version vs system CUDA version.

---

## [[PyTorch]]

### Tensor Errors
- `RuntimeError: The size of tensor a (X) must match the size of tensor b (Y) at non-singleton dimension Z` → [[Offset & Boundary Errors]]: Shape mismatch in operation.
  - **Debug**: Print `.shape` of all tensors involved. Check broadcasting rules.
- `RuntimeError: mat1 and mat2 shapes cannot be multiplied (AxB and CxD)` → [[Offset & Boundary Errors]]: Matrix multiplication dimension mismatch.
  - **Fix**: B must equal C. Check layer input/output dimensions.
- `RuntimeError: expected scalar type Float but found Double` → [[Type & Casting Errors]]: Tensor dtype mismatch.
  - **Fix**: Use `.float()` or `.double()` to convert. Most models expect float32.
- `RuntimeError: expected scalar type Long but found Float` → [[Type & Casting Errors]]: Loss function expects integer labels.
  - **Fix**: Use `.long()` for classification targets. `CrossEntropyLoss` expects `Long` targets.
- `RuntimeError: Boolean value of Tensor with more than one element is ambiguous` → [[Type & Casting Errors]]: Using tensor in `if` statement.
  - **Fix**: Use `.any()`, `.all()`, or `.item()` for single-element tensors.
- `RuntimeError: one of the variables needed for gradient computation has been modified by an inplace operation` → [[State & Lifecycle Errors]]: In-place operation broke autograd graph.
  - **Fix**: Avoid in-place operations (`x += 1` → `x = x + 1`, `x.add_(1)` → `x = x.add(1)`).
- `RuntimeError: Trying to backward through the graph a second time` → [[State & Lifecycle Errors]]: Need `retain_graph=True` or restructure computation.
- `RuntimeError: element 0 of tensors does not require grad and does not have a grad_fn` → [[State & Lifecycle Errors]]: Tensor not part of computation graph.
  - **Fix**: Ensure `requires_grad=True` on parameters. Check that loss is computed from model output.
- `UserWarning: Using a target size that is different to the input size` → [[Offset & Boundary Errors]]: Loss function input/target shape mismatch.

### Training Errors
- **Loss is NaN** → [[The Silent Failure]]: Numerical instability.
  - **Causes**: Learning rate too high, division by zero, log of zero, exploding gradients.
  - **Fix**: Reduce learning rate, use gradient clipping (`torch.nn.utils.clip_grad_norm_`), check for zero/negative values before log, use `torch.nan_to_num()`.
- **Loss not decreasing** → [[The Silent Failure]]: Model not learning.
  - **Causes**: Learning rate too low/high, wrong loss function, data not shuffled, labels wrong, model too small, bug in data pipeline.
  - **Debug**: Overfit on single batch first. If it can't overfit one batch, there's a bug.
- **Loss oscillating wildly** → Learning rate too high. Reduce by 10x.
- **Validation loss increasing while training loss decreasing** → Overfitting. Add regularization (dropout, weight decay, data augmentation, early stopping).
- `RuntimeError: DataLoader worker is killed by signal: Killed` → [[Resource Exhaustion Errors]]: Worker process OOM killed.
  - **Fix**: Reduce `num_workers`, reduce batch size, or increase system memory.
- `RuntimeError: DataLoader worker exited unexpectedly` → Various: Check worker error. Often memory or file descriptor issue.
- `BrokenPipeError` in DataLoader → [[Connection & Network Errors]]: Worker process died. Common on Windows with `num_workers > 0`.

### Model Errors
- `RuntimeError: Error(s) in loading state_dict` → [[Serialization & Encoding Errors]]: Model architecture doesn't match saved weights.
  - `Missing key(s)`: Model has layers not in checkpoint.
  - `Unexpected key(s)`: Checkpoint has layers not in model.
  - `size mismatch`: Layer exists but shape differs.
  - **Fix**: Use `strict=False` to load partial weights. Check model architecture matches.
- Forgetting `model.eval()` for inference → [[State & Lifecycle Errors]]: Dropout and BatchNorm behave differently in train vs eval mode.
- Forgetting `model.train()` after eval → [[State & Lifecycle Errors]]: Model stuck in eval mode during training.
- Forgetting `optimizer.zero_grad()` → [[State & Lifecycle Errors]]: Gradients accumulate across batches (sometimes intentional for gradient accumulation).
- Forgetting `torch.no_grad()` for inference → [[Resource Exhaustion Errors]]: Autograd graph built unnecessarily, wasting memory.
- Forgetting `loss.backward()` → [[State & Lifecycle Errors]]: Gradients never computed. Weights never update.
- Forgetting `optimizer.step()` → [[State & Lifecycle Errors]]: Gradients computed but weights never updated.

---

## [[TensorFlow]] / [[Keras]]

### Common Errors
- `InvalidArgumentError: Incompatible shapes` → [[Offset & Boundary Errors]]: Tensor shape mismatch.
- `InvalidArgumentError: logits and labels must have the same first dimension` → [[Offset & Boundary Errors]]: Batch size mismatch between predictions and labels.
- `InvalidArgumentError: Received a label value of X which is outside the valid range of [0, Y)` → [[Offset & Boundary Errors]]: Label index out of range.
- `ResourceExhaustedError: OOM when allocating tensor` → [[Resource Exhaustion Errors]]: GPU memory full.
- `FailedPreconditionError: Attempting to use uninitialized value` → [[State & Lifecycle Errors]]: Variable not initialized (TF1 issue).
- `ValueError: Input 0 of layer "X" is incompatible with the layer` → [[Offset & Boundary Errors]]: Input shape doesn't match layer expectation.
- `ValueError: Shapes (X,) and (Y,) are incompatible` → [[Offset & Boundary Errors]]: Shape mismatch.
- `ValueError: No gradients provided for any variable` → [[State & Lifecycle Errors]]: Loss not connected to trainable variables.
- `AttributeError: module 'tensorflow' has no attribute 'X'` → [[Dependency & Import Errors]]: TF1 vs TF2 API confusion. Use `tf.compat.v1.X` for TF1 APIs.
- `ModuleNotFoundError: No module named 'tensorflow'` → [[Dependency & Import Errors]]: TensorFlow not installed.
- `Could not load dynamic library 'libcudart.so'` → [[Dependency & Import Errors]]: CUDA runtime not found.
- `SavedModel file does not exist` → [[Configuration & Environment Errors]]: Wrong model path.
- `OpError: Could not find valid device for node` → [[Configuration & Environment Errors]]: Operation not supported on available devices.

### TF-Specific Traps
- **Eager vs Graph mode**: TF2 defaults to eager. Some operations behave differently in graph mode (`@tf.function`).
- **`@tf.function` tracing**: First call traces the function. Subsequent calls with different input shapes/types retrace. Can cause unexpected behavior.
- **TF1 vs TF2**: Massive API changes. `tf.Session`, `tf.placeholder` are TF1. Use `tf.compat.v1` for migration.
- **Keras vs tf.keras**: Standalone Keras and `tf.keras` diverged. Use `tf.keras` with TensorFlow.
- **Mixed precision**: `tf.keras.mixed_precision.set_global_policy('mixed_float16')` — can cause NaN if loss scaling not configured.

---

## [[LLM]] Specific Errors

### API Errors
- `RateLimitError` / `429 Too Many Requests` → [[Resource Exhaustion Errors]]: API rate limit hit.
  - **Fix**: Implement exponential backoff with jitter. Use a queue. Batch requests. Cache responses.
- `ContextLengthExceeded` / `InvalidRequestError: max tokens` → [[Resource Exhaustion Errors]]: Input + output tokens exceed model's context window.
  - **Fix**: Truncate input, use chunking/summarization, use a model with larger context window.
- `AuthenticationError` / `401` → [[Permission & Authorization Errors]]: Invalid API key.
- `InternalServerError` / `500` → [[Connection & Network Errors]]: Provider-side error. Retry with backoff.
- `ServiceUnavailableError` / `503` → [[Connection & Network Errors]]: Provider overloaded. Retry.
- `Timeout` → [[Connection & Network Errors]]: Response took too long. Increase timeout, reduce input size, use streaming.
- `BadRequestError: Invalid model` → [[Configuration & Environment Errors]]: Model name wrong or not available.
- `ContentFilterError` → [[Permission & Authorization Errors]]: Content policy violation. Rephrase prompt.

### Output Errors
- **Hallucination** → [[The Silent Failure]]: Model generates plausible but factually incorrect output.
  - **Mitigate**: Use RAG (Retrieval Augmented Generation), grounding with sources, fact-checking, confidence scores, human review.
  - **Detect**: Cross-reference with known data, use self-consistency (ask multiple times), use a separate verification model.
- **JSON parse error on LLM output** → [[Serialization & Encoding Errors]]: Model didn't follow format instructions.
  - **Fix**: Use structured output / function calling / tool use. Use JSON mode if available. Add explicit format instructions. Parse with error recovery.
- **Truncated output** → [[Resource Exhaustion Errors]]: `max_tokens` too low. Increase limit.
- **Repetitive output** → [[The Silent Failure]]: Model stuck in a loop.
  - **Fix**: Increase `temperature`, add `frequency_penalty`, use `presence_penalty`.
- **Refusal to answer** → [[Permission & Authorization Errors]]: Safety filter triggered. Rephrase prompt.
- **Wrong language** → [[Configuration & Environment Errors]]: Add explicit language instruction in prompt.
- **Inconsistent output** → [[Concurrency & Race Condition Errors]]: Non-deterministic by nature. Use `temperature=0` and `seed` parameter for reproducibility.

### RAG Errors
- **Retrieval returns irrelevant documents** → [[The Silent Failure]]: Embedding model or chunking strategy not optimal.
  - **Fix**: Tune chunk size, overlap, embedding model. Use hybrid search (semantic + keyword). Add metadata filtering.
- **Context window overflow with retrieved docs** → [[Resource Exhaustion Errors]]: Too many documents retrieved.
  - **Fix**: Limit `top_k`, use reranking to select most relevant, summarize retrieved docs.
- **Stale knowledge base** → [[State & Lifecycle Errors]]: Documents not re-indexed after update.
  - **Fix**: Implement incremental indexing, track document versions.

### Fine-tuning Errors
- **Catastrophic forgetting** → [[The Silent Failure]]: Model loses general capabilities after fine-tuning.
  - **Fix**: Use lower learning rate, fewer epochs, LoRA/QLoRA instead of full fine-tuning.
- **Training data format error** → [[Serialization & Encoding Errors]]: JSONL format wrong, missing required fields.
- **Overfitting to fine-tuning data** → [[The Silent Failure]]: Model memorizes training examples.
  - **Fix**: More diverse training data, fewer epochs, validation set monitoring.

---

## [[HuggingFace]]

### Common Errors
- `OSError: Can't load tokenizer for 'X'` → [[Dependency & Import Errors]]: Model name typo, gated model without auth, or network issue.
  - **Fix**: Check model name on HuggingFace Hub. For gated models: `huggingface-cli login`.
- `OSError: X does not appear to have a file named config.json` → [[Configuration & Environment Errors]]: Not a valid model repository.
- `RuntimeError: The size of tensor a (X) must match the size of tensor b (Y)` → [[Offset & Boundary Errors]]: Tokenizer/model mismatch.
  - **Fix**: Ensure tokenizer and model are from the same checkpoint.
- `ValueError: Tokenizer class X does not exist or is not currently imported` → [[Dependency & Import Errors]]: Need `trust_remote_code=True` or install specific library.
- `OutOfMemoryError` when loading model → [[Resource Exhaustion Errors]]: Model too large for GPU.
  - **Fix**: Use `device_map="auto"` for model parallelism, `load_in_8bit=True` or `load_in_4bit=True` for quantization, `torch_dtype=torch.float16`.
- `ImportError: X requires the Y library` → [[Dependency & Import Errors]]: Optional dependency not installed.
- `ValueError: Text input must be of type str, but got type list` → [[Type & Casting Errors]]: Wrong input format for tokenizer.
- `IndexError: index out of range in self` → [[Offset & Boundary Errors]]: Token ID exceeds vocabulary size. Tokenizer/model mismatch.

### HuggingFace Traps
- **`trust_remote_code=True`**: Security risk. Executes arbitrary Python from the model repo. Only use for trusted models.
- **Tokenizer padding side**: Some models need left padding (decoder-only), some need right padding (encoder). Check model docs.
- **`max_length` vs `max_new_tokens`**: `max_length` is total length (input + output). `max_new_tokens` is output only. Confusion causes truncation.
- **Pipeline vs manual inference**: Pipelines are convenient but hide details. For production, use manual tokenize → model → decode.
- **Model/tokenizer version mismatch**: Always load tokenizer and model from the same checkpoint/revision.

---

## [[Pandas]] / [[NumPy]] / Data Processing

### Pandas Errors
- `SettingWithCopyWarning: A value is trying to be set on a copy of a slice from a DataFrame` → [[State & Lifecycle Errors]]: Chained indexing creates ambiguous view vs copy.
  - **Fix**: Use `.loc[]` or `.iloc[]` for assignment. Use `.copy()` to make explicit copy.
- `ValueError: cannot reindex from a duplicate axis` → [[Offset & Boundary Errors]]: Index has duplicate values.
- `KeyError: 'column_name'` → [[Offset & Boundary Errors]]: Column doesn't exist. Check `df.columns`.
- `ValueError: Length of values does not match length of index` → [[Offset & Boundary Errors]]: Assigning array of wrong length.
- `MemoryError` → [[Resource Exhaustion Errors]]: DataFrame too large for memory.
  - **Fix**: Use `dtype` optimization, chunked reading (`chunksize` parameter), Dask, Polars, or Vaex.
- `ParserError: Error tokenizing data` → [[Serialization & Encoding Errors]]: CSV parsing failure. Check delimiter, quoting, encoding.
- `ValueError: could not convert string to float` → [[Type & Casting Errors]]: Column contains non-numeric strings.
- `TypeError: unsupported operand type(s)` → [[Type & Casting Errors]]: Operating on incompatible dtypes.
- `MergeError: No common columns to perform merge on` → [[Configuration & Environment Errors]]: Join columns don't match.
- `FutureWarning: X is deprecated` → Not an error but indicates upcoming breaking change.
- `PerformanceWarning: DataFrame is highly fragmented` → [[Resource Exhaustion Errors]]: Too many column operations. Use `.copy()` to defragment.

### NumPy Errors
- `ValueError: operands could not be broadcast together with shapes (X,) (Y,)` → [[Offset & Boundary Errors]]: Shape mismatch in broadcasting.
- `ValueError: setting an array element with a sequence` → [[Type & Casting Errors]]: Trying to set scalar with array.
- `LinAlgError: Singular matrix` → [[Offset & Boundary Errors]]: Matrix is not invertible. Check for zero determinant.
- `FloatingPointError: divide by zero encountered` → [[Offset & Boundary Errors]]: Division by zero in array operation.
- `RuntimeWarning: overflow encountered` → [[Offset & Boundary Errors]]: Number exceeds dtype range.
- `RuntimeWarning: invalid value encountered in true_divide` → [[Offset & Boundary Errors]]: 0/0 produces NaN.

### Pandas Traps
- **Chained indexing**: `df[condition]['col'] = value` doesn't modify original. Use `df.loc[condition, 'col'] = value`.
- **`inplace=True` is deprecated**: Will be removed in future Pandas. Assign result instead: `df = df.dropna()`.
- **Index alignment**: Operations between DataFrames align on index. Mismatched indices produce NaN.
- **`apply()` is slow**: Vectorized operations are 10-100x faster. Use NumPy operations when possible.
- **Memory usage**: `object` dtype (strings) uses much more memory than categorical. Use `df['col'].astype('category')`.
- **`NaN` vs `None` vs `pd.NA`**: Different null representations. `NaN` is float, `None` is Python null, `pd.NA` is Pandas nullable.

---

## [[Spark]]

### Common Errors
- `OutOfMemoryError: Java heap space` → [[Resource Exhaustion Errors]]: Driver or executor OOM.
  - **Fix**: Increase `spark.driver.memory` / `spark.executor.memory`. Repartition data. Avoid `collect()` on large datasets.
- `OutOfMemoryError: GC overhead limit exceeded` → [[Resource Exhaustion Errors]]: Too much time in garbage collection.
- `AnalysisException: cannot resolve 'X'` → [[Type & Casting Errors]]: Column name doesn't exist or is ambiguous.
- `AnalysisException: Reference 'X' is ambiguous` → [[Type & Casting Errors]]: Column exists in multiple DataFrames after join.
- `Py4JJavaError: An error occurred while calling` → Various: Python-to-Java bridge error. Read the Java stack trace.
- `Task not serializable` → [[Serialization & Encoding Errors]]: Closure captures non-serializable object.
  - **Fix**: Don't reference non-serializable objects (database connections, SparkContext) inside transformations. Use broadcast variables.
- `SparkException: Job aborted due to stage failure` → Various: Check executor logs for the actual error.
- `FileNotFoundException` → [[Configuration & Environment Errors]]: Input path doesn't exist.
- `PermissionDeniedException` → [[Permission & Authorization Errors]]: No access to data source.
- `StreamingQueryException` → Various: Structured Streaming error. Check the cause.

### Spark Traps
- **Lazy evaluation**: Transformations are lazy. Errors surface at action time (`collect`, `count`, `write`), not transformation time.
- **Data skew**: One partition much larger than others → one task takes forever or OOMs. Use salting, repartition, or broadcast join.
- **Small files problem**: Too many small files = too many tasks = overhead. Coalesce output files.
- **`collect()` on large data**: Brings all data to driver. OOM. Use `take()`, `show()`, or write to storage.
- **Shuffle operations**: `groupBy`, `join`, `repartition` cause shuffle (data movement across network). Expensive.
- **Broadcast join**: For small tables, use broadcast join to avoid shuffle: `spark.sql.autoBroadcastJoinThreshold`.
- **Serialization**: Use Kryo serializer for better performance: `spark.serializer=org.apache.spark.serializer.KryoSerializer`.
- **Python UDF performance**: Python UDFs are slow (data serialized between JVM and Python). Use Pandas UDFs or native Spark functions.

---

## Computer Vision Errors

### Common Issues
- `RuntimeError: Given groups=1, weight of size [X], expected input[Y] channels` → [[Offset & Boundary Errors]]: Input channels don't match model expectation (e.g., grayscale vs RGB).
- `ValueError: Expected input batch_size (X) to match target batch_size (Y)` → [[Offset & Boundary Errors]]: Batch size mismatch.
- **Image loading errors** → [[Serialization & Encoding Errors]]: Corrupted image, unsupported format, truncated file.
  - **Fix**: Use `PIL.Image.verify()`, handle exceptions in data loader, filter corrupt images.
- **Augmentation errors** → [[Offset & Boundary Errors]]: Augmentation produces invalid image (negative values, wrong range).
  - **Fix**: Normalize after augmentation. Check value ranges.
- **Bounding box errors** → [[Offset & Boundary Errors]]: Coordinates outside image bounds after augmentation/resize.

---

## NLP Errors

### Common Issues
- `RuntimeError: The expanded size of the tensor (X) must match the existing size (Y)` → [[Offset & Boundary Errors]]: Sequence length mismatch.
- **Tokenization mismatch** → [[Serialization & Encoding Errors]]: Different tokenizer used for training vs inference.
- **OOV (Out of Vocabulary)** → [[Offset & Boundary Errors]]: Token not in vocabulary. Use subword tokenization (BPE, WordPiece).
- **Padding/truncation issues** → [[Offset & Boundary Errors]]: Sequences not padded to same length, or truncated too aggressively.
- **Attention mask errors** → [[State & Lifecycle Errors]]: Attention mask not matching input. Padding tokens attended to.

---

## MLOps & Deployment Errors

### Model Serving
- `Model not found` → [[Configuration & Environment Errors]]: Wrong model path or version.
- `Input shape mismatch` → [[Offset & Boundary Errors]]: Inference input doesn't match model's expected shape.
- `Timeout during inference` → [[Resource Exhaustion Errors]]: Model too slow. Use batching, quantization, or faster hardware.
- **Model version mismatch** → [[Serialization & Encoding Errors]]: Model saved with different framework version.
- **Feature store drift** → [[The Silent Failure]]: Training features differ from serving features.

### Experiment Tracking
- `MLflow: Run already finished` → [[State & Lifecycle Errors]]: Trying to log to completed run.
- `W&B: Rate limit exceeded` → [[Resource Exhaustion Errors]]: Too many API calls to Weights & Biases.
- **Metric not logged** → [[The Silent Failure]]: Forgot to log metric, or logging call failed silently.

### Data Pipeline
- **Training/serving skew** → [[The Silent Failure]]: Data preprocessing differs between training and inference. Use the same pipeline.
- **Data leakage** → [[The Silent Failure]]: Test data information leaks into training. Results look great but model fails in production.
- **Label noise** → [[The Silent Failure]]: Incorrect labels in training data. Model learns wrong patterns.
- **Class imbalance** → [[The Silent Failure]]: Model predicts majority class for everything. Use oversampling, undersampling, class weights, or focal loss.
