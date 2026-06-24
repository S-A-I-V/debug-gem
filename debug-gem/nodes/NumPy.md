---
tags: [data, python, numerical]
aliases: [numpy, np]
---
# NumPy

Numerical computing library for [[Python]]. N-dimensional arrays, linear algebra, random numbers. Foundation for scientific Python.

## Known For These Error Patterns
- [[Offset & Boundary Errors]] — Shape mismatch in broadcasting, `IndexError: index out of bounds`, axis out of range
- [[Type & Casting Errors]] — dtype mismatches, overflow/underflow warnings, unsafe casting
- [[Resource Exhaustion Errors]] — `MemoryError` allocating large arrays
- [[Serialization & Encoding Errors]] — Binary format version mismatch when loading `.npy` files

## Common Errors
- `ValueError: operands could not be broadcast together with shapes (X,) (Y,)` — Shape mismatch. Check array shapes with `.shape`.
- `IndexError: index X is out of bounds for axis Y with size Z` — Array index exceeds dimension size.
- `LinAlgError: Singular matrix` — Matrix is not invertible. Check with `np.linalg.det()` or use pseudoinverse `np.linalg.pinv()`.
- `TypeError: Cannot cast ufunc 'add' output from dtype('float64') to dtype('int64')` — Operation would lose precision.
- `RuntimeWarning: overflow encountered` — Integer overflow with fixed-size dtypes. Use `int64` or `float64`.
- `MemoryError` — Array too large for RAM. Use memory-mapped files (`np.memmap`), chunked processing, or reduce dtype precision.

## Common Gotchas
- Views vs copies — slicing creates a VIEW (shared memory). Modifying view modifies original! Use `.copy()` for independent array.
- Broadcasting rules — dimensions compared right-to-left, must be equal or one must be 1
- Integer overflow is SILENT with default dtypes — `np.int8(127) + np.int8(1) = -128` (wraps around)
- `==` on floats is unreliable — use `np.isclose()` or `np.allclose()` for float comparison
- Random number generation — `np.random.seed()` is global state. Use `np.random.default_rng()` for reproducible, isolated randomness.
- Fancy indexing (with arrays) ALWAYS returns a copy, never a view

## Broadcasting Rules
```
Shape A: (8, 1, 6, 1)
Shape B:    (7, 1, 5)
Result:  (8, 7, 6, 5)
```
Dimensions align right-to-left. Each pair must be equal OR one must be 1.

## Related
- [[Pandas]] — built on NumPy (tabular data)
- [[PyTorch]], [[TensorFlow]] — tensor libraries inspired by NumPy API
- [[Python]] — NumPy is Python-native
- [[MOC — AI & ML]]

## My Notes

