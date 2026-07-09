
# Evaluator Class

<em><small>**Assembly:** CodeEffects.Rule.Engine.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Engine`</small></em>

## Summary

Provides core functionality for the [evaluation](/decision-automation/business-rule-evaluation) of business rules when the exact type of the [source object](/decision-automation/business-rule-source-object) is known only at run time.

## Syntax

```csharp
public class Evaluator : CodeEffects.Rule.Engine.EvaluatorBase
```
<div class="members">

## Constructors

- ### Evaluator(..)

	#### Parameters

	<div class="params">

	- *rulesetXml* (`System.String`) - The [Rule XML](/decision-automation/business-rules-storage) document as a string.
	- *provider* ([`CodeEffects.Rule.Common.Models.IFlexDataProvider`](/decision-automation/rule-common-models-iflexdataprovider)) - The [FlexSource](/decision-automation/business-rule-flex-source) data provider.
	- *parameters* ([`CodeEffects.Rule.Common.Models.EvaluationParameters`](/decision-automation/rule-common-models-evaluationparameters)) - The evaluation parameters. The default value is `null`.

	</div>

- ### Evaluator(..)

	#### Parameters

	<div class="params">

	- *sourceType* (`System.Type`) - The type of the [source object](/decision-automation/business-rule-source-object) used by all rules referenced in the [Rule XML](/decision-automation/business-rules-storage) of the ruleset being evaluated.
	- *rulesetXml* (`System.String`) - The [Rule XML](/decision-automation/business-rules-storage) document as a string.
	- *parameters* ([`CodeEffects.Rule.Common.Models.EvaluationParameters`](/decision-automation/rule-common-models-evaluationparameters)) - The evaluation parameters. The default value is `null`.

	</div>

- ### Evaluator(..)

	#### Parameters

	<div class="params">
	
	- *sourceType* (`System.Type`) - The type of the [source object](/decision-automation/business-rule-source-object) used by all rules referenced in the [Rule XML](/decision-automation/business-rules-storage) of the ruleset being evaluated.
	- *rulesetXml* (`System.String`) - The [Rule XML](/decision-automation/business-rules-storage) document as a string.
	- *getRule* ([`CodeEffects.Rule.Common.Models.GetRuleDelegate`](/decision-automation/rule-common-models-getruledelegate)) - Specifies a delegate to a method used for retrieving external rules referenced by other rules. If this parameter is `null`, the evaluator attempts to locate any referenced rules within the [ruleset](/decision-automation/business-rules-storage) itself. If no referenced rules are found, an exception is thrown.
	- *maxIterations* (`System.Int32`) - The maximum number of rule iterations in the [Loop mode](/decision-automation/business-rule-editor-modes) of the Rule Editor. The default value is `-1`, which means no limit.

	</div>

## Properties

- ### LicenseFileFullPath<br/>
	<small>Type: `System.String`</small>

	Gets or sets the full physical path to the Engine Subscription license file. This property is required for the Code Effects [Engine Subscription](/content/business-rules-engine-editions) edition and is ignored by all other editions. See the [Engine Implementation](/decision-automation/business-rules-engine-implementation) topic for details on using the license file.

## Methods

- ### Evaluate(..)<br/>
	<small>Returns: `System.Boolean` - Returns a `Boolean` value indicating the result of the rule [evaluation](/decision-automation/business-rule-evaluation): `true` if the evaluation succeeded; otherwise, `false`.</small>

	Evaluates all rules in the [ruleset](/decision-automation/business-rules-storage) within the defined [scope](/decision-automation/rule-common-models-evaluationparameters) against the specified [source object](/decision-automation/business-rule-source-object).

	#### Parameters

	<div class="params">
	
	- *source* (`System.Object`) - Specifies the object to be evaluated. This object serves as the data source for the rule.
	- *scope* ([`CodeEffects.Rule.Common.Models.EvaluationScope`](/decision-automation/rule-common-models-evaluationscope)) - Specifies the logical operator (`AND` or `OR`) used when executing the rules in the ruleset.
	- *shortCircuit* (`System.Boolean`) - Specifies whether short-circuit evaluation is enabled. When set to `true`, the evaluator stops execution as soon as the rule scope is satisfied. When set to `false`, all rules are evaluated even if the final result is already determined. See **Remarks** in [`EvaluationParameters`](/decision-automation/rule-common-models-evaluationparameters) topic for details. The default value is `true`.

	</div>

- ### Evaluate(..)<br/>
	<small>Returns: `System.Boolean` - Returns a `Boolean` value indicating the result of the rule [evaluation](/decision-automation/business-rule-evaluation): `true` if the evaluation succeeded; otherwise, `false`.</small>

	Evaluates the specific rule selected by its zero-based index from the [ruleset](/decision-automation/business-rules-storage) against the specified [source object](/decision-automation/business-rule-source-object).

	#### Parameters

	<div class="params">
	
	- *source* (`System.Object`) - Specifies the object to be evaluated. This object serves as the data source for the rule.
	- *ruleIndex* (`System.Int32`) - Specifies the index of the rule to be executed. If the [ruleset](/decision-automation/business-rules-storage) contains multiple rules, you can [evaluate](/decision-automation/business-rule-evaluation) a specific rule by providing its index. Do not confuse this with external rules referenced within other rules. The specified rule must exist as a top-level rule within the ruleset; otherwise, an exception will be thrown.

	</div>

- ### Evaluate(..)<br/>
	<small>Returns: `System.Boolean` - Returns a `Boolean` value indicating the result of the rule [evaluation](/decision-automation/business-rule-evaluation): `true` if the evaluation succeeded; otherwise, `false`.</small>

	Evaluates the rule against the specified [source object](/decision-automation/business-rule-source-object). If no rule ID is provided, the first rule in the [ruleset](/decision-automation/business-rules-storage) is [evaluated](/decision-automation/business-rule-evaluation) by default.

	#### Parameters

	<div class="params">
	
	- *source* (`System.Object`) - Specifies the object to be evaluated. This object serves as the data source for the rule.
	- *ruleId* (`System.String`) - Specifies an optional ID of the rule to be evaluated. If the ruleset contains multiple rules, you can evaluate a specific rule by providing its ID. Do not confuse this with external rules referenced within other rules. The specified rule must exist as a top-level rule within the ruleset; otherwise, an exception will be thrown.

	</div>

- ### Filter(..)<br/>
	<small>Returns: `System.Collections.IEnumerable` - Returns a new `System.Collections.Generic.IEnumerable<T>` collection containing all source objects that satisfied the rule evaluation.</small>

	Filters a collection of [source objects](/decision-automation/business-rule-source-object) by applying a business rule. The specified [ruleset Xml](/decision-automation/business-rules-storage) is loaded into the `Evaluator`, where its rules are compiled into IL objects and stored in memory. The `Evaluator` then applies the rule to each object in the collection. Source objects that satisfy the rule are returned as a new collection of type `System.Collections.IEnumerable`.

	#### Parameters

	<div class="params">
	
	- *source* (`System.Collections.IEnumerable`) - Specifies an enumerable collection of type `System.Collections.Generic.IEnumerable<T>` to be filtered.

	</div>

- ### Filter(..)<br/>
	<small>Returns: `System.Linq.IQueryable` - Returns an expression tree of type `System.Linq.IQueryable<T>` for deferred or immediate evaluation. Immediate evaluation is performed by the underlying LINQ provider.</small>

	Filters a collection of source objects by applying a business rule. The specified ruleset Xml is loaded into the `Evaluator`, where its rules are compiled into expression trees. This method does not perform immediate evaluation; instead, it returns an expression tree that can be further modified. During execution, the expression tree is translated into a set of backend commands supported by the underlying LINQ provider. For example, when using LINQ to SQL, the expression tree is translated into corresponding SQL commands.

	#### Parameters

	<div class="params">
	
	- *source* (`System.Linq.IQueryable`) - Specifies a queryable collection of type `System.Linq.IQueryable<T>` to be filtered.

	</div>

</div>