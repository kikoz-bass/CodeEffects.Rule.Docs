
# Collection Class

<em><small>**Assembly:** CodeEffects.Rule.Editor.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Editor.Client`</small></em>

> <span style="color:red;">This page is currently being updated to reflect the features and API changes introduced in Code Effects Decision Platform 6. Please check back in a few days for the completed documentation.</span>

## Summary

Provides a way to specify collection-related settings for [Fields](/decision-automation/rule-editor-client-field), [Parameters](/decision-automation/rule-editor-client-parameter), and [Return](/decision-automation/rule-editor-client-return) values of [collection types](/decision-automation/business-rules-collection-data-types) in [Adaptive Source](/decision-automation/business-rule-adaptive-source).

> This class is intended exclusively for use with the Adaptive Source model.

## Syntax

```csharp
public class Collection
```

## Properties

<div class="members">

- ### ComparisonName<br/>
	<small>Type: `System.String`</small>

	The generic type definition name of the collection. The [Rule Editor](/decision-automation/business-rule-editor) uses this value to determine whether the collection can be compared with other collections defined in the [Source](/decision-automation/rule-editor-models-source), hence the name of this property. You can obtain this value from the type of your collection field, parameter, or return value by executing the following code:

	```csharp
	Type constructed = typeof(ICollection<MyType>).GetGenericTypeDefinition();
	var name = genericDef.FullName; // "System.Collections.Generic.ICollection`1"
	```

- ### DataType<br/>
	<small>Type: [CodeEffects.Rule.Common.Models.DataType](/decision-automation/rule-common-models-datatype)</small>

	The data type of the underlying class of the collection. This value should be only used in value-typed collections. The default value is `DataType.None`.

	#### Example

	```csharp
	using CodeEffects.Rule.Common.Models;
	using CodeEffects.Rule.Editor.Client;

	// Field of List<string> type (value-typed collection)
	var field = new Field
	{
		Value = "Student.Nicknames",
		DisplayName = "List of nicknames of the student",
		DataType = DataType.Collection,
		Collection = new Collection
		{
			ComparisonName = "System.Collections.Generic.List`1",
			DataType = DataType.String,
			IsArray = false,
			IsGeneric = true,
			IsUnderlyingTypeNullable = true,
			Type = CollectionType.Value,
			UnderlyingTypeName = "System.String"
		}
	};
	```

- ### UnderlyingTypeName<br/>
	<small>Type: `System.String`</small>

	The assembly-qualified name or [alias](/decision-automation/business-rule-type-handling-and-tyle-alias-names) of the collection's underlying type. This property is ignored for [generic collections](/decision-automation/business-rules-collection-data-types).

	#### Example

	```csharp
	using CodeEffects.Rule.Common.Models;
	using CodeEffects.Rule.Editor.Client;

	// Field of List<Student> type (reference-typed collection)
	var field = new Field
	{
		Value = "Student.Schools",
		DisplayName = "List of schools attended by the student",
		DataType = DataType.Collection,
		Collection = new Collection
		{
			ComparisonName = "System.Collections.Generic.List`1",
			DataType = DataType.None, // No data types in reference collections
			IsArray = false,
			IsGeneric = true,
			DisplayName = "School",
			IsUnderlyingTypeNullable = true,
			Type = CollectionType.Reference, // Type of this collection - value, reference, or generic
			UnderlyingTypeName = "TestProject.People.Student, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"
		}
	};
	```

</div>