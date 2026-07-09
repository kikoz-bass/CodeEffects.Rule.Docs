
# EvaluationScope Enumeration

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Models`</small></em>

## Summary

Specifies how to treat multiple rules when they are evaluated together.

- `EvaluationScope.All` — all rules are evaluated, and their results are combined using the logical `AND` operator.
- `EvaluationScope.AtLeastOne` — all rules are evaluated, and their results are combined using the logical `OR` operator.

## Syntax

```csharp
public enum EvaluationScope
{
	All,
	AtLeastOne
}
```

## Remarks

Refer to the Remarks section of the [`EvaluationParameters`](/decision-automation/rule-common-models-evaluationparameters) class for usage details of this enumeration.