
# TracerDelegate Delegate

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Models`</small></em>

## Summary

Enables runtime [tracing](/decision-automation/business-rules-debugger-tracer-event-logger-in-visual-studio) of [rule evaluation](/decision-automation/business-rule-evaluation). It is assigned to the [`EvaluationParameters.Tracer`](/decision-automation/rule-common-models-evaluationparameters) property and is invoked by the [`Evaluator`](/decision-automation/rule-engine-evaluator-generic) whenever it evaluates an expression in a rule.

## Syntax

```csharp
using System.Linq.Expressions;
using System.Xml.Linq;

public delegate void TracerDelegate(object source, Expression expression, object result, XElement element);
```

## Parameters

<div class="members">

- ### source<br/>
	<small>Type: `System.Object`</small>

	The current reference to the original [source object](/decision-automation/business-rule-source-object) passed to the evaluator. This is the same object instance used throughout [rule evaluation](/decision-automation/business-rule-evaluation). For [execution type](/decision-automation/business-rule-execution-type) rules, any setters or actions executed by the rule can modify this object, allowing your tracer implementation to inspect its current state at every trace point.

- ### expression<br/>
	<small>Type: [`System.Linq.Expressions.Expression`](https://github.com/dotnet/dotnet/blob/b0f34d51fccc69fd334253924abd8d6853fad7aa/src/runtime/src/libraries/System.Linq.Expressions/src/System/Linq/Expressions/Expression.cs)</small>

	The .NET `Expression` instance that has just been evaluated.

- ### result<br/>
	<small>Type: `System.Object`</small>

	The value produced by the evaluated expression.

- ### element<br/>
	<small>Type: [`System.Xml.Linq.XElement`](https://github.com/dotnet/dotnet/blob/b0f34d51fccc69fd334253924abd8d6853fad7aa/src/runtime/src/libraries/System.Private.Xml.Linq/src/System/Xml/Linq/XElement.cs)</small>

	The original [RuleXML](/decision-automation/business-rules-storage) node that represents the expression being evaluated. This parameter provides direct access to the XML element from which the expression was compiled, making it easy to identify the corresponding rule element during debugging or logging.

</div>

## Returns

<small>Type: `System.Void`</small>

## Remarks

The evaluator invokes the delegate every time it evaluates a traceable expression during rule execution. Because the delegate executes as part of the evaluation process, implementations should avoid long-running or resource-intensive operations unless required by the application.

See the [Using the Code Effects Rule Tracer](/decision-automation/business-rules-debugger-tracer-event-logger-in-visual-studio) article for implementation details.