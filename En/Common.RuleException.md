
# RuleException Class

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common`</small></em>

## Summary

Provides the base class for all rule-related exception types used by both the [Rules Engine](/decision-automation/business-rule-evaluation) and the [Rule Editor](/decision-automation/business-rule-editor).

## Syntax

```csharp
public class RuleException : System.Exception
```

## Inheritance

- `System.Object`
	- `System.Exception`
		- **CodeEffects.Rule.Common.RuleException**
			- [`CodeEffects.Rule.Editor.InvalidRuleException`](/decision-automation/rule-editor-invalidruleexception)
			- [`CodeEffects.Rule.Editor.MalformedXmlException`](/decision-automation/rule-editor-malformedxmlexception)
			- [`CodeEffects.Rule.Editor.SourceException`](/decision-automation/rule-editor-sourceexception)
			- [`CodeEffects.Rule.Engine.EvaluationException`](/decision-automation/rule-engine-evaluationexception)