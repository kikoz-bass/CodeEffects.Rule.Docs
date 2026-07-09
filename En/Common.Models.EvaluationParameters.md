
# EvaluationParameters Class

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Models`</small></em>

## Summary

Represents a parameter object used by the [rule evaluator](/decision-automation/rule-engine-namespace) classes during [rule evaluation](/decision-automation/business-rule-evaluation).

## Syntax

```csharp
public class EvaluationParameters
```

## Properties

<div class="members">

- ### AssemblyWhitelist<br/>
	<small>Type: `ICollection<System.String>`</small>

	Gets or sets the collection of trusted assembly names that are allowed to participate in [rule evaluation](/decision-automation/business-rule-evaluation). When specified, the evaluator only allows fields, properties, methods, and actions declared in the listed assemblies to be accessed or invoked. This property is optional. See the main [Assembly Whitelisting](/decision-automation/business-rules-assembly-whitelisting) article for details.

- ### LINQProviderType<br/>
	<small>Type: [`CodeEffects.Rule.Common.Models.LinqProviderType`](/decision-automation/rule-common-models-linqprovidertype)</small>

	Gets or sets the type of LINQ provider used when Code Effects builds expression trees during [data filtering](/decision-automation/business-rule-data-filtering) operations. The `Default` value indicates that the engine will attempt to detect the LINQ provider type automatically. Use `SQL` for LINQ-to-SQL providers and `Entities` for LINQ-to-Entities. This enumeration exists because various providers handle string operations differently, and in most cases it is difficult to automatically determine the behavior of the current provider. Theoretically, any LINQ provider is supported as long as it implements the same set of commands as LINQ-to-SQL.

- ### MaxIterations<br/>
	<small>Type: `System.Int32`</small>

	Gets or sets the maximum number of iterations allowed before the evaluation fails in [Loop mode](/decision-automation/business-rule-editor-modes). A value of `-1` indicates no limit.

- ### MidpointRounding<br/>
	<small>Type: `System.MidpointRounding`</small>

	Gets or sets the rounding algorithm. The default value is `System.MidpointRounding.ToEven`, also known as **round to even** or **banker’s rounding**. This is the default .NET rounding algorithm. For traditional rounding, set this property to `MidpointRounding.AwayFromZero`. Rounding is applied <b>only in comparison operators</b> using the `Math.Round()` method. Set the value of `Precision` property to `-1` to disable rounding.

- ### PerformNullChecks<br/>
	<small>Type: `System.Boolean`</small>

	Gets or sets a value indicating whether the engine includes null-pointer safety checks in the compiled code to prevent run-time exceptions. The default value is `true`.

- ### Precision<br/>
	<small>Type: `System.Int32`</small>

	Gets or sets the number of fractional digits to which decimal or double values are rounded. Rounding is applied <b>only in comparison operators</b> using the `Math.Round()` method. A value of `-1` disables rounding.

- ### PreserveWhitespace<br/>
	<small>Type: `System.Boolean`</small>

	Gets or sets a value indicating whether the [rule editor](/decision-automation/business-rule-editor) preserves white spaces in string values in the [Rule XML](/decision-automation/business-rules-storage) during rule generation. By default, the rule editor trims all string values and reduces consecutive white spaces to a single space. For example, if this property is set to `false`, the string value " My name is John " is saved in the Rule XML as "My name is John". The default value is `false`.

- ### RuleID<br/>
	<small>Type: `System.String`</small>

	Gets or sets the ID of the rule to be evaluated or compiled. This property is optional.

- ### RuleResolver<br/>
	<small>Type: [`CodeEffects.Rule.Core.GetRuleDelegate`](/decision-automation/rule-common-models-getruledelegate)</small>

	Delegate used to request missing rules — rules that are referenced but not included in the current rule set. This property is optional.

	#### Remarks

	In some cases, such as rules that use [reference-typed collections and arrays](/decision-automation/business-rules-collection-data-types), the [Rule Editor](/decision-automation/business-rule-editor) creates separate rule nodes in [RuleXML](/decision-automation/business-rules-storage) for each referenced collection because its `ElementType` is essentially another [source object](/decision-automation/business-rule-source-object) for the rule. Those rule nodes are included in the resulting RuleXML of the main rule, so the `RuleResolver` delegate is not needed in that scenario.

	However, if, for instance, you use the [reusable rules](/decision-automation/business-rules-reusable) feature and do not manually add to the main Rule XML all reusable rules referenced by the main rule before loading it into the editor, you need a `RuleResolver` delegate. The editor calls this delegate whenever it needs a referenced reusable rule, allowing your code to query rule storage and return the requested rule.

- ### Scope<br/>
	<small>Type: [`CodeEffects.Rule.Core.EvaluationScope`](/decision-automation/rule-common-models-evaluationscope)</small>

	Gets or sets a value that specifies how to treat multiple rules in a rule set when they are evaluated in a single step using the [Ruleset mode](/decision-automation/business-rule-editor-modes). The default value is `EvaluationScope.All`. See *Remarks* for additional details.

- ### ShortCircuit<br/>
	<small>Type: `System.Boolean`</small>

	Gets or sets a value indicating whether to stop further evaluation of a rule immediately after its result has been determined. The default value is `true`.

	#### Remarks

	The `ShortCircuit` parameter applies only to entire rules, not to individual statements, and operates in conjunction with the `Scope` parameter.

	To illustrate, consider a [ruleset](/decision-automation/business-rules-storage) containing three rules — `A`, `B`, and `C`. The following table demonstrates the possible evaluation states:

	<table style="margin-top:40px;margin-bottom:40px;width:95%;">
		<tr>
			<th width="130">Scope</th>
			<th width="130">ShortCircuit</th>
			<th width="240">Expression</th>
			<th>Details</th>
		</tr>
		<tr>
			<td>All</td>
			<td>True</td>
			<td>A short-and B short-and C</td>
			<td>Rules are evaluated sequentially using the logical `AND` operator. The evaluation process stops immediately when any rule returns `false`.</td>
		</tr>
		<tr>
			<td>All</td>
			<td>False</td>
			<td>A and B and C</td>
			<td>All rules are evaluated, and their results are combined using the logical `AND` operator.</td>
		</tr>
		<tr>
			<td>AtLeastOne</td>
			<td>True</td>
			<td>A short-or B short-or C</td>
			<td>Rules are evaluated sequentially using the logical `OR` operator. The evaluation process stops immediately when any rule returns `true`.</td>
		</tr>
		<tr>
			<td>AtLeastOne</td>
			<td>False</td>
			<td>A or B or C</td>
			<td>All rules are evaluated, and their results are combined using the logical `OR` operator.</td>
		</tr>
	</table>

	<div class="params">

	Individual rules are still evaluated using short-circuit logic internally.

	This behavior applies in two scenarios:

	- When evaluating multiple rules as part of a larger ruleset — call the `Evaluator.Evaluate(TSource, EvaluationScope, bool)` overload.
	- When evaluating a single rule that contains multiple `if` statements in [Ruleset mode](/decision-automation/business-rule-editor-modes) — pass the desired settings in the `EvaluationParameters` param of the `Evaluator` constructor.

	</div>

- ### Tracer<br/>
	<small>Type: [`CodeEffects.Rule.Common.Models.TracerDelegate`](/decision-automation/rule-common-models-tracerdelegate)</small>

	Gets or sets the `TraceDelegate` instance used by the evaluator to trace rule execution. When assigned, the evaluator invokes this delegate during [rule evaluation](/decision-automation/business-rule-evaluation), allowing your code to inspect expressions, evaluation results, and the corresponding [RuleXML](/decision-automation/business-rules-storage) elements for debugging, logging, or diagnostics. This property is optional. See the main [Debugging and Logging](/decision-automation/business-rules-debugger-tracer-event-logger-in-visual-studio) article for details.

- ### TypeResolver<br/>
	<small>Type: [`CodeEffects.Rule.Core.GetRuleDelegate`](/decision-automation/rule-common-models-getruledelegate)</small>

	Gets or sets the delegate used to resolve .NET types by their assembly-qualified or alias names during rule evaluation. Assign this property when your application uses custom type aliases or requires custom type resolution logic. This property is optional.  See the main [Type Handling and Aliasing](/decision-automation/business-rule-type-handling-and-tyle-alias-names) article for details.

</div>