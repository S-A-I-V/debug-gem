---
tags: [data, python, dataframe]
aliases: [pandas, pd]
---
# Pandas

Python data manipulation library. DataFrames for tabular data. The workhorse of data science.

## Known For These Error Patterns
- [[State & Lifecycle Errors]] — `SettingWithCopyWarning` (chained indexing ambiguity)
- [[Offset & Boundary Errors]] — KeyError on missing column, index mismatch
- [[Resource Exhaustion Errors]] — MemoryError on large datasets (loads everything into RAM)
- [[Type & Casting Errors]] — dtype mismatches, string-to-numeric conversion failures

## Common Gotchas
- Chained indexing: `df[cond]['col'] = val` doesn't modify original — use `df.loc[cond, 'col']`
- `apply()` is slow — use vectorized operations
- `object` dtype (strings) uses much more memory than `category`
- `NaN` vs `None` vs `pd.NA` — different null representations

## Related
- [[NumPy]] — underlying array library
- [[Spark]] — distributed alternative for big data
- [[Python]] — Pandas is Python-native
- [[MOC — AI & ML]]

## My Notes

