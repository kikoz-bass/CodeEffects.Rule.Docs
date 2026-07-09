
# ExternalMethodAttribute Class

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Attributes`</small></em>

## Summary

**IMPORTANT** Generic methods with generic params cannot be used in [Source XML](/decision-automation/business-rule-source-object-xml) source model as in-rule methods or rule actions. Use [Reflection](/decision-automation/business-rule-source-object-class) model if your [source object](/decision-automation/business-rule-source-object) declares generic methods.

When applied to a source object, this attribute references an external qualified public method to be used as an [in-rule method](/decision-automation). To qualify, the method must be `public`, static or instance, return a value type and must either be parameterless or declare only parameters of the source object type or of any [value type](/decision-automation/business-rules-data-types) supported by Code Effects.

## Syntax

```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Interface, AllowMultiple = true, Inherited = true)]
public class ExternalMethodAttribute : System.Attribute,
	CodeEffects.Rule.Common.Attributes.IExternalAttribute,
	CodeEffects.Rule.Common.Attributes.ISortable
```

## Properties

<div class="members">

- ### Method<br/>
	<small>Type: `System.String`</small>

	Gets the name of the method declared outside the source object that Rule Editor uses as an in-rule method. This property is required.

- ### ParamTypesToMatch<br/>
	<small>Type: `System.Type[]`</small>

	Gets or sets an array of `System.Type` objects that represent the parameter types matching the signature of the external method to be used as an in-rule method. This property can be used to select a specific overload of an external method.

- ### SortOrder<br/>
	<small>Type: `System.Int16`</small>

	Gets or sets the sort order in which the in-rule rule method appears in the field menu of the Rule Area. This property is optional. The default value is `0`.

- ### Type<br/>
	<small>Type: `System.Type`</small>

	Gets the type of the class that declares the external in-rule method. This property is required if the `TypeName` property is not specified.

- ### TypeName<br/>
	<small>Type: `System.String`</small>

	Gets the assembly-qualified name of the type that declares the external in-rule method. You can obtain this name by executing `typeof(ClassName).AssemblyQualifiedName`. This property is required if the `Type` property is not specified.

</div>

## Remarks

This attribute is optional and is used to reference external methods that you want to use as in-rule methods. An exception is thrown if the attribute references a non-qualified method. If the external method defines multiple overloads, decorate each overload that you intend to use as an in-rule method with the [`MethodAttribute`](/decision-automation/rule-common-attributes-method), and assign a unique display name to each using its `DisplayName` property.

Remember that external static or instance in-rule methods can accept your source object as a parameter. When a rule author uses such a method in a rule, parameters of the source object type are not displayed — Rule Editor passes them automatically. In the [Rule XML](/decision-automation/business-rules-storage), the parameter of the source object type is represented as `this`.

## Example

```csharp
using System;
using CodeEffects.Rule.Common.Attributes;

namespace TestLibrary
{
	[ExternalMethod(typeof(Helper), nameof(Helper.GetUtc))]
	public class Test
	{
		// Class implementation
	}

	public class Helper
	{
		[Method("Get UTC Date", "Gets a new UTC value")]
		public static DateTime GetUtc()
		{
			return DateTime.UtcNow;
		}
	}
}
```