---
tags: [language, runtime, backend, dotnet, gamedev]
aliases: [csharp, c-sharp, dotnet]
---
# C#

Microsoft's language for .NET. Used in enterprise backends, Unity game development, and Azure services.

## Known For These Error Patterns
- [[Null & Undefined Reference Errors]] — `NullReferenceException` (the C# NPE)
- [[State & Lifecycle Errors]] — `ObjectDisposedException`
- [[Resource Exhaustion Errors]] — `OutOfMemoryException`, `StackOverflowException`
- [[Concurrency & Race Condition Errors]] — Collection modified during enumeration

## Common Gotchas
- `async void` — only for event handlers, exceptions crash the process
- `IDisposable` leaks — always use `using` statement
- LINQ deferred execution — multiple enumeration re-executes query
- `HttpClient` per-request causes socket exhaustion — use `IHttpClientFactory`

## Related
- [[Java]] — similar ecosystem and patterns
- [[MOC — Languages & Runtimes]]

## My Notes

