
# MethodAttribute Class

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Attributes`</small></em>

## Summary

**IMPORTANT** Generic methods with generic params cannot be used in [Source XML](/decision-automation/business-rule-source-object-xml) as in-rule methods or rule actions. Use plain [.Net class(es)](/decision-automation/business-rule-source-object-class) if your [source object](/decision-automation/business-rule-source-object) declares generic methods.

If applied to a qualified public method of the source object, this attribute marks that method as an [in-rule method](/decision-automation). To qualify, the method must return a value type and either be parameterless or declare only parameters of the source object type or any [value type](/decision-automation/business-rules-data-types) supported by Code Effects. This attribute can also be applied to a qualified method of any other public class to make it available as an in-rule method. In that case, the source object must be decorated with the [`ExternalMethodAttribute`](/decision-automation/rule-common-attributes-externalmethod), which references the external method by name and class type.

## Syntax

```csharp
[AttributeUsage(AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
public class MethodAttribute : System.Attribute, CodeEffects.Rule.Common.Attributes.IDescribableAttribute
```

## Properties

<div class="members">

- ### Description<br/>
	<small>Type: `System.String`</small>

	Gets or sets the description of the in-rule method. Rule authors can view this description by hovering the mouse pointer over the in-rule method element in the Rule Editor. The value of this property is ignored if `Control.ShowDescriptionsOnMouseHover` is set to `false`. It is not recommended to use this property in [multilingual applications](/decision-automation/business-rule-multilingual-support); instead, use culture-specific custom [Source XML](/decision-automation/business-rule-source-object-xml) documents. This property is optional.

- ### DisplayName<br/>
	<small>Type: `System.String`</small>

	Gets or sets the display name of the in-rule method in the Rule Editor. Use this property to represent in-rule methods when the method name is not descriptive enough for non-technical rule authors or when several overloads of the same method need to appear as different in-rule methods. It is not recommended to use this property in [multilingual applications](/decision-automation/business-rule-multilingual-support); instead, use culture-specific custom [Source XML](/decision-automation/business-rule-source-object-xml) documents. This property is optional. The default value is the declared name of the qualified method.

- ### Group<br/>
	<small>Type: `System.String`</small>

	Gets or sets the name of the menu group. This property is optional.

	**<em>Remarks</em>**

	Your source object may declare dozens or even hundreds of rule fields and in-rule methods. In such cases, the field menus in the Rule Area could contain an overwhelming number of items and become difficult to use. Grouping allows you to organize fields and in-rule methods into groups, which the Rule Editor uses to present them in two levels of menus: first, it displays a list of all available groups, and then, when the user selects a group, it shows a new menu containing only the items from that group.

	This feature is automatically enabled if at least one rule field or in-rule method in the [source object](/decision-automation/business-rule-source-object) has a group name set. The group name can be any string value. When grouping is enabled, all rule fields and in-rule methods that do not have a group assigned are placed in a default group called `OTHER`, which is displayed at the bottom of the menu. You can rename the default group using the [Help XML](/decision-automation/business-rule-multilingual-support) feature.

- ### Filter<br/>
	<small>Type: `System.String`</small>

	Gets or sets the name of the menu filter. This property is optional.

	**<em>Remarks</em>**

	Use this property to instruct the Rule Editor to allow value comparisons in rule conditions based on the filter value. For example, consider a source object that declares two in-rule methods returning the `System.String` type and two fields of the `System.String` type:

	```csharp
	[Method(DisplayName = "Home Phone")]
	[return:Return(Max = 10)]
	public string GetHomePhone()
	{
		return MyCRM.GetPhoneNumber(PhoneTypeEnum.Home);
	}

	[Field(DisplayName = "Work Phone", Max = 10)]
	public string WorkPhone { get; set; }

	[Method(DisplayName = "Home Email")]
	[return: Return(Max = 200)]
	public string GetHomeEmail()
	{
		return MyCRM.GetEmail(EmailTypeEnum.Home);
	}

	[Field(DisplayName = "Work Email", Max = 200)]
	public string WorkEmail { get; set; }
	```

	With this source object, a user could create a condition in which the phone value is compared to the email value. Although all fields and in-rule methods are of the `System.String` type, they clearly represent unrelated data:

	![Collection item](/resources/images/doc/filterthree.png)

	Let’s separate these properties and in-rule methods into two filters: <b>email</b> and <b>phone</b>:

	```csharp
	[Method(DisplayName = "Home Phone", Filter = "phone")]
	[return:Return(Max = 10)]
	public string GetHomePhone()
	{
		return MyCRM.GetPhoneNumber(PhoneTypeEnum.Home);
	}

	[Field(DisplayName = "Work Phone", Max = 10, Filter = "phone")]
	public string WorkPhone { get; set; }

	[Method(DisplayName = "Home Email", Filter = "email")]
	[return: Return(Max = 200)]
	public string GetHomeEmail()
	{
		return MyCRM.GetEmail(EmailTypeEnum.Home);
	}

	[Field(DisplayName = "Work Email", Max = 200, Filter = "email")]
	public string WorkEmail { get; set; }
	```

	Now, users can compare email addresses only to other email addresses, and phone numbers only to other phone numbers:

	![Collection item](/resources/images/doc/filterfour.png)

- ### IncludeInCalculations<br/>
	<small>Type: `System.Bool`</small>

	Used only for in-rule methods that return [numeric types](/decision-automation/business-rules-data-types) and ignored for in-rule methods of other return types. Gets or sets a value that indicates whether the in-rule method can be included in value calculations of other fields and in-rule methods. For example, it may be confusing for rule authors when an in-rule method that returns a non-financial value appears in a calculation menu alongside financial fields like *Income* or *Debt*. In such cases, set `IncludeInCalculations` to `false`. This property is optional. The default value is `true`.

</div>

## Remarks

Rule Editor uses any qualified method of the source object as an in-rule method unless it is decorated with the [`ExcludeFromEvaluationAttribute`](/decision-automation/rule-common-attributes-excludefromevaluation). If this attribute is applied to a non-qualified method, an exception is thrown. All non-qualified methods that are not decorated with the `MethodAttribute` are ignored.

Remember that static or instance in-rule methods can accept your [source object](/decision-automation/business-rule-source-object) as a parameter. When a rule author uses such a method in a rule, parameters of the source object type are not displayed — Rule Editor passes them automatically. In the [Rule XML](/decision-automation/business-rules-storage), the parameter of the source object type is represented as `this`.

## Example

```csharp
using CodeEffects.Rule.Common.Attributes;

namespace TestLibrary
{
	public class Person
	{
		[Field(DisplayName = "First name", Max = 30)]
		public string FirstName { get; set; }

		[Field(DisplayName = "Last name", Max = 30)]
		public string LastName { get; set; }

		[Method("Full name", Description = "Concatenates the first and last names into a single string.")]
		public string GetFullName()
		{
			return $"{LastName}, {FirstName}";
		}
	}
}
```