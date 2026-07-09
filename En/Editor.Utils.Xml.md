
# Xml Class

<em><small>**Assembly:** CodeEffects.Rule.Editor.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Editor.Utils`</small></em>

## Summary

Provides helper methods for processing and managing [Rule XML](/decision-automation/business-rules-storage) and [Source XML](/decision-automation/business-rule-source-object-xml).

## Syntax

```csharp
public static partial class Xml
```

## Properties

- ### RuleNamespace<br/>
	<small>Type: `System.String`</small>

	Gets the location of the Rule XML schema and defines the rule namespace used by the current version of Code Effects.

- ### SourceNamespace<br/>
	<small>Type: `System.String`</small>

	Gets the location of the Source XML schema and defines the source namespace used by the current version of Code Effects.

- ### UiNamespace<br/>
	<small>Type: `System.String`</small>

	Gets the namespace and location of the XML schema for supporting attributes used by the rule schema in the current version of Code Effects.

## Methods

<div class="members">

- ### GetEmptyRuleDocument<br/>
	<small>Returns: `System.Xml.XmlDocument`</small>

	Returns an empty `XmlDocument` containing only the root `<codeeffects>` element. This document can serve as the starting point for a [Rule XML](/decision-automation/business-rules-storage) when generating XML files dynamically.

	#### Parameters

	<div class="params">

	- None.

	</div>

- ### GetEmptySourceDocument<br/>
	<small>Returns: `System.Xml.XmlDocument`</small>

	Returns an empty `XmlDocument` containing only the root `<codeeffects>` element. This document can serve as the starting point for a [Source XML](/decision-automation/business-rule-source-object-xml) when generating XML files dynamically.

	#### Parameters

	<div class="params">

	- None.

	</div>

- ### IsRuleValid<br/>
	<small>Returns: `System.Boolean`</small>

	Validates a [Rule XML](/decision-automation/business-rules-storage) document against the current schema and CodeEffects' internal validation logic.

	#### Parameters

	<div class="params">

	- *ruleXml* (`System.Xml.XmlDocument`) - A Rule XML document.

	</div>

</div>