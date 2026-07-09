
# ExcludedOperator Enumeration

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Models`</small></em>

## Summary

This enumeration defines operators that should be excluded from use in rule conditions within the editor. See the description of the [`Control.ExcludedOperators`](/decision-automation/rule-editor-control) property for details.

## Syntax

```csharp
public enum ExcludedOperator
{
	// Exclude the Equal operator
	Equal = 1 << 0,

	// Exclude the NotEqual operator
	NotEqual = 1 << 1,

	// Exclude the Less operator
	Less = 1 << 2,

	// Exclude the LessOrEqual operator
	LessOrEqual = 1 << 3,

	// Exclude the Greater operator
	Greater = 1 << 4,

	// Exclude the GreaterOrEqual operator
	GreaterOrEqual = 1 << 5,

	// Exclude the Contains operator
	Contains = 1 << 6,

	// Exclude the DoesNotContain operator
	DoesNotContain = 1 << 7,

	// Exclude the EndsWith operator
	EndsWith = 1 << 8,

	// Exclude the DoesNotEndWith operator
	DoesNotEndWith = 1 << 9,

	// Exclude the StartsWith operator
	StartsWith = 1 << 10,

	// Exclude the DoesNotStartWith operator
	DoesNotStartWith = 1 << 11
}
```