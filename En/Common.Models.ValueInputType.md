
# ValueInputType Enumeration

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Models`</small></em>

## Summary

Defines the type of input that the user can use to set the value of a particular field in the [Rule Area](/decision-automation/business-rule-editor).

## Syntax

```csharp
public enum ValueInputType
{
	// Allows selection of fields, in-rule methods, or reusable rules only
	Fields = 0,

	// Allows the user to enter a value manually or select it from predefined ranges only
	User = 2,

	// Allows all types of value inputs
	All = 4
}
```

## Remarks

This enumeration is used by the [`FieldAttribute`](/decision-automation/rule-common-attributes-fieldattribute), [`ReturnAttribute`](/decision-automation/rule-common-attributes-returnattribute), and [`ParameterAttribute`](/decision-automation/rule-common-attributes-parameterattribute) to specify how rule authors are permitted to input values for fields, method return values, and method parameters of the [source object](/decision-automation/business-rule-source-object). See the corresponding topics for details.