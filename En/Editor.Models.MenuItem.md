
# MenuItem Class

<em><small>**Assembly:** CodeEffects.Rule.Editor.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Editor.Models`</small></em>

## Summary

This class is used to hold rule items in the `ContextMenuRules` and `ToolBarRules` collections of the [`Control`](/decision-automation/rule-editor-control) class.

## Syntax

```csharp
public class  MenuItem
```

## Properties

<div class="members">

- ### Description<br/>
	<small>Type: `System.String`</small>

	Gets or sets the description of the menu item. If set, the [Help String](/decision-automation/business-rule-editor) displays this description when the menu item is selected. This property is optional.

- ### DisplayName<br/>
	<small>Type: `System.String`</small>

	Gets or sets the display name of the menu item. This property is required.

- ### ID<br/>
	<small>Type: `System.String`</small>

	Gets or sets the ID of the menu item. This property is required.

</div>