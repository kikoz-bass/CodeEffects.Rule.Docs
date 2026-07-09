
# ReturnAttribute Class

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Attributes`</small></em>

## Summary

If applied to the return type of a qualified [in-rule method](/decision-automation/rule-common-attributes-method), marks it as a value element of a rule condition. The method must return a [value type](/decision-automation/business-rules-data-types) supported by Code Effects.

## Syntax

```csharp
[AttributeUsage(AttributeTargets.ReturnValue, AllowMultiple = false, Inherited = false)]
public class ReturnAttribute : System.Attribute
```

## Properties

<div class="members">

- ### AllowCalculations<br/>
	<small>Type: `System.Bool`</small>

	Used only for return values of [numeric type](/decision-automation/business-rules-data-types) and ignored for values of other types. Gets or sets a value indicating whether the return value of the in-rule method can be expressed as a calculation. This property is optional. The default value is `true`.
	
	![Numeric Calculation](/resources/images/doc/editorcalcfield.png)

- ### DataSourceName<br/>
	<small>Type: `System.String`</small>

	Used only in return values of `System.Int32` type, and is ignored in return values of other types. Gets or sets the unique name of the [Dynamic Menu Data Source](/decision-automation/business-rules-dynamic-menu-data-sources) declared by the [`SourceAttribute`](/decision-automation/rule-common-attributes-source) on the [source object](/decision-automation/business-rule-source-object).

- ### DateTimeFormat<br/>
	<small>Type: `System.String`</small>

	Used only for return values of date and time [types](/decision-automation/business-rules-data-types) and ignored for values of other types. Gets or sets the date or time format of the return value displayed in the Rule Editor. You can use standard .NET [date and time formatting](https://learn.microsoft.com/en-us/dotnet/standard/base-types/custom-date-and-time-format-strings). This property is not used during rule evaluation. This property is optional. The default date format is `MMM dd, yyyy`, and the default time format is `hh:mm tt`. The use of this property is identical to the use of [`FieldAttribute.DateTimeFormat`](/decision-automation/rule-common-attributes-field).

- ### Max<br/>
	<small>Type: `System.Int64`</small>

	Used only for return values of [numeric and string](/decision-automation/business-rules-data-types) types and ignored for values of other types. For numeric values, gets or sets the maximum numeric value that rule authors can enter manually. For string values, gets or sets the maximum length of the string that rule authors can type as the value. This value is not used during rule evaluation. This property is optional.

	- Max value for numeric types allowed by the editor: `2147483647`
	- Max length for string types allowed by the editor: `256`

- ### Min<br/>
	<small>Type: `System.Int64`</small>

	Used only for return values of numeric type and ignored for values of other types. Gets or sets the minimum numeric value that rule authors can enter manually. This value is not used during rule evaluation. This property is optional. The minimum value allowed by the editor is `-2147483648`.

- ### StringComparison<br/>
	<small>Type: `System.StringComparison`</small>

	Used only in return values of [string type](/decision-automation/business-rules-data-types) and ignored for values of other types. Gets or sets the string comparison option that determines how string values are compared in rule conditions during evaluation. Can be used to specify culture-specific comparison behavior, including case sensitivity. The default value is `System.StringComparison.OrdinalIgnoreCase`.

- ### ValueInputType<br/>
	<small>Type: `CodeEffects.Rule.Common.Models.ValueInputType`</small>

	Gets or sets a value that indicates whether the return value can only be entered manually, can only accept other fields as its value, or can accept both. For example, consider the rule condition `Get ID(LastName) = 123`. The return value of the in-rule method `Get ID` is `123`, which is a number manually entered by the rule author. This is a user-entered value and is of type `ValueInputType.User`. The same condition can also be expressed as `Get ID(FirstName) = Admin ID`. Since `Admin ID` is a field, this value was selected by the rule author from the menu of available fields, and is therefore of type `ValueInputType.Fields`. This property is optional. The default value is `ValueInputType.All`, which allows both input types.

</div>

## Remarks

In the rule condition...

```
	... Get Age( Birthday ) is equal to 18 ...
```

... `Get Age` is an in-rule method, `Birthday` is its parameter, and `18` is a value element that can be customized with the `ReturnAttribute` (that is, the return value of the in-rule method). See the [source object](/decision-automation/business-rule-source-object) topic for details on in-rule methods and actions.

## Example

```csharp
using CodeEffects.Rule.Common.Attributes;

namespace TestLibrary
{
	public class Person
	{
		[Field(DateTimeFormat = "yyyy-MM-dd", DisplayName = "Birthday")]
		public DateTime DOB { get; set; } = DateTime.MinValue;

		[Method("Get Age")]
		[return: Return(Min = 0, Max = 150)]
		public int GetAge()
		{
			return (DateTime.Now - this.DOB).Year;
		}
	}
}
```