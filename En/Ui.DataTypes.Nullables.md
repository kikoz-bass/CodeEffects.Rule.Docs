
# Nullable Value Support in Code Effects Platform

Code Effects provides comprehensive support for nullable [value types](/decision-automation/business-rules-data-types) throughout [rule authoring](/decision-automation/business-rule-management) and [evaluation](/decision-automation/business-rule-evaluation). Nullable properties, fields, collection elements, method parameters, and method return values participate in rules in a natural and predictable manner, allowing rule authors to focus on business logic rather than the implementation details of the underlying data model.

> **NOTE:** The nullable compatibility rules described in this article are applied in addition to Code Effects' automatic numeric type conversion. The rule engine automatically converts compatible numeric value types during evaluation, and the [Rule Editor](/decision-automation/business-rule-editor) uses the same logic when constructing menus.
>
> Therefore, references in this article to types such as `decimal` or `decimal?` should be understood as shorthand for any numeric type that can be converted to those types. For example, a parameter declared as `decimal` can also accept values of types such as `short`, `int`, `long`, `float`, and `double`, provided they satisfy the nullable restrictions described in this article. Likewise, a `decimal?` parameter can accept both nullable and non-nullable numeric values that can be converted to `decimal?`.
>
> See [this article](/decision-automation/business-rules-automatic-numeric-type-conversion) for details about automatic numeric type conversion in Code Effects.

## Fields, Properties, and Method Return Values

Nullable and non-nullable value types can be compared directly in rule conditions without any restrictions.

During rule evaluation, if one operand is non-nullable and the other is nullable, the engine automatically converts the non-nullable value to its nullable equivalent and performs the comparison using the operator selected in the condition.

For example, the following comparisons are fully supported:

- `int` compared to `int?`
- `decimal?` compared to `decimal`
- `DateTime` compared to `DateTime?`
- Nullable method return values compared to non-nullable fields
- Non-nullable method return values compared to nullable fields

This behavior applies equally to fields, properties, and return values of in-rule methods. As a result, rule authors do not need to distinguish between nullable and non-nullable value types when building conditions.

### Rule Editor Behavior

The Rule Editor automatically exposes nullable and non-nullable members together when constructing conditions. For example, if a user selects a non-nullable `int` field from the fields menu, the subsequent value menu will include all compatible integer values, including:

- Non-nullable integer fields and properties (`int`)
- Nullable integer fields and properties (`int?`)
- In-rule methods returning `int`
- In-rule methods returning `int?`

The same compatibility behavior applies when the initially selected value is nullable. This design enables users to author rules based on business meaning instead of technical type declarations.

## Method Parameters

Parameters of in-rule methods and rule actions follow slightly stricter compatibility rules.

- ### Nullable Parameters

	Parameters declared as nullable value types accept both nullable and non-nullable values. For example, if a method parameter is declared as `decimal? amount`, users can supply values originating from both `decimal` and `decimal?` fields and properties.

- ### Non-Nullable Parameters

	Parameters declared as non-nullable value types only accept non-nullable values. For example, if a method parameter is declared as `decimal amount`, users may only provide values originating from `decimal` sources. Nullable values such as `decimal?` are not permitted.

In other words, nullable parameters are permissive, while non-nullable parameters remain strict. These rules apply to value typed parameters of both the in-rule methods and rule actions.

## Value Setters in Execution Rules

Execution type rules apply the same enforcement to value assignments performed after the `Then` clause (setters).

- ### Assigning Nullable Values

	Nullable setters can receive values originating from both nullable and non-nullable sources. For example, a setter field declared as `decimal? ApprovedAmount` may be assigned values from `decimal` and `decimal?` sources.

- ### Assigning Non-Nullable Values

	Non-nullable fields can only receive values originating from non-nullable sources. For example, a field declared as `decimal ApprovedAmount` cannot be assigned a value originating from `decimal?`.

## Collections and Arrays of Value Types

Collections and arrays containing value types follow the same compatibility rules used by method parameters and value setters.

- ### Nullable Collection Element Types

	Collections and arrays whose element type is nullable accept both nullable and non-nullable values. For example, `List<decimal?>` can be evaluated using both `decimal` and `decimal?` values.

- ### Non-Nullable Collection Element Types

	Collections and arrays whose element type is non-nullable accept only non-nullable values. For example, `List<decimal>` can only be evaluated using `decimal` values. The following scenario is therefore not supported:

	```
	Check if List<decimal> contains decimal?
	```

	However, the opposite scenario is fully supported:

	```
	Check whether List<decimal?> contains decimal
	```

	The same rules apply to arrays.

## Adaptive Source Considerations

In Adaptive Source source model, you have complete control over which fields, methods, and actions are returned to the editor for each menu request.

However, the Rule Editor maintains an internal cache of previously retrieved source elements to improve responsiveness. If sufficient compatible members have already been cached from earlier requests, the editor may continue applying its built-in nullable compatibility logic and display cached nullable and non-nullable members together, even if they are not included in the most recent Adaptive Source response.

## Nullable Enumerations

> **IMPORTANT:** Code Effects does not support nullable enumerations (`enum?`).

For certain internal implementation reasons, nullable enumerations cannot be used by the platform. Enumeration values must therefore always be declared as non-nullable enumeration types.

## Summary

Nullable value support in Code Effects is designed to remove unnecessary friction from rule authoring while preserving type safety where it matters.

The general principles are straightforward:

- Rule conditions freely compare nullable and non-nullable value types.
- Nullable method parameters accept both nullable and non-nullable values.
- Non-nullable parameters accept only non-nullable values.
- Execution rule setters follow the same rules as parameters.
- Collections and arrays enforce the same compatibility model as parameters and setters.
- Nullable enumerations are not supported.

This approach provides an intuitive authoring experience for business users while maintaining predictable evaluation semantics and strong typing throughout the platform.