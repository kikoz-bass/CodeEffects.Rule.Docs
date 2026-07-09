
# ParameterAttribute Class

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Attributes`</small></em>

## Summary

**IMPORTANT NOTE #1** Generic methods that declare generic array (not collection) parameters cannot be used as [in-rule methods or rule actions](/decision-automation).

**IMPORTANT NOTE #2** Generic methods with generic params cannot be used in [Source XML](/decision-automation/business-rule-source-object-xml) source model as in-rule methods or rule actions. Use [Reflection](/decision-automation/business-rule-source-object-class) model if your [source object](/decision-automation/business-rule-source-object) declares generic methods.

If applied to a parameter of a qualified method, marks that parameter as a rule action or in-rule method parameter. To qualify, the parameter must be of the source object type, any [value type](/decision-automation/business-rules-data-types) supported by Code Effects, or a [collection](/decision-automation/business-rules-collection-data-types). Methods that declare non-qualified parameters are ignored by Rule Editor.

## Syntax

```csharp
[AttributeUsage(AttributeTargets.Parameter, AllowMultiple = false, Inherited = false)]
public class ParameterAttribute : System.Attribute, CodeEffects.Rule.Common.Attributes.ISettingsAttribute
```

## Properties

<div class="members">

- ### CollectionItemType<br/>
	<small>Type: `System.Type`</small>

	Used only with qualified parameters of non-generic [collection types](/decision-automation/business-rules-collection-data-types) whose underlying element type cannot be determined at run time (qualified collections). This property is ignored for parameters of all other types and for generic collections. Gets or sets the underlying element type of the qualified collection. Examples of qualified collections include `System.Collections.Specialized.StringCollection` and `System.Collections.ArrayList`. This property is optional. The entire method is ignored if the value of this property is not set for any qualified parameter of a qualified collection type.

- ### DataSourceName<br/>
	<small>Type: `System.String`</small>

	Used only with rule action or in-rule method parameters of `System.Int32` type and ignored for parameters of other types. Gets or sets the unique name of the [dynamic menu data source](/decision-automation/business-rules-dynamic-menu-data-sources) declared by the [`SourceAttribute`](/decision-automation/rule-common-attributes-source) on the [source object](/decision-automation/business-rule-source-object). When set, the rule author can select a value only from the list of items provided by the specified data source method.

- ### DateTimeFormat<br/>
	<small>Type: `System.String`</small>

	Used only with qualified parameters of date and time [types](/decision-automation/business-rules-data-types) and ignored for parameters of other types. Gets or sets the display format of the qualified parameter. Supports standard .NET date and time format strings. This property is optional and not used during rule evaluation. The default date format is `MMM dd, yyyy`, and the default time format is `hh:mm tt`.

	**<em>Remarks</em>**

	The Rule Editor uses a built-in date picker for parameters of the `DateTime` type. If the `DateTimeFormat` value includes the time fraction, the date picker includes time selection controls and an <b>OK</b> button, which closes the picker and sets the selected value in the Rule Area. If the format omits the time fraction, the picker includes no time controls. It closes automatically and sets the value when the user clicks the desired date in the calendar.

	If the date value omits the time fraction, the `Evaluator` adds a *zero* time component to the date value and uses the resulting value `yyyy-MM-ddT00:00:00.0000` during rule evaluation.

	```csharp
	[Action(DisplayName = "Set Schedule")]
	public void SetSchedule([Parameter(DateTimeFormat = "MMM-d-yy")] DateTime dateTime)
	{
		// Method implementation
	}
	```

	![Date picker](/resources/images/doc/editordatepickertime3.png)<br>
	![Date picker](/resources/images/doc/editordatepickertime4.png)

	```csharp
	[Action(DisplayName = "Set Schedule")]
	public void SetSchedule([Parameter(DateTimeFormat = "yyyy.MM.dd HH:mm:ss")] DateTime dateTime)
	{
		// Method implementation
	}
	```

	![Date picker](/resources/images/doc/editordatepickertime5.png)<br>
	![Date picker](/resources/images/doc/editordatepickertime6.png)

- ### Description<br/>
	<small>Type: `System.String`</small>

	Gets or sets the description of the parameter. Rule authors can view this description by hovering the mouse pointer over the parameter element in the [Rule Editor](/decision-automation/business-rule-editor). The value of this property is ignored if `Control.ShowDescriptionsOnMouseHover` is set to `false`. It is not recommended to use this property in [multilingual applications](/decision-automation/business-rule-multilingual-support); instead, use culture-specific custom [Source XML](/decision-automation/business-rule-source-object-xml) documents. This property is optional.

- ### Filter<br/>
	<small>Type: `System.String`</small>

	Gets or sets the name of the menu filter. This property is optional.

	**<em>Remarks</em>**

	Use this property to instruct the Rule Editor to allow value comparisons in in-rule method and rule action parameters based on the filter value. For example, consider a source object that declares four properties of the `System.String` type, and a rule action method that accepts a parameter of the `System.String` type:

	```csharp
	[Field(DisplayName = "Work Email", Max = 200)]
	public string WorkEmail { get; set; }

	[Field(DisplayName = "Home Email", Max = 200)]
	public string HomeEmail { get; set; }

	[Field(DisplayName = "Work Phone", Max = 10)]
	public string WorkPhone { get; set; }

	[Field(DisplayName = "Home Phone", Max = 10)]
	public string HomePhone { get; set; }

	[Action(DisplayName = "Save Phone")]
	public void SavePhone([Parameter(Description = "Phone number", Max = 10)] string phone)
	{
		// Method implementation
	}
	```
	With this source object, a user could pass both email and phone number values as the `SavePhone` parameter. Although all those fields are of the `System.String` type, they clearly represent unrelated data:

	![Collection item](/resources/images/doc/filterfive.png)

	Let’s separate these fields and the `SavePhone` parameter into two filters: <b>email</b> and <b>phone</b>:

	```csharp
	using CodeEffects.Rule.Common.Attributes;

	namespace TestLibrary
	{
		public class Thing
		{
			[Field(DisplayName = "Work Email", Max = 200, Filter = "email")]
			public string WorkEmail { get; set; }

			[Field(DisplayName = "Home Email", Max = 200, Filter = "email")]
			public string HomeEmail { get; set; }

			[Field(DisplayName = "Work Phone", Max = 10, Filter = "phone")]
			public string WorkPhone { get; set; }

			[Field(DisplayName = "Home Phone", Max = 10, Filter = "phone")]
			public string HomePhone { get; set; }
			
			[Action(DisplayName = "Save Phone")]
			public void SavePhone([Parameter(Description = "Phone number", Max = 10, Filter = "phone")] string phone)
			{
				// Method implementation
			}
		}
	}
	```
	Now, users can pass only phone numbers as the parameter to the rule action that expects phone numbers:

	![Collection item](/resources/images/doc/filtersix.png)

- ### Max<br/>
	<small>Type: `System.Int64`</small>

	Used only with qualified parameters of numeric and string types, and ignored by parameters of [other types](/decision-automation/business-rules-data-types). For numeric parameters, gets or sets the maximum value that rule authors can enter manually. For string parameters, gets or sets the maximum length of the string that rule authors can type as the value. Setting this property has no effect on rule evaluation. This property is optional.

	- Max value for numeric types allowed by the editor: `2147483647`
	- Max length for string types allowed by the editor: `256`

- ### Min<br/>
	<small>Type: `System.Int64`</small>

	Used only with qualified parameters of numeric type, and ignored by parameters of [other types](/decision-automation/business-rules-data-types). Gets or sets the minimum value that rule authors can enter manually for the qualified parameter. Setting this property has no effect on rule evaluation. This property is optional. The minimum value allowed by the editor is `-2147483648`.

- ### ValueInputType<br/>
	<small>Type: `CodeEffects.Rule.Common.Models.ValueInputType`</small>

	Gets or sets a value indicating whether the qualified parameter can only be entered manually, can only accept fields of a certain type, or both. For example, consider the in-rule method `DoSomething(John)`. The parameter `John` is a string that the rule author typed in. This is a <b>value</b> entered by the user, i.e., it is of type `ValueInputType.User`. The same in-rule method can be expressed as `DoSomething(MiddleName)`. In this case, the parameter `MiddleName` is a field, so it was selected by the rule author as a <b>field</b> item from the menu, i.e., it is of type `ValueInputType.Fields`. This property is optional. The default value is `ValueInputType.All`, which allows <b>both</b> input types.

</div>

## Remarks

In the rule

```
If Get Credit Score(SSN) is less than 640 then Decline(Bad Credit)
```
the `Get Credit Score` is an in-rule method, and `SSN` is its parameter, which can be customized with the `ParameterAttribute`. The value `640` represents the value element that can be customized with the [`ReturnAttribute`](codeeffects-rule-common-attributes-return) (i.e., the return value of the in-rule method). The `Decline` is a rule action method, and `Bad Credit` is the action’s parameter, which can also be customized with the `ParameterAttribute`.

```csharp
using CodeEffects.Rule.Common.Models;
using CodeEffects.Rule.Common.Attributes;

namespace TestLibrary
{
	[ExternalAction(typeof(Helper), "Action")]
	public class Test
	{
		[Field(Max = 30)]
		public string Something { get; set; }
	}

	public class Helper
	{
		[Action("Action One")]
		public void Action()
		{
			// Method implementation
		}

		[Action("Action Two")]
		public void Action(Test source)
		{
			// Method implementation
		}

		[Action("Action Three")]
		public void Action([Parameter(ValueInputType.User)] int number)
		{
			// Method implementation
		}
	}
}
```

The `Action One` method takes no parameters; rule authors will see it as a field (no parentheses):

```
If Something is ABC then Action One ...
```

The `Action Two` method takes a single parameter of type `Test`. This class serves as the source object for the rule. During evaluation, the Rule Evaluator automatically passes the instance of the source object to the action. The Rule Editor also displays `Action Two` actipon method as a field (no parentheses) — there is no need to expose parameters to rule authors unless necessary:

```
... else if Something is XYZ then Action Two and ...
```
The `Action Three` method takes a single parameter of type `System.Int32`:

```
... else Action Three(123)
```