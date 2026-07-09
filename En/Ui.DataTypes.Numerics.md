
# Automatic Numeric Type Conversion

Code Effects performs automatic numeric type conversion during both [rule authoring](/decision-automation/business-rule-management) and [rule evaluation](/decision-automation/business-rule-evaluation). This allows rule authors to work with compatible [numeric values](/decision-automation/business-rules-data-types) without requiring exact CLR type matches in the underlying [source object](/decision-automation/business-rule-source-object).

The platform applies numeric compatibility differently depending on how the value is used in a rule. Rule conditions are intentionally permissive, while in-rule method parameters, rule action parameters, value setters, collections, and arrays enforce stricter compatibility rules.

> **NOTE:** This article describes automatic conversion between numeric value types only. Nullable value compatibility is documented separately and may impose additional restrictions. See [Nullable Value Support](business-rules-nullable-values-support) for details.

## Supported Numeric Types

Automatic conversion applies to the following numeric value types:

- `byte`
- `sbyte`
- `short`
- `ushort`
- `int`
- `uint`
- `long`
- `ulong`
- `float`
- `double`
- `decimal`

## Numeric Conversion in Rule Conditions

In rule conditions, Code Effects allows numeric fields, properties, and method return values to be compared regardless of their specific numeric type. For example, a rule condition can compare:

```csharp
int = decimal
short < long
double >= decimal
float = int
```

When a rule condition is evaluated, the [`Evaluator`](/decision-automation/rule-engine-evaluator) casts both numeric operands to `decimal` and then applies the selected condition operator to the resulting decimal values.

This approach works because all standard .NET numeric value types supported by Code Effects can be converted to `decimal`. As a result, rule authors can compare decimal and integral numeric values freely in conditions without needing exact type matches.

## Numeric Conversion Outside Conditions

Numeric values used outside rule conditions follow stricter compatibility rules. This applies to:

- In-rule method parameters;
- Rule action parameters;
- Value setters in execution-type rules;
- Value-typed collections;
- Arrays.

In these cases, the selected value must be compatible with the target numeric type. The editor does not simply allow any numeric value of any numeric type.

Each numeric rule element also exposes the `AllowDecimals` setting, which indicates whether the value can contain fractional components. In the [Reflection source model](/decision-automation/business-rule-source-object-class), the [Rule Editor](/decision-automation/business-rule-editor) derives this setting automatically from the CLR type of the underlying member, so there is no need to specify it explicitly using the [`Filed`](/decision-automation/rule-common-attributes-field) attribute) attribute. The editor uses this setting to prevent conversions that could discard the fractional part of a value and lead to a loss of precision.

For example, a value that allows decimal fractions is not offered where an integral value is required, because a value such as `12.5` cannot be safely converted to `int`, `long`, or `short` without losing the fractional part.

However, integral values may be used where decimal values are permitted, provided all other compatibility requirements are satisfied.

## Numeric Range Compatibility

Minimum and maximum range checks apply to all numeric rule elements, including rule conditions, method parameters, rule action parameters, value setters, collections, and arrays.

Each numeric rule element exposes minimum and maximum bounds that describe its valid range of values. When constructing menus, the Rule Editor compares those ranges and includes only elements whose ranges are compatible with the currently selected element:

```csharp
using CodeEffects.Rule.Common.Attributes;
using CodeEffects.Rule.Common.Models;
using CodeEffects.Rule.Editor.Client;

// Adaptive Source
var field = new Field
{
	Value = "User.UserID",
	DisplayName = "User ID",
	DataType = DataType.Numeric,
	Nullable = true,
	Settings = new Settings
	{
		Min = 0,
		Max = 1000000,
		AllowDecimals = false
	}
};
 
// Reflection source
[Field(DisplayName = "User ID", Min = 0, Max = 1000000)]
public int? UserID { get; set; }
```
When constructing menus, the editor compares these ranges and includes only those elements whose ranges are compatible with the currently selected element. For example, although an `int` value could normally be converted to `decimal`, a value constrained to the range `0-20` will not be considered compatible with a value constrained to `100-200` because the candidate value cannot satisfy the required range.

Ranges are considered compatible when they overlap **or touch** at their boundaries. Therefore, the following ranges are compatible:

```
0–20 and 20–100
```

because they share the boundary value `20`.

## Evaluation Behavior

The Rule Editor uses metadata-based compatibility checks to filter menu choices during rule authoring.

The Evaluator, however, performs the actual CLR numeric conversions when compiling rules into IL before evaluation begins. As a result, conversions that cannot be completed successfully at runtime produce the same exceptions that would be thrown by equivalent .NET code, such as `OverflowException` when a value falls outside the range of the target type.

## Important Considerations

Automatic numeric conversion does not make Code Effects loosely typed. The Rule Editor prevents invalid selections during rule authoring whenever possible, and the [`Evaluator`](/decision-automation/rule-engine-evaluator) performs the required conversions only when the involved values satisfy the platform's compatibility requirements.

Therefore, when this documentation refers to a specific numeric type, such as `decimal`, it should generally be understood as:

> "`decimal` and any compatible numeric type that can be converted to `decimal` according to the rules described in this article."

The same principle applies to all supported numeric value types.

Rule conditions are permissive because both operands are normalized to `decimal` before comparison. Other rule operations remain stricter because they must pass values into specific parameters, assign values to specific fields, or use values with specific collection and array element types.

## Summary

Automatic numeric type conversion allows Code Effects rules to work naturally with source models that use different numeric value types. The main rules are:

- Numeric values in rule conditions can be compared regardless of their specific numeric type.
- During condition evaluation, both numeric operands are converted to `decimal` before comparison.
- Numeric values used as method parameters, rule action parameters, value setters, collection values, or array values must satisfy stricter compatibility rules.
- Minimum and maximum range checks apply to all numeric rule elements.
- `AllowDecimals` filtering applies to numeric operations outside rule conditions.
- The Evaluator performs actual CLR conversions when compiling rules into IL.

This approach gives rule authors the flexibility to compare numeric values naturally while preserving type safety for operations that pass, assign, or use values in strongly typed contexts.

> Numeric conversion is one global compatibility mechanism that applies everywhere in the platform.