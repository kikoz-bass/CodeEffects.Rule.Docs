
# DataSourceHolder Class

<em><small>**Assembly:** CodeEffects.Rule.Editor.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Editor.Models`</small></em>

## Summary

This class is used to hold named instances of [`GetDataSourceDelegate`](/decision-automation/rule-editor-models-getdatasourcedelegate) types utilized by custom [Dynamic Menu Data Sources](/decision-automation/business-rules-dynamic-menu-data-sources).

## Syntax

```csharp
public class DataSourceHolder
```

## Properties

<div class="members">

- ### Name<br/>
	<small>Type: `System.String`</small>

	Gets or sets the unique names of custom Dynamic Menu Data Sources used by the [`FieldAttribute`](/decision-automation/rule-common-attributes-field), [`ReturnAttribute`](/decision-automation/rule-common-attributes-return), and [`ParameterAttribute`](/decision-automation/rule-common-attributes-parameter).

- ### Delegate<br/>
	<small>Type: [`CodeEffects.Rule.Editor.Models.GetDataSourceDelegate`](/decision-automation/rule-editor-models-getdatasourcedelegate)</small>

	Gets or sets the signature of a method that takes no parameters and returns a collection of [`DataSourceItem`](/decision-automation/rule-editor-client-datasourceitem) used by the custom [Dynamic Menu Data Sources](/decision-automation/business-rules-dynamic-menu-data-sources) feature.

</div>