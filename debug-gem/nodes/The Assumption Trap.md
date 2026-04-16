---
tags: [meta-pattern, debugging-philosophy]
---
# The Assumption Trap

> Every bug is a violated assumption.

The code assumed X, reality delivered Y. Debug by listing every assumption the code makes and testing each one.

## Common False Assumptions
- "This will never be null"
- "This list will always have elements"
- "This API will always return 200"
- "This will complete in under 1 second"
- "This string is always UTF-8"
- "This number will always be positive"
- "This user input will be well-formed"
- "This file will always exist"
- "The database will always be available"
- "The order of these events is guaranteed"

## Debug Technique
Write down 5 assumptions the failing code makes. Test each one. The bug is in the false assumption.

## Related
- [[The Boundary Problem]], [[The Environment Delta]], [[The Silent Failure]]

## My Notes

