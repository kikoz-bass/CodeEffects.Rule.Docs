
# RuleModel Class

<em><small>**Assembly:** CodeEffects.Rule.Editor.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Editor.Models`</small></em>

## Summary

Provides the data model for [authoring](/decision-automation/business-rule-management) and validating complex business rules.

## Syntax

```csharp
public class RuleModel
```

## Properties

<div class="members">

- ### Desc<br/>
	<small>Type: `System.String`</small>

	Gets or sets the description of the rule. This value is typically provided by the rule author in the <b>Description</b> text box of the [Toolbar](/decision-automation/business-rule-toolbar). This property is optional.

- ### ID<br/>
	<small>Type: `System.String`</small>

	Gets or sets the <b>ID</b> of the rule being handled. Code Effects automatically assigns unique identifiers to all new rules using string representations of `System.Guid`. These IDs are stored in the [Rule XML](/decision-automation/business-rules-storage). You should override this value only if your system uses its own rule identification scheme.

- ### IsEvalType<br/>
	<small>Type: `System.Nullable<Boolean>`</small>

	Gets or sets a value indicating whether the rule being handled is of [evaluation type](/decision-automation/business-rule-evaluation-type). A value of `false` means that the rule is of [execution type](/decision-automation/business-rule-execution-type). This property is useful when different rule types are stored in separate locations. A value of `null` indicates that Code Effects cannot determine the rule type.

- ### Name<br/>
	<small>Type: `System.String`</small>

	Gets or sets the name of the rule. This value is typically provided by the rule author in the <b>Name</b> text box of the [Toolbar](/decision-automation/business-rule-toolbar). This property is required only if the Toolbar is visible.

- ### SkipNameValidation<br/>
	<small>Type: `System.Boolean`</small>

	Gets or sets a value indicating whether Code Effects should skip validation of the rule name when the rule author saves or updates a rule. The default value is `false`.

</div>