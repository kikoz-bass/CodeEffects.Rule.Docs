
# RuleExtensions Class

<em><small>**Assembly:** CodeEffects.Rule.Engine.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Engine`</small></em>

## Summary

Provides a set of static methods for [evaluating](/decision-automation/business-rule-evaluation) business rules against individual [source objects](/decision-automation/business-rule-source-object) and for filtering collections that implement one of the following interfaces: `System.Collections.IEnumerable`, `System.Collections.Generic.IEnumerable<T>`, `System.Linq.IQueryable`, or `System.Linq.IQueryable<T>`. These methods internally use either the [`Evaluator<TSource>`](/decision-automation/rule-engine-evaluator-generic) or [`Evaluator`](/decision-automation/rule-engine-evaluator) class to apply business rules.

## Syntax

```csharp
public static class RuleExtensions
```
<div class="members">

## Extension Methods

- ### Evaluate<TSource>(..)<br/>
	<small>Returns: `System.Boolean` - Returns a `Boolean` value indicating the result of the rule [evaluation](/decision-automation/business-rule-evaluation): `true` if the evaluation succeeded; otherwise, `false`.</small>

	Evaluates a rule from the specified [ruleset](/decision-automation/business-rules-storage) against the given source object. This extension method serves as a shortcut for calling the [`Evaluator<TSource>.Evaluate(..)`](/decision-automation/rule-engine-evaluator-generic) method. Use `Evaluator<TSource>` class directly when executing multiple evaluations with the same ruleset, as the evaluator compiles rules once per instantiation.

	<div class="params">

	#### Type Parameters

	- *TSource* - Specifies the underlying source type of the object against which the rules will be evaluated.

	#### Parameters
	
	- this *source* (`TSource`) - Specifies the object to be evaluated. This object serves as the data source for the rule.
	- *getRule* ([`CodeEffects.Rule.Common.Models.GetRuleDelegate`](/decision-automation/rule-common-models-getruledelegate)) - Specifies a delegate to a method used for retrieving external rules referenced by other rules. If this parameter is `null`, the evaluator attempts to locate any referenced rules within the [ruleset](/decision-automation/business-rules-storage) itself. If no referenced rules are found, an exception is thrown.
	- *rulesetXml* (`System.String`) - The [Rule XML](/decision-automation/business-rules-storage) document as a string.
	- *ruleIndex* (`System.Int32`) - Specifies the index of the rule to be executed. If the [ruleset](/decision-automation/business-rules-storage) contains multiple rules, you can [evaluate](/decision-automation/business-rule-evaluation) a specific rule by providing its zero-based index. Do not confuse this with external rules referenced within other rules. The specified rule must exist as a top-level rule within the ruleset; otherwise, an exception will be thrown.

	</div>

- ### Evaluate<TSource>(..)<br/>
	<small>Returns: `System.Boolean` - Returns a `Boolean` value indicating the result of the rule [evaluation](/decision-automation/business-rule-evaluation): `true` if the evaluation succeeded; otherwise, `false`.</small>

	Evaluates a rule from the specified [ruleset](/decision-automation/business-rules-storage) against the given source object. This extension method serves as a shortcut for calling the [`Evaluator<TSource>.Evaluate(..)`](/decision-automation/rule-engine-evaluator-generic) method. Use `Evaluator<TSource>` class directly when executing multiple evaluations with the same ruleset, as the evaluator compiles rules once per instantiation.

	<div class="params">

	#### Type Parameters

	- *TSource* - Specifies the underlying source type of the object against which the rules will be evaluated.

	#### Parameters
	
	- this *source* (`TSource`) - Specifies the object to be evaluated. This object serves as the data source for the rule.
	- *rulesetXml* (`System.String`) - The [Rule XML](/decision-automation/business-rules-storage) document as a string.
	- *ruleId* (`System.String`) - Specifies an optional ID of the rule to be evaluated. If the ruleset contains multiple rules, you can evaluate a specific rule by providing its ID. Do not confuse this with external rules referenced within other rules. The specified rule must exist as a top-level rule within the ruleset; otherwise, an exception will be thrown.
	- *getRule* ([`CodeEffects.Rule.Common.Models.GetRuleDelegate`](/decision-automation/rule-common-models-getruledelegate)) - Specifies a delegate to a method used for retrieving external rules referenced by other rules. If this parameter is `null`, the evaluator attempts to locate any referenced rules within the [ruleset](/decision-automation/business-rules-storage) itself. If no referenced rules are found, an exception is thrown.

	</div>

- ### Filter(..)<br/>
	<small>Returns: `System.Collections.IEnumerable` - Returns a new `System.Collections.Generic.IEnumerable<T>` collection containing all source objects that satisfied the rule evaluation.</small>

	Filters a collection of [source objects](/decision-automation/business-rule-source-object) by applying a business rule. The specified [ruleset Xml](/decision-automation/business-rules-storage) is loaded into the `Evaluator` class, where its rules are compiled and stored in memory. The evaluator then applies either the first rule in the ruleset or the rule identified by the specified rule ID to each object in the collection. Source objects that satisfy the rule are returned as a new collection of type `System.Collections.IEnumerable`.

	#### Parameters

	<div class="params">
	
	- this *source* (`System.Collections.IEnumerable`) - Specifies an enumerable collection to be filtered.
	- *type* (`System.Collections.IEnumerable`) - Specifies the underlying source type of the enumerable collection.
	- *rulesetXml* (`System.String`) - The [Rule XML](/decision-automation/business-rules-storage) document as a string to be evaluated against source objects.
	- *ruleId* (`System.String`) - Specifies an optional ID of the rule to be evaluated. If the ruleset contains multiple rules, you can evaluate a specific rule by providing its ID. Do not confuse this with external rules referenced within other rules. The specified rule must exist as a top-level rule within the ruleset; otherwise, an exception will be thrown.
	- *getRule* ([`CodeEffects.Rule.Common.Models.GetRuleDelegate`](/decision-automation/rule-common-models-getruledelegate)) - Specifies a delegate to a method used for retrieving external rules referenced by other rules. If this parameter is `null`, the evaluator attempts to locate any referenced rules within the [ruleset](/decision-automation/business-rules-storage) itself. If no referenced rules are found, an exception is thrown.

	</div>

- ### Filter(..)<br/>
	<small>Returns: `System.Collections.IQueryable` - Returns an expression tree of type `System.Linq.IQueryable<T>` for deferred or immediate evaluation. Immediate evaluation is performed by the underlying LINQ provider.</small>

	Filters a collection of source objects by applying a business rule. The specified ruleset Xml is loaded into the `Evaluator`, where its rules are compiled into expression trees. This method does not perform immediate evaluation; instead, it returns an expression tree that can be further modified. During execution, the expression tree is translated into a set of backend commands supported by the underlying LINQ provider. For example, when using LINQ to SQL, the expression tree is translated into corresponding SQL commands.

	#### Parameters

	<div class="params">
	
	- this *source* (`System.Collections.IQueryable`) - Specifies a queriable collection to be filtered.
	- *type* (`System.Collections.IEnumerable`) - Specifies the underlying source type of the queriable collection.
	- *rulesetXml* (`System.String`) - The [Rule XML](/decision-automation/business-rules-storage) document as a string to be evaluated against source objects.
	- *parameters* ([`CodeEffects.Rule.Common.Models.EvaluationParameters`](/decision-automation/rule-common-models-evaluationparameters)) - The evaluation parameters.

	</div>

- ### Filter(..)<br/>
	<small>Returns: `System.Collections.IQueryable` - Returns an expression tree of type `System.Linq.IQueryable<T>` for deferred or immediate evaluation. Immediate evaluation is performed by the underlying LINQ provider.</small>

	Filters a collection of source objects by applying a business rule. The specified ruleset Xml is loaded into the `Evaluator`, where its rules are compiled into expression trees. This method does not perform immediate evaluation; instead, it returns an expression tree that can be further modified. During execution, the expression tree is translated into a set of backend commands supported by the underlying LINQ provider. For example, when using LINQ to SQL, the expression tree is translated into corresponding SQL commands.

	#### Parameters

	<div class="params">
	
	- this *source* (`System.Collections.IQueryable`) - Specifies a queriable collection to be filtered.
	- *type* (`System.Collections.IEnumerable`) - Specifies the underlying source type of the queriable collection.
	- *rulesetXml* (`System.String`) - The [Rule XML](/decision-automation/business-rules-storage) document as a string to be evaluated against source objects.
	- *ruleId* (`System.String`) - Specifies an optional ID of the rule to be evaluated. If the ruleset contains multiple rules, you can evaluate a specific rule by providing its ID. Do not confuse this with external rules referenced within other rules. The specified rule must exist as a top-level rule within the ruleset; otherwise, an exception will be thrown.
	- *getRule* ([`CodeEffects.Rule.Common.Models.GetRuleDelegate`](/decision-automation/rule-common-models-getruledelegate)) - Specifies a delegate to a method used for retrieving external rules referenced by other rules. If this parameter is `null`, the evaluator attempts to locate any referenced rules within the [ruleset](/decision-automation/business-rules-storage) itself. If no referenced rules are found, an exception is thrown.

	</div>

- ### Filter&lt;TSource&gt;(..)<br/>
	<small>Returns: `System.Collections.IEnumerable<TSource>` - Returns a new `System.Collections.Generic.IEnumerable<TSource>` collection containing all source objects that satisfied the rule evaluation.</small>

	Filters a collection of [source objects](/decision-automation/business-rule-source-object) by applying a business rule. The specified [ruleset Xml](/decision-automation/business-rules-storage) is loaded into the `Evaluator`, where its rules are compiled and stored in memory. The evaluator then applies either the first rule in the ruleset or the rule identified by the specified rule ID to each object in the collection. Source objects that satisfy the rule are returned as a new collection of type `System.Collections.IEnumerable<TSource>`.

	<div class="params">

	#### Type Parameters

	- *TSource* - An underlying source type of a generic enumerable collection.

	#### Parameters
	
	- *source* (`System.Collections.IEnumerable<TSource>`) - Specifies an enumerable collection to be filtered.
	- *rulesetXml* (`System.String`) - The [Rule XML](/decision-automation/business-rules-storage) document as a string to be evaluated against source objects.
	- *ruleIndex* (`System.Int32`) - Specifies the index of the rule to be executed. If the [ruleset](/decision-automation/business-rules-storage) contains multiple rules, you can [evaluate](/decision-automation/business-rule-evaluation) a specific rule by providing its zero-based index. Do not confuse this with external rules referenced within other rules. The specified rule must exist as a top-level rule within the ruleset; otherwise, an exception will be thrown.
	- *getRule* ([`CodeEffects.Rule.Common.Models.GetRuleDelegate`](/decision-automation/rule-common-models-getruledelegate)) - Specifies a delegate to a method used for retrieving external rules referenced by other rules. If this parameter is `null`, the evaluator attempts to locate any referenced rules within the [ruleset](/decision-automation/business-rules-storage) itself. If no referenced rules are found, an exception is thrown.

	</div>

- ### Filter&lt;TSource&gt;(..)<br/>
	<small>Returns: `System.Collections.IEnumerable<TSource>` - Returns a new `System.Collections.Generic.IEnumerable<TSource>` collection containing all source objects that satisfied the rule evaluation.</small>

	Filters a collection of [source objects](/decision-automation/business-rule-source-object) by applying a business rule. The specified [ruleset Xml](/decision-automation/business-rules-storage) is loaded into the `Evaluator`, where its rules are compiled and stored in memory. The evaluator then applies either the first rule in the ruleset or the rule identified by the specified rule ID to each object in the collection. Source objects that satisfy the rule are returned as a new collection of type `System.Collections.IEnumerable<TSource>`.

	<div class="params">

	#### Type Parameters

	- *TSource* - An underlying source type of a generic enumerable collection.

	#### Parameters
	
	- *source* (`System.Collections.IEnumerable<TSource>`) - Specifies an enumerable collection to be filtered.
	- *rulesetXml* (`System.String`) - The [Rule XML](/decision-automation/business-rules-storage) document as a string to be evaluated against source objects.
	- *ruleId* (`System.String`) - Specifies an optional ID of the rule to be evaluated. If the ruleset contains multiple rules, you can evaluate a specific rule by providing its ID. Do not confuse this with external rules referenced within other rules. The specified rule must exist as a top-level rule within the ruleset; otherwise, an exception will be thrown.
	- *getRule* ([`CodeEffects.Rule.Common.Models.GetRuleDelegate`](/decision-automation/rule-common-models-getruledelegate)) - Specifies a delegate to a method used for retrieving external rules referenced by other rules. If this parameter is `null`, the evaluator attempts to locate any referenced rules within the [ruleset](/decision-automation/business-rules-storage) itself. If no referenced rules are found, an exception is thrown.

	</div>

- ### Filter&lt;TSource&gt;(..)<br/>
	<small>Returns: `System.Linq.IQueryable<TSource>` - Returns an expression tree of type `System.Linq.IQueryable<TSource>` for deferred or immediate evaluation. Immediate evaluation is performed by the underlying LINQ provider.</small>

	Filters a collection of source objects by applying a business rule. The specified ruleset Xml is loaded into the `Evaluator`, where its rules are compiled into expression trees. This method does not perform immediate evaluation; instead, it returns an expression tree that can be further modified. During execution, the expression tree is translated into a set of backend commands supported by the underlying LINQ provider. For example, when using LINQ to SQL, the expression tree is translated into corresponding SQL commands.

	<div class="params">

	#### Type Parameters

	- *TSource* - An underlying source type of a generic enumerable collection

	#### Parameters

	- *source* (`System.Linq.IQueryable<TSource>`) - Specifies a queryable collection of type `System.Linq.IQueryable<TSource>` to be filtered.
	- *rulesetXml* (`System.String`) - The [Rule XML](/decision-automation/business-rules-storage) document as a string to be evaluated against source objects.
	- *parameters* ([`CodeEffects.Rule.Common.Models.EvaluationParameters`](/decision-automation/rule-common-models-evaluationparameters)) - The evaluation parameters.

	</div>

- ### Filter&lt;TSource&gt;(..)<br/>
	<small>Returns: `System.Linq.IQueryable<TSource>` - Returns an expression tree of type `System.Linq.IQueryable<TSource>` for deferred or immediate evaluation. Immediate evaluation is performed by the underlying LINQ provider.</small>

	Filters a collection of source objects by applying a business rule. The specified ruleset Xml is loaded into the `Evaluator`, where its rules are compiled into expression trees. This method does not perform immediate evaluation; instead, it returns an expression tree that can be further modified. During execution, the expression tree is translated into a set of backend commands supported by the underlying LINQ provider. For example, when using LINQ to SQL, the expression tree is translated into corresponding SQL commands.

	<div class="params">

	#### Type Parameters

	- *TSource* - An underlying source type of a generic enumerable collection

	#### Parameters

	- *source* (`System.Linq.IQueryable<TSource>`) - Specifies a queryable collection of type `System.Linq.IQueryable<TSource>` to be filtered.
	- *rulesetXml* (`System.String`) - The [Rule XML](/decision-automation/business-rules-storage) document as a string to be evaluated against source objects.
	- *ruleId* (`System.String`) - Specifies an optional ID of the rule to be evaluated. If the ruleset contains multiple rules, you can evaluate a specific rule by providing its ID. Do not confuse this with external rules referenced within other rules. The specified rule must exist as a top-level rule within the ruleset; otherwise, an exception will be thrown.
	- *getRule* ([`CodeEffects.Rule.Common.Models.GetRuleDelegate`](/decision-automation/rule-common-models-getruledelegate)) - Specifies a delegate to a method used for retrieving external rules referenced by other rules. If this parameter is `null`, the evaluator attempts to locate any referenced rules within the [ruleset](/decision-automation/business-rules-storage) itself. If no referenced rules are found, an exception is thrown.

	</div>

</div>