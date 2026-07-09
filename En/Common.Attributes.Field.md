
# FieldAttribute Class

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Attributes`</small></em>

## Summary

When applied to a public, non-static [value-type](/decision-automation/business-rules-data-types) property or field of the [source object](/decision-automation/business-rule-source-object) type (a qualified property), this attribute references that member as a rule field.

## Syntax

```csharp
[AttributeUsage(AttributeTargets.Property | AttributeTargets.Field, AllowMultiple = false, Inherited = true)]
public class FieldAttribute : System.Attribute,
	CodeEffects.Rule.Common.Attributes.IDescribableAttribute,
	CodeEffects.Rule.Common.Attributes.ISettingsAttribute
```

## Properties

<div class="members">

- ### AllowCalculations<br/>
	<small>Type: `System.Bool`</small>

	Used only for rule fields of [numeric type](/decision-automation/business-rules-data-types). Ignored for fields of other types. Gets or sets a value that indicates whether the value element of this field can be expressed as a calculation. When creating a rule condition with a numeric field or in-rule method, rule authors must choose between selecting another numeric field, entering a number directly (Enter a number...), or adding a calculation (Add a calculation...) before assigning the field’s value. If this property is set to `false`, the Add a calculation... option is removed from rule menus for numeric fields. This property is optional. The default value is `true`.
	
	![Numeric Calculation](/resources/images/doc/editorcalcfield.png)

- ### CollectionItemName<br/>
	<small>Type: `System.String`</small>

	Used only for rule fields of [collection type](/decision-automation/business-rules-collection-data-types) and ignored for fields of other types. Gets or sets the display name of the single type in collection when it's displayed in the Rule Area. This property is optional. The default value is the declared name of the property.

	```csharp
	[Field(CollectionItemName = "Visit")]
	public ICollection<Visit> Visits { get; set; } = new List<Visit>();
	```

	![Collection item](/resources/images/doc/editorcollmenuitem.png)

- ### CollectionItemType<br/>
	<small>Type: `System.Type`</small>

	Used only for rule fields of non-generic [collection types](/decision-automation/business-rules-collection-data-types) whose underlying type cannot be determined at run time (qualified collections). Ignored for fields of other types and for generic collections. Gets or sets the underlying type of a qualified collection. Examples of qualified collections include `System.Collections.Specialized.StringCollection` and `System.Collections.ArrayList`. This property is optional. A qualified collection is ignored if this property is not set.

- ### DataSourceName<br/>
	<small>Type: `System.String`</small>

	Used only for rule fields that represent source object properties of the `System.Int` type and ignored for fields of other types. Gets or sets the unique name of the [dynamic menu data source](/decision-automation/business-rules-dynamic-menu-data-sources) declared by the [`SourceAttribute`](/decision-automation/rule-common-attributes-source) on the [source object](/decision-automation/business-rule-source-object).

- ### DateTimeFormat<br/>
	<small>Type: `System.String`</small>

	Used only for rule fields of date and time [types](/decision-automation/business-rules-data-types) and ignored for fields of other types. Gets or sets the date or time format of the field’s value as displayed in the Rule Editor. Standard .NET date and time format strings are supported. This property is not used during rule evaluation. This property is optional. The default value for date fields is `MMM dd, yyyy`, and for time fields is `hh:mm tt`.

	**<em>Remarks</em>**

	The Rule Editor uses a built-in date picker for fields of the `DateTime` type. If the `DateTimeFormat` value includes the time fraction, the date picker includes time selection controls and an <b>OK</b> button, which closes the picker and sets the selected value in the Rule Area. If the format omits the time fraction, the picker includes no time controls. It closes automatically and sets the value when the user clicks the desired date in the calendar.

	If the time fraction is omitted, the `Evaluator` adds a *zero* time component to the date and uses the resulting value `yyyy-MM-ddT00:00:00.0000` during rule evaluation.

	```csharp
	[Field(
		Description = "Date of Birth of the Patient",
		DisplayName = "Date of Birth",
		DateTimeFormat = "yyyy.MM.dd")]
	public DateTime DOB { get; set; } = DateTime.MinValue;
	```

	![Collection item](/resources/images/doc/editordatepickernotime.jpg)<br>
	![Collection item](/resources/images/doc/editordatepickernotime2.jpg)

	```csharp
	[Field(
		Description = "Date of Birth of the Patient",
		DisplayName = "Date of Birth",
		DateTimeFormat = "MMM dd, yyyy hh:mm:ss tt")]
	public DateTime DOB { get; set; } = DateTime.MinValue;
	```

	![Collection item](/resources/images/doc/editordatepickertime.png)<br>
	![Collection item](/resources/images/doc/editordatepickertime2.png)

- ### Description<br/>
	<small>Type: `System.String`</small>

	Gets or sets the description of the rule field. Rule authors can view this description by hovering the mouse pointer over the field element in the Rule Area or in the field menu. The value of this property is ignored if `Control.ShowDescriptionsOnMouseHover` is set to `false`. It is not recommended to use this property in [multilingual applications](/decision-automation/business-rule-multilingual-support); instead, use culture-specific custom [Source XML](/decision-automation/business-rule-source-object-xml) documents. This property is optional.

	```csharp
	[Field(Description = "Date of Birth of the Patient")]
	public DateTime DOB { get; set; } = DateTime.MinValue;
	```
	![Collection item](/resources/images/doc/editorhelpstrdesc.png)<br/><br/>
	![Collection item](/resources/images/doc/editorfielddesc.png)

- ### DisplayName<br/>
	<small>Type: `System.String`</small>

	Gets or sets the display name of the rule field in the [Rule Editor](/decision-automation/business-rule-editor). Use this property to represent rule fields in the Rule Area when the property name is not descriptive enough for non-technical rule authors. It is not recommended to use this property in [multilingual applications](/decision-automation/business-rule-multilingual-support); instead, use culture-specific custom [Source XML](/decision-automation/business-rule-source-object-xml) documents. This property is optional. The default value is the declared name of the property.

- ### Gettable<br/>
	<small>Type: `System.Bool`</small>

	Gets or sets a value that indicates whether this field appears in the Rule Editor’s context menus that list available rule fields and [in-rule methods](/decision-automation/rule-common-attributes-method) for rule conditions. The default value is `true`.

- ### Group<br/>
	<small>Type: `System.String`</small>

	Gets or sets the name of the menu group. This property is optional.

	**<em>Remarks</em>**
	
	Your source object may declare dozens or even hundreds of rule fields and in-rule methods. In such cases, the field menus in the Rule Area could contain an overwhelming number of items and become difficult to use. Grouping allows you to organize fields into groups, which the Rule Editor uses to present them in two levels of menus: first, it displays a list of all available groups, and then, when the user selects a group, it shows a new menu containing only the items from that group.

	This feature is automatically enabled if at least one rule field or in-rule method in the [source object](/decision-automation/business-rule-source-object) has a group name set. The group name can be any string value. When grouping is enabled, all rule fields that do not have a group assigned are placed in a default group called `OTHER`, which is displayed at the bottom of the menu. You can rename the default group using the [Help XML](/decision-automation/business-rule-multilingual-support) feature.

- ### Filter<br/>
	<small>Type: `System.String`</small>

	Gets or sets the name of the menu filter. This property is optional.

	**<em>Remarks</em>**

	Use this property to instruct the Rule Editor to allow value comparisons in rule conditions based on the filter value. For example, consider a source object that declares four properties of the `System.String` type:

	```csharp
	using CodeEffects.Rule.Common.Attributes;

	namespace TestLibrary
	{
		public class Thing
		{
			[Field(DisplayName = "Work Email", Max = 200)]
			public string WorkEmail { get; set; }

			[Field(DisplayName = "Home Email", Max = 200)]
			public string HomeEmail { get; set; }

			[Field(DisplayName = "Work Phone", Max = 10)]
			public string WorkPhone { get; set; }

			[Field(DisplayName = "Home Phone", Max = 10)]
			public string HomePhone { get; set; }
		}
	}
	```
	With this source object, a user could create a condition in which the phone value is compared to the email value. Although both fields are of the `System.String` type, they clearly represent unrelated data:

	![Collection item](/resources/images/doc/filterone.png)

	Let’s separate these fields into two filters: <b>email</b> and <b>phone</b>:

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
		}
	}
	```
	Now, users can compare email addresses only to other email addresses, and phone numbers only to other phone numbers:

	![Collection item](/resources/images/doc/filtertwo.png)

- ### IncludeInCalculations<br/>
	<small>Type: `System.Bool`</small>

	Used only for rule fields of [numeric type](/decision-automation/business-rules-data-types) and ignored for fields of other types. Gets or sets a value that indicates whether the rule field can be included in value calculations of other fields and in-rule methods. For example, it may be confusing for rule authors when a non-financial numeric field, such as an *ID*, appears in a calculation menu alongside financial fields like *Income* or *Debt*. In such cases, set `IncludeInCalculations` to `false`. This property is optional. The default value is `true`.

- ### Max<br/>
	<small>Type: `System.Int64`</small>

	Used only with numeric and string value elements and ignored for [other types](/decision-automation/business-rules-data-types). For numeric elements, gets or sets the maximum value that rule authors can enter manually. For string elements, gets or sets the maximum length of the string that rule authors can enter as the value. This property is used only by the Rule Editor and is not used during rule evaluation. This property is optional.

	Max value for numeric types allowed by the editor: `2147483647`<br/>
	Max length for string types allowed by the editor: `256`

- ### Min<br/>
	<small>Type: `System.Int64`</small>

	Used only for rule fields of numeric type and ignored for fields of [other types](/decision-automation/business-rules-data-types). Gets or sets the minimum value that rule authors can enter manually. This property is used only by the Rule Editor and is not used during rule evaluation. This property is optional. The minimum value allowed by the editor is `-2147483648`.

- ### Settable<br/>
	<small>Type: `System.Bool`</small>

	Gets or sets a value that indicates whether the value of the rule field can be assigned in [execution-type](/decision-automation/business-rule-execution-type) rules. In general, the Rule Editor recognizes read-only fields and properties with private setters and does not allow rule authors to assign values to them. Setting this property to `false` prevents rule authors from assigning a value to the field or property, regardless of the .NET setter declared for that property. The default value is `true`.

- ### StringComparison<br/>
	<small>Type: `System.StringComparison`</small>

	Used only for rule fields of [string type](/decision-automation/business-rules-data-types) and ignored for other types. Gets or sets the string comparison option that determines how string values are compared in rule conditions during evaluation. This property can be used to specify culture-specific comparison options, including case sensitivity. The default value is `System.StringComparison.OrdinalIgnoreCase`.

	**<em>Remarks</em>**

	This property is ignored by the rule-based [data filtering](/decision-automation/business-rule-data-filtering) feature. Use your database's collation settings to define string comparison behavior for all SELECT operations when using Code Effects with T-SQL LINQ providers such as **Entity Framework**.

- ### ValueInputType<br/>
	<small>Type: `CodeEffects.Rule.Common.Models.ValueInputType`</small>

	Gets or sets a value that specifies whether the field’s value can be entered manually, selected from other fields, or both. For example, consider the rule condition `FirstName = John`. In this case, the value `John` is a string typed by the rule author — a user-entered value of type `ValueInputType.User`. The same condition can also be expressed as `FirstName = MiddleName`. Since `MiddleName` is a field, this value is selected by the rule author as a field item from the menu — a value of type `ValueInputType.Fields`. This property is optional. The default value is `ValueInputType.All`, which allows both `User` and `Fields` input types.

</div>