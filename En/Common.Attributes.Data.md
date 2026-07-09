
# DataAttribute Class

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Attributes`</small></em>

## Summary

Defines a single reusable [Dynamic Menu Data Source](/decision-automation/business-rules-dynamic-menu-data-sources) on a [source object](/decision-automation/business-rule-source-object) level.

## Syntax

```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Interface, AllowMultiple = true, Inherited = true)]
public class DataAttribute : System.Attribute
```

## Properties

<div class="members">

- ### Method<br/>
	<small>Type: `System.String`</small>

	Gets or sets the name of the method that provides the menu data source. This property is required. This must be a public parameterless method that returns an [`ICollection<DataSourceItem>`](/decision-automation/rule-editor-client-datasourceitem).

- ### Name<br/>
	<small>Type: `System.String`</small>

	Gets or sets the unique name of the data source, which can be used to distinguish this menu data source from all other menu data sources within the same source object. You can think of this property as a data source identifier that is unique only within the current source object. The uniqueness of each name must be ensured by the developer; Rule Editor does not enforce it. This property is required.

- ### Type<br/>
	<small>Type: `System.Type`</small>

	Gets or sets the type that declares the data source method. This property is required if the `TypeName` property is not specified.

- ### TypeOrAliasName<br/>
	<small>Type: `System.String`</small>

	Gets or sets the assembly-qualified name or [alias name](/decision-automation/business-rule-type-handling-and-tyle-alias-names) of the type that declares the data source method. This property is required if the `Type` property is not specified. You can obtain this name by executing `typeof(ClassName).AssemblyQualifiedName`.

</div>