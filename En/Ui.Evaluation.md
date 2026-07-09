# Business Rule Evaluation

## Rule Elements and Definitions

Code Effects decision automation platform supports two types of business rules: [evaluation type](/decision-automation/business-rule-evaluation-type) and [execution type](/decision-automation/business-rule-execution-type). The evaluation type only answers one question — *True* or *False*. The execution type answers the same question and also can invoke public [methods](/decision-automation/rule-common-attributes-action) and/or set/update [field](/decision-automation/rule-common-attributes-field) values. Here are examples of both rule types:

> Check if First Name is equal to "John" and Age is greater than or equal to 18

> If First Name is equal to "John" and Age is greater than or equal to 18 then set Email to "john@test.test" and Send Notification (Email, "Hello!")

Notice how you were able to read both Code Effects rules and immediately understand the business logic each rule defines without the necessity of deciphering or decoding any cryptic format or <a href="/resources/images/doc/decisiontable.gif" target="_blank_">decision table</a>.

In both rules the `First Name is equal to "John"` and the `Age is greater than or equal to 18` are called *conditions*. Each condition consists of a *field*, an *operator*, and a *value*. Both the field and the value can be either a property of your [source object](/decision-automation/business-rule-source-object) or a qualified in-rule [method](/decision-automation/rule-common-attributes-method) declared [anywhere](/decision-automation/rule-common-attributes-externalmethod). The value can also contain [user input](/decision-automation/rule-common-models-valueinputtype) or be expressed as a numeric calculation.

The execution type rule can have one or more *rule sections*. Each section starts with a *flow* element: `If`, `Else If`, or `Else`:

> If Phone starts with "770" then set State to Georgia</br>
Else if Phone starts with "215" then set State to Pennsylvania</br>
Else if Phone starts with "212" then set State to New York</br>
Else set State to Unlisted

The rule above has four sections. The execution type rules must begin with the `If` section, can have none or many `Else If` sections, and may have only one `Else` section. The evaluation type rules can only have one section that starts with the flow element `Check if`.

Sections `Check if`, `If`, and `Else If` can have as many conditions as needed. The `If` and `Else If` sections must end with the `Then` action clause that encapsulates *setters* and/or rule *actions*. The `Else` section cannot have conditions and must only contain setters and/or actions. Rule sections may have any number of setters and action methods in any order. Setters and action methods are invoked in the order they are referenced in the rule during rule evaluation.

Rule conditions may have one or more *logical levels* divided by *logical operators* `and` or `or`. A level is a collection of rule conditions combined under the same logical operator. Code Effects employs *parentheses* to split rules into logical levels. They are unique to Code Effects platform.

> Check if Street is "123 Main Dr" and (Zip Code is "30004" or Zip Code is "30005")

The rule above has two logical levels — the higher one under the `and` operator, and the lower one under the `or` operator. Both levels contain two conditions (the `or` level is the second condition for the `and` level). Both rule types may have as many nested logical levels as needed, and each level may have one or more conditions.

One feature that is also unique to Code Effects is [Reusable Rules](/decision-automation/business-rules-reusable), which allow you to use any evaluation type rule in any other rules as if it were a `Boolean` field. For example, if we save the following rule under the name `Adult`:

> Check if Age is greater than or equal to 18

... and tell the editor to use it in field menus:

```csharp
using System.Collections.Generic;
using CodeEffects.Rule.Editor;
using CodeEffects.Rule.Editor.Models;

var editor = new Control("divEditorId");
editor.ContextMenuRules = new List<MenuItem>
{
	new MenuItem 
	{
		ID = "GuidID",
		DisplayName = "Adult"
	}
};
```

... we can create rules like these two reusing the same logic:

> Check if Gender is Female and Adult is True

> If Adult is True and Has Account ( Checking ) is False then Send Invitation

## Rule Evaluation

By default, the evaluation of each rule happens the way you would expect: each condition in each section of the rule is evaluated and the entire rule returns `true` as soon as any logical level evaluates to `true`. The evaluator won't attempt to evaluate the rest of the conditions (if any). The rule returns `false` if none of its logical levels evaluate to `true`. Both rule types work this way. The execution rule also invokes its setters and actions and waits for them to complete before returning the result of its evaluation.

But sometimes rules require greater control over the evaluation flow. The [`EvaluationParameter`](/decision-automation/rule-common-models-evaluationparameters) class can be used to define whether each condition in a section, or even each section of a rule, has to be evaluated before the entire rule returns the result. This can be especially useful in execution type rules if you must ensure that all actions of a section are invoked if it returns true, even if the result of the evaluation of the whole rule has already been determined. Consider the following three sections:

> If Name is "John" then Action 1</br>
Else if Name is "Alex" or Email ends with "hotmail.com" then Action 2</br>
Else if Name is "Michael" or Email contains "gmail" then Action 3</br>
Else Action 4

Under default evaluation settings, if the `Name` value of the record being evaluated against this rule is `John`, the rule would invoke `Action 1` and return `true`. Actions 2, 3, and 4 would never get hit even if their sections would return `true` as well.

You can ensure that all sections of a rule get evaluated and all setters and actions get invoked if their sections return `true` by setting the `EvaluationParameter.ShortCircuit` value to `false`:

```csharp
using CodeEffects.Rule.Common.Models;
using CodeEffects.Rule.Engine;

var rule = // Load XML of our rule from its storage
var data = new { Name = "John", Email = "test@gmail.com" };

var ev = new Evaluator(data.GetType(), rule, new EvaluationParameters() { ShortCircuit = false });
bool result = ev.Evaluate(data);
```

If we run this code, actions 1 and 3 will be invoked and the `result` value will be equal to `true`. Actions 2 and 4 will be ignored.

## Rulesets

As mentioned in [Engine Implementation](/decision-automation/business-rules-engine-implementation) and [Rule XML](/decision-automation/business-rules-storage) articles, rules can be combined into large rulesets if you need to evaluate any number of different rules referring to the same source object against the same set of data. That improves performance because all your rules are compiled only once:

```xml
<?xml version="1.0" encoding="utf-8"?>
<codeeffects
    xmlns="https://codeeffects.com/schemas/rule/41"
    xmlns:ui="https://codeeffects.com/schemas/ui/4">

        <rule id="ID1" ...>
            <!-- Rule #1 -->
        </rule>

        <rule id="ID2" ...>
            <!-- Rule #2 -->
        </rule>

        <rule id="ID3" ...>
            <!-- Rule #3 -->
        </rule>

        ...

</codeeffects>
```

The default behavior of the engine is to evaluate all rules in the ruleset regardless of the overall outcome; this is logically opposite to the default settings of the rule section evaluation behavior. If you need to evaluate each rule in the order they are listed in the ruleset and return `true` as soon as any of them evaluates to `true`, disregarding setters and actions of the rest of the rules, get an instance of the same [`EvaluationParameters`](/decision-automation/rule-common-models-evaluationparameters) class but this time use its `Scope` property and set its value to `EvaluationScope.AtLeastOne`.

## Evaluators

Code Effects employs two classes and multiple overloads of the extension `Evaluate(..)` and `Filter(..)` methods for rule evaluation and [data filtering](/decision-automation/business-rule-data-filtering).

- [`Evaluator`](/decision-automation/rule-engine-evaluator) and [`Evaluator<TSource>`](/decision-automation/rule-engine-evaluator-generic) classes are designed to evaluate or filter large amounts of data against large rulesets or queries.

    ```csharp
    var evaluator = new Evaluator<SourceObjectType>(ruleXmlString);
    var data = // get data for evaluation...

    foreach(SourceObjectType record in data)
        bool success = evaluator.Evaluate(record);
    ```

- [Extension Methods](/decision-automation/rule-engine-ruleextensions) are designed for cases when you just need to evaluate a number of records against a single rule or a small ruleset.

    ```csharp
    bool success = sourceInstance.Evaluate(ruleXmlString);
    ```

The Code Effects evaluation engine is an extremely fast business rules engine that is capable of evaluating millions of source objects against a rule in a matter of milliseconds. Because the business needs of organizations and industries can have unforeseeable differences, Code Effects engine attempts to be as forgiving as possible, allowing developers to use many kinds of scenarios, and throwing exceptions only when the result of rule evaluation is clearly unpredictable. For example, it allows comparisons of different .NET [numeric types](/decision-automation/business-rules-data-types) without asking developers for explicit boxing or type conversion. It also provides internal type conversion between nullable and regular types when preparing the [source](/decision-automation/business-rule-source-object) for evaluation.
