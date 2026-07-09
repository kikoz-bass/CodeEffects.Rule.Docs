
# DataSourceItem Class

<em><small>**Assembly:** CodeEffects.Rule.Editor.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Editor.Client`</small></em>

## Summary

Represents a menu item used by the [Dynamic Menu Data Sources](/decision-automation/business-rules-dynamic-menu-data-sources) feature.

## Syntax

```csharp
public class DataSourceItem : IFilterable
```

## Properties

<div class="members">

- ### ID<br/>
	<small>Type: `System.Int32`</small>

	Gets or sets the numeric ID of the menu item used by the Dynamic Menu Data Sources. See *Remarks* for details on the use of this property.

- ### Key<br/>
	<small>Type: `System.String`</small>

	Gets or sets the string ID (key) of the menu item used by the Dynamic Menu Data Sources. See *Remarks* for details on the use of this property.

- ### Name<br/>
	<small>Type: `CodeEffects.Rule.Common.Models.GetDataSourceDelegate`</small>

	Gets or sets the signature of a method that takes no parameters and returns a collection of `DataSourceItem` objects used by the Dynamic Menu Data Sources.

- ### Filter<br/>
	<small>Type: `System.String`</small>

	Gets or sets the string value used by the Dynamic Menu Data Sources feature to filter out all `DataSourceItem` menu items whose `Name` value does not match the specified filter value. This property works together with the `Filter` values of the [`FieldAttribute`](/decision-automation/rule-common-attributes-field), [`ParameterAttribute`](/decision-automation/rule-common-attributes-parameter), and [`MethodAttribute`](/decision-automation/rule-common-attributes-method) classes.

	```csharp
	using CodeEffects.Rule.Common.Attributes;
	using CodeEffects.Rule.Common.Models;

	namespace Test
	{
		[Data("Education", typeof(Util), "Schools")]
		public class Patient
		{
			[Field(DisplayName = "High School", DataSourceName = "Education", Filter = "school")]
			public string SchoolID { get; set; }

			[Field(DisplayName = "College", DataSourceName = "Education", Filter = "college")]
			public string CollegeID { get; set; }

			[Field(DisplayName = "University", DataSourceName = "Education", Filter = "university")]
			public string UniversityID { get; set; }
		}
		
		public class Util
		{
			public static List<DataSourceItem> Schools()
			{
				var schools = new List<DataSourceItem>();

				schools.Add(new DataSourceItem("One", "School # 1", "school"));
				schools.Add(new DataSourceItem("Two", "University # 2", "university"));
				schools.Add(new DataSourceItem("Three", "Third School", "school"));
				schools.Add(new DataSourceItem("Four", "College # 4", "college"));
				schools.Add(new DataSourceItem("Five", "College # 5", "college"));
				schools.Add(new DataSourceItem("Six", "University # 6", "university"));

				return schools;
			}
		}
	}
	```

	![Numeric Calculation](/resources/images/doc/editormenuitems1.png)

</div>

## Remarks

The `ID` and `Key` properties both represent the identifier of each `DataSourceItem` in your dynamic menus. The only difference between them is their type: `ID` is of type `System.Int32`, while `Key` is of type `System.String`. Both properties are used in the same way. The first version of Code Effects included only the numeric `ID` property, but many customers requested support for a string identifier as well. As a result, the `Key` property was added while keeping `ID` for backward compatibility. If both properties are set, the value of `Key` takes precedence.