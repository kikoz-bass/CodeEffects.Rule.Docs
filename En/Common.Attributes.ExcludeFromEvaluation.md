
# ExcludeFromEvaluationAttribute Class

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Attributes`</small></em>

## Summary

When applied to a member of the [source object](/decision-automation/business-rule-source-object), this attribute instructs [Rule Editor](/decision-automation/business-rule-editor) to ignore that member.

## Syntax

```csharp
[AttributeUsage(AttributeTargets.Property | AttributeTargets.Field | AttributeTargets.Method, AllowMultiple = false)]
public class ExcludeFromEvaluationAttribute : System.Attribute
```

## Example

```csharp
using CodeEffects.Rule.Common.Attributes;

namespace TestLibrary
{
	public class Thing
	{
		[ExcludeFromEvaluation]
		public int ID { get; set; }

		public string Name { get; set; }
	}
}
```