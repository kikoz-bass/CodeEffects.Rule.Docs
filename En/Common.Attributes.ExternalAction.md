
# ExternalActionAttribute Class

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Attributes`</small></em>

## Summary

**IMPORTANT** Generic methods with generic params cannot be used in [Source XML](/decision-automation/business-rule-source-object-xml) source model as in-rule methods or rule actions. Use [Reflection](/decision-automation/business-rule-source-object-class) source model if your [source object](/decision-automation/business-rule-source-object) declares generic methods.

When applied to a source object, this attribute references an external qualified public method to be used as a rule action. To qualify, the method must be `public`, static or instance, return `System.Void` and either be parameterless or declare only parameters of the source object type or of [value types](/decision-automation/business-rules-data-types) supported by Code Effects.

## Syntax

```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Interface, AllowMultiple = true, Inherited = true)]
public class ExternalActionAttribute : System.Attribute,
	CodeEffects.Rule.Common.Attributes.IExternalAttribute,
	CodeEffects.Rule.Common.Attributes.ISortable
```

## Properties

<div class="members">

- ### Method<br/>
	<small>Type: `System.String`</small>

	Gets the name of the method declared outside the source object that Rule Editor uses as a rule action. This property is required.

- ### ParamTypesToMatch<br/>
	<small>Type: `System.Type[]`</small>

	Gets or sets an array of `System.Type` objects that represent the parameter types matching the signature of the external method to be used as a rule action. This property can be used to select a specific overload of an external method.

- ### SortOrder<br/>
	<small>Type: `System.Int16`</small>

	Gets or sets the sort order in which the rule action method appears in the action menu of the Rule Area. This property is optional. The default value is `0`.

- ### Type<br/>
	<small>Type: `System.Type`</small>

	Gets the type of the class that declares the external action method. This property is required if the `TypeName` property is not specified.

- ### TypeName<br/>
	<small>Type: `System.String`</small>

	Gets the `AssemblyQualifiedName` name of the type that declares the external action method. You can obtain this name by executing `typeof(ClassName).AssemblyQualifiedName`. This property is required if the `Type` property is not specified.

</div>

## Remarks

This attribute is optional and is used to reference external methods that you want to use as rule actions. An exception is thrown if the attribute references a non-qualified method. If the external method defines multiple overloads, decorate each overload that you intend to use as a rule action with the [`ActionAttribute`](/decision-automation/rule-common-attributes-action), and assign a unique display name to each using its `DisplayName` property.

Remember that external static or instance action methods can accept your source object as a parameter. When a rule author uses such an action in a rule, parameters of the source object type are not displayed — Rule Editor passes them automatically. In the [Rule XML](/decision-automation/business-rules-storage), the parameter of the source object type is represented as `this`.

The following example illustrates how the rule would appear in Rule Area when a user uses both external actions from the code sample below:

```
If ID is greater than 10 then Set new ID (0) and Set the Date to UTC
```

Note that the second method is displayed as parameterless (without parentheses) because it accepts a single parameter of the source type, which is omitted by the editor.

## Example

```csharp
using System;
using CodeEffects.Rule.Common.Attributes;

namespace TestLibrary
{
	// This source object contains no actions of its own
	// but references 2 external actions declared in the Helper class
	[ExternalAction(typeof(Helper), nameof(Helper.SetUtcDate))]
	[ExternalAction(typeof(Helper), nameof(Helper.SetNewID))]
	public class Thing
	{
		public int ID {get; set; } = 0;
		public DateTime? Date { get; set; }
	}

	public class Helper
	{
		[Action("Set new ID", "Sets a new value to the Thing's ID field")]
		public void SetNewID(Thing thing, int id)
		{
			thing.ID = id;
		}

		[Action("Set the Date to UTC", "Sets a new value to the Thing's Date field")]
		public void SetUtcDate(Thing thing)
		{
			thing.Date = DateTime.UtcNow;
		}
	}
}
```