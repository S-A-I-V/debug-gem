---
tags: [error-pattern, universal-failure-pattern]
aliases: [syntax-errors, parse-errors, compilation-errors]
---
# Syntax & Parse Errors

> The code/data is malformed. The parser or compiler can't understand the structure.

One of the 12 [[Universal Failure Taxonomy|Universal Failure Patterns]]. The input doesn't conform to the expected grammar, syntax, or format — the system can't even begin to execute it.

## The Pattern
Before code runs or data is processed, it must be parsed into a structure the system understands. This pattern fires when parsing fails:
- Code doesn't follow language grammar
- Configuration files have invalid syntax
- Data formats are malformed (JSON, XML, YAML)
- Query languages have syntax violations (SQL, GraphQL, regex)

## Universal Symptoms
- `SyntaxError`, `ParseError`, `CompileError`
- `Unexpected token`, `Unexpected end of input`
- `invalid syntax` (Python — check line BEFORE the reported line)
- YAML/JSON parse failures
- `ParserError` in Solidity, HCL, SQL
- Red squiggly lines in IDE before you even run anything

## Diagnostic Decision Tree
1. **WHAT can't be parsed?** Code, config file, data payload, query?
2. **WHERE exactly?** Line number and column in error message
3. **WHAT was expected?** Error usually says "expected X, got Y"
4. **IS IT THE REPORTED LINE?** In Python, the error is often on the line BEFORE. In most languages, check the preceding line for missing bracket/semicolon.
5. **IS IT ENCODING?** Invisible characters (BOM, zero-width space, smart quotes)?

## Common Causes

### Code Syntax
- Missing/extra bracket, parenthesis, brace
- Missing semicolon or comma
- Unclosed string literal
- Indentation error (Python, YAML)
- Using syntax from wrong language version
- Copy-paste from documentation with smart quotes

### Configuration (YAML/JSON/TOML)
- YAML indentation wrong (tabs vs spaces — YAML doesn't allow tabs)
- JSON trailing comma (not allowed)
- JSON with comments (not allowed in standard JSON)
- Unquoted special characters in YAML
- TOML datetime format wrong

### Data Parsing
- Invalid JSON in API response (HTML error page returned instead)
- Malformed CSV (unescaped delimiters in fields)
- XML not well-formed (unclosed tags)
- Regex syntax error (unescaped special characters)

### Invisible Characters
- BOM (Byte Order Mark) at start of file
- Zero-width spaces copied from web
- Smart/curly quotes instead of straight quotes
- Windows line endings (CRLF) causing issues

## Fix Strategies
1. **Read the error message** — it usually points to exact location and what was expected
2. **Check the line BEFORE** — missing closer on previous line causes error on next line
3. **Use a linter/formatter** — catch syntax errors before runtime
4. **Validate config files** — YAML lint, JSON validators, schema validation
5. **Show invisible characters** — turn on whitespace rendering in editor
6. **Use IDE** — real-time syntax checking catches these immediately

## Tools for Detection
- Linters: ESLint, Pylint, RuboCop, `go vet`, Clippy
- Formatters: Prettier, Black, gofmt, rustfmt
- Online validators: jsonlint.com, yamllint.com
- Editor: VS Code, IntelliJ — real-time parse error detection
- `cat -A file` — show invisible characters on Linux/Mac

## Technologies That Commonly Produce This
- [[Python]] — IndentationError, SyntaxError (check line before)
- [[JavaScript]], [[TypeScript]] — Unexpected token
- [[Terraform]] — HCL syntax errors
- [[GitLab CI]], [[GitHub Actions]] — YAML invalid
- [[GraphQL]] — Query syntax errors
- [[Solidity]] — ParserError
- [[Elasticsearch]] — Query DSL malformed JSON
- [[Docker]] — Dockerfile syntax (less common, format is simple)

## Related Patterns
- [[Configuration & Environment Errors]] — valid syntax but wrong values is configuration error
- [[Serialization & Encoding Errors]] — encoding issues can cause parse failures
- [[Dependency & Import Errors]] — wrong file format (ESM vs CJS) causes parse error

## My Notes

