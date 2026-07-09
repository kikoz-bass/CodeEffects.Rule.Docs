
# Operator Class

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Models`</small></em>

## Summary

Represents an operator excluded by the [Rule Editor](/decision-automation/business-rule-editor) from operator menus in the Rule Area.

## Syntax

```csharp
public class Operator
```

## Properties

<div class="members">

- ### ExcludedOperator<br/>
	<small>Type: [`CodeEffects.Rule.Common.Models.ExcludedOperator`](/decision-automation/rule-common-models-excludedoperator)</small>

	Gets or sets the value indicating which operator or operators should be excluded from use on the client for a given data type.

- ### Type<br/>
	<small>Type: [`CodeEffects.Rule.Common.Models.DataType`](/decision-automation/rule-common-models-datatype)</small>

	Gets or sets the client [data type](/decision-automation/business-rules-data-types) for which the [Rule Editor](/decision-automation/business-rule-editor) should exclude the defined operators.

</div>

## Remarks

Use this class as an item in the [`Control.ExcludedOperators`](/decision-automation/rule-editor-control) collection. The `Operator` type can also be useful when the LINQ provider you use to [filter](/decision-automation/business-rule-data-filtering) source object collections does not implement certain operators. See the description of the `Control.ExcludedOperators` property for details about excluded operators and how to use this feature.