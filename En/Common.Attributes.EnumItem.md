
# EnumItemAttribute Class

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Attributes`</small></em>

## Summary

When applied to a member of an enumeration that is used by a [source object](/decision-automation/business-rule-source-object) as a rule field, this attribute specifies the display name for that member.

## Syntax

```csharp
[AttributeUsage(AttributeTargets.Field, AllowMultiple = false, Inherited = false)]
public class EnumItemAttribute : System.Attribute,
	CodeEffects.Rule.Common.Attributes.IDisplayableAttribute
```

## Properties

- ### DisplayName<br/>
	<small>Type: `System.String`</small>

	Gets or sets the display name of the enumeration member in the [Rule Editor](/decision-automation/business-rule-editor).

## Example

```csharp
using CodeEffects.Rule.Common.Attributes;

public enum State
{
	[ExcludeFromEvaluation]
	Undefined,

	Arizona,
	California,
	Florida,
	Georgia,

	[EnumItem("North Dakota")]
	NorthDakota,

	[EnumItem("South Carolina")]
	SouthCarolina
}
```