
# ParentAttribute Class

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Attributes`</small></em>

## Summary

Gets or sets the display name of the public non-static property of a [value type](/decision-automation/business-rules-data-types) or of a field of a reference type declared in the [source object](/decision-automation/business-rule-source-object).

## Syntax

```csharp
[AttributeUsage(AttributeTargets.Property | AttributeTargets.Field, AllowMultiple = true, Inherited = false)]
public class ParentAttribute : System.Attribute, CodeEffects.Rule.Common.Attributes.IDescribableAttribute
```

## Properties

<div class="members">

- ### Description<br/>
	<small>Type: `System.String`</small>

	Gets or sets the description of the rule field. Rule authors can see this description when they hover the mouse pointer over the field element in the [Rule Editor](/decision-automation/business-rule-editor). The value of this property is ignored if `Control.ShowDescriptionsOnMouseHover` is set to `false`. It is not advisable to use this property in [multilingual applications](/decision-automation/business-rule-multilingual-support); instead, use culture-specific custom [Source XML](/decision-automation/business-rule-source-object-xml) documents. This property is optional.

- ### DisplayName<br/>
	<small>Type: `System.String`</small>

	Gets or sets the display name of the rule field in the Rule Editor. Use this property to represent rule fields in the Rule Area when the property name is not sufficiently descriptive for non-technical rule authors. It is not recommended to use this property in [multilingual applications](/decision-automation/business-rule-multilingual-support); instead, use culture-specific custom [Source XML](/decision-automation/business-rule-source-object-xml) documents. This property is optional. The default value is the declared name of the property.

- ### ParentName<br/>
	<small>Type: `System.String`</small>

	Gets or sets the name of the **parent** property declared in the [source object](/decision-automation/business-rule-source-object). See the remarks below for details on this feature.

</div>

## Remarks

If a source object contains two or more reference-type properties, those properties can be of the same type. For example, consider a `Company` class that declares two properties of type `Address`: `ShippingAddress` and `BillingAddress`:

```csharp
namespace TestLibrary
{
	public class Company
	{
		public Address ShippingAddress { get; set; }
		public Address BillingAddress { get; set; }
	}

	public class Address
	{
		public string Street { get; set; }
		public string City { get; set; }
		public string PostalCode { get; set; }
	}
}
```

Code Effects Rule Editor uses dot notation to display the default field names of reference types in the Rule Area. If the `Company` class is used as the source object, the fields menu will appear as follows:

![Parent Attribute](/resources/images/doc/editorparent1.png)

While this may be acceptable for internal solutions, you will likely want to assign meaningful display names to the rule fields to make the experience easier for business users. However, simply setting the `DisplayName` parameter of the [`FieldAttribute`](/decision-automation/rule-common-attributes-field) on each `Address` field...

```csharp
[Field(DisplayName = "Street Address")]
public string Street { get; set; }
```
…won’t work because Rule Editor cannot determine which property should be labeled **Street Address** when it scans the source object while preparing the Rule Area — `ShippingAddress.Street` or `BillingAddress.Street`. If the `Street` property is modified as shown in the example above, Rule Editor will simply use the first property of the `Company` class that contains the field `Street` (the `ShippingAddress` property in this case) and ignore all other occurrences. This is the default behavior.

Use the `ParentAttribute` to resolve this issue. Its behavior is easy to understand from the code sample below:

```csharp
using CodeEffects.Rule.Common.Attributes;

namespace TestLibrary
{
	public class Company
	{
		public Address ShippingAddress { get; set; }
		public Address BillingAddress { get; set; }
	}

	public class Address
	{
		[Parent("ShippingAddress", "Shipping Street")]
		[Parent("BillingAddress", "Billing Street")]
		public string Street { get; set; }

		[Parent("ShippingAddress", "Shipping City")]
		[Parent("BillingAddress", "Billing City")]
		public string City { get; set; }

		[Parent("ShippingAddress", "Shipping Postal Code")]
		[Parent("BillingAddress", "Billing Postal Code")]
		public string PostalCode { get; set; }
	}
}
```

Run the editor with this version of the `Company` class as the source object to see the difference:

![Parent Attribute](/resources/images/doc/editorparent2.png)