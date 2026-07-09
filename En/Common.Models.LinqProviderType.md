
# LinqProviderType Enumeration

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Models`</small></em>

## Summary

Represents the LINQ provider used when Code Effects builds expression trees during [data filtering](/decision-automation/business-rule-data-filtering) operations. The `Default` value indicates that the engine will attempt to detect the LINQ provider type automatically. Use `SQL` for LINQ-to-SQL providers and `Entities` for LINQ-to-Entities. This enumeration exists because various providers handle string operations differently, and in most cases it is difficult to automatically determine the behavior of the current provider. Theoretically, any LINQ provider is supported as long as it implements the same set of commands as LINQ-to-SQL.

## Syntax

```csharp
public enum LinqProviderType
{
	Default,
	SQL,
	Entities
}
```