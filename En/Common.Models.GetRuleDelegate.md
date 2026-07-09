
# GetRuleDelegate Delegate

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Models`</small></em>

## Summary

Defines the signature of a public method that takes a rule ID as its single parameter and returns the corresponding [Rule XML](/decision-automation/business-rules-storage) as `System.Strng` value.

## Syntax

```csharp
public delegate string GetRuleDelegate(string ruleId);
```

## Parameters

- ### RuleID<br/>
	<small>Type: `System.String`</small>

	The ID of the rule to be returned as a Rule XML string.

## Returns

<small>Type: `System.String`</small>

The string representaion of the [Rule XML](/decision-automation/business-rules-storage).

## Remarks

During [rule evaluation](/decision-automation/business-rule-evaluation), the engine may encounter references to one or more rules within the [Rule XML](/decision-automation/business-rules-storage) being processed. For example, a rule may include [reusable rules](/decision-automation/business-rules-reusable) or use the **“If exists collection item where…”** logic expressed as a separate rule (see the code example below). In most cases, if a rule references other rules by their IDs, it includes the definitions of those rules in its own Rule XML. However, you may create Rule XML documents dynamically, split rule definitions across multiple files, or consume rules produced by other systems where rule definitions may reside in other files or data storage.

In such scenarios, a Rule XML document that references other rules may contain only their IDs, not their definitions. The `GetRuleDelegate` delegate enables the engine or [editor](/decision-automation/business-rule-editor) to locate and retrieve these referenced rules while reading the main rule. Several overloads of the [`Evaluator`](/decision-automation/rule-engine-evaluator) class accept this delegate, and it is also used by certain overloads of the [`Control.ToString()`](/decision-automation/rule-editor-control) method.

```xml
<?xml version="1.0" encoding="utf-8"?>
<codeeffects
		xmlns="https://codeeffects.com/schemas/rule/41"
		xmlns:ui="https://codeeffects.com/schemas/ui/4">

	<rule
			id="08373af7-da1f-409c-bf4b-793506404f50"
			webrule="5.5.0.3"
			utc="2025-10-15T23:41:56.7853"
			type="...type full name..."
			eval="true">
		<name>Test</name>
		<definition>
			<rule id="96fe93d6-c5d3-4842-a55c-1395acf7b2c9" operator="exists">
				<property name="Visits" />
			</rule>
		</definition>
		<format>
			<lines />
		</format>
	</rule>

	<rule
			id="96fe93d6-c5d3-4842-a55c-1395acf7b2c9"
			webrule="5.5.0.3"
			utc="2025-10-15T23:41:56.7853"
			type="...type full name..."
			eval="true">
		<definition>
			<condition type="equal">
				<property name="PhysicianID" />
				<value type="numeric">3</value>
			</condition>
		</definition>
		<format>
			<lines />
		</format>
	</rule>

</codeeffects>
```

The following image shows the Rule XML from the example above loaded into the Rule Area:

![Numeric Calculation](/resources/images/doc/delegate1.png)