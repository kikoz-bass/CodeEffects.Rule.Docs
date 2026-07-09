
# SourceAttribute Class

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Attributes`</small></em>

## Summary

When applied to a public class, marks that class as a source object. This attribute is optional. Source objects are a core element of Code Effects. See the [Source Object](/decision-automation/business-rule-source-object) topic for details.

## Syntax

```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Interface, AllowMultiple = false, Inherited = false)]
public class SourceAttribute : System.Attribute
```

## Properties

<div class="members">

- ### DeclaredMembersOnly<br/>
	<small>Type: `System.Bool`</small>

	Gets or sets a value indicating whether the [Rule Editor](/decision-automation/business-rule-editor) should use only the public members declared by the source object itself, ignoring any members inherited from its base class(es). The default value is `false`. Note that members of the `System.Object` class are ignored by Code Effects regardless of the value of this property. This property plays an important role in preserving attributes in auto-generated classes.

- ### MaxTypeNestingLevel<br/>
	<small>Type: `System.Int32`</small>

	[Rule Editor](/decision-automation/business-rule-editor) performs a recursive search of all reference-type members of the [source object](/decision-automation/business-rule-source-object) and includes their public [value-type](/decision-automation/business-rules-data-types) members that are not decorated with the [`ExcludeFromEvaluation`](/decision-automation/rule-common-attributes-excludefromevaluation) attribute as rule fields. The recursion depth for public value-type members is determined by this optional property. The default value is `4`. Setting this value to a high number may reduce the performance of the Rule Editor while it renders on a page.

- ### PersistTypeNameInRuleXml<br/>
	<small>Type: `System.Bool`</small>

	Gets or sets a value indicating whether the Rule Editor should store the name of the source object’s type in the [Rule XML](/decision-automation/business-rules-storage). The default value is `true`. Set this property to `false` if source objects are generated dynamically and their types are not known at design time.

- ### UseParentFullNamePaths<br/>
	<small>Type: `System.Bool`</small>

	Gets or sets a value indicating whether the [Rule Editor](/decision-automation/business-rule-editor) should use dot notation instead of the declared names of reference types when assigning `DisplayName` values to rule fields, in-rule methods, and rule actions during the recursive search for value-type properties and qualified methods. The default value is `false`.

	### Remarks

	Consider a plain `Parent` class with no Code Effects attributes applied:

	```csharp
	namespace HelloWorld
	{
		public class Parent
		{
			public Child Test { get; set; }
		}

		public class Child
		{
			public Address Home { get; set; }
			public Address Work { get; set; }
		}

		public class Address
		{
			public string Street { get; set; }
			public string Zip { get; set; }
		}
	}
	```
	If we run an imaginary web app with the Rule Editor and the `Parent` class as its source object, we would see the following fields menu:

	![Source Attribute](/resources/images/doc/editorsource1.png)

	This menu works as expected. However, you would likely want to display more readable names for your fields, even if they are buried deep within your source object. If we decorate this class with the `SourceAttribute`, set the value of its `UseParentFullNamePaths` property to `true`, decorate the members of the `Address` class with the [`ParentAttribute`](/decision-automation/rule-common-attributes-parent), and use **dot notation** to define the exact path of each reference type down to its value types ...

	```csharp
	using CodeEffects.Rule,Common.Attributes;

	namespace HelloWorld
	{
		[Source(UseParentFullNamePaths = true)]
		public class Patient
		{
			public Middle Test { get; set; }
		}

		public class Middle
		{
			public Address Home { get; set; }
			public Address Work { get; set; }
		}

		public class Address
		{
			[Parent("Test.Home", "Home Street")]
			[Parent("Test.Work", "Work Street")]
			public string Street { get; set; }

			[Parent("Test.Home", "Home Zip")]
			[Parent("Test.Work", "Work Zip")]
			public string Zip { get; set; }
		}
	}
	```

	... and then run the project again, we get the result we were looking for:

	![Source Attribute](/resources/images/doc/editorsource2.png)

	As you can see, this property works well in combination with `ParentAttribute.ParentName` when the structure of the source object is more complex than a single level of reference-type properties, or when the exact structure of the source is not known at design time. If the value of this property is set to `true`, you can use paths with any number of levels as the value of `ParentAttribute.ParentName`, such as `Test.One.Two.Three.Home`.

</div>