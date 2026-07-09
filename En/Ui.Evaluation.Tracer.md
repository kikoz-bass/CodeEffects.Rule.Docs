
# Using the Code Effects Rule Tracer

The Code Effects rule [tracer](/decision-automation/rule-common-models-tracerdelegate) allows your application to inspect rule evaluation at runtime. It can be used as a native Visual Studio debugging aid, a custom logging mechanism, or both.

The tracer is assigned through [`EvaluationParameters`](/decision-automation/rule-common-models-evaluationparameters) before the rule is [evaluated](/decision-automation/business-rule-evaluation). During evaluation, the engine calls your tracer method every time it evaluates an expression in the rule, including equations, comparisons, conditions, setters, parameter values, and method invocations.

## Basic Implementation

```csharp
using System.Xml.Linq;
using System.Linq.Expressions;
using CodeEffects.Rule.Common.Models;
using CodeEffects.Rule.Engine;

// Your debugging or logging method that implements TracerDelegate.
// It can be declared anywhere as an instance or static method.
public void YourTracer(object source, Expression expression, object result, XElement element)
{
	// Set a breakpoint here to inspect parameter values during evaluation.
	// Add any tracing or debugging logic required by your application.
}

// Declare evaluation parameters and assign your Tracer method
// as the tracer for this evaluation.
var options = new EvaluationParameters { Tracer = YourTracer };
var evaluator = new Evaluator<YourSourceType>(YourRuleStorage.GetRuleXml(), options);
bool success = evaluator.Evaluate(instanceOfYourSourceType);
```

## How the Tracer Works

The [tracer method](/decision-automation/rule-common-models-tracerdelegate) is called during rule evaluation each time the engine evaluates an expression. This includes evaluation of rule conditions, comparisons, calculations, setter values, method parameters, and method invocations.

Your tracer method receives four values:

```csharp
public void Tracer(object source, Expression expression, object result, XElement element) { }
```

- ### source

	The `source` parameter is the current reference to the original [source object](/decision-automation/business-rule-source-object) that was passed to the [`Evaluator`](/decision-automation/rule-engine-evaluator).

	This is the same object instance being evaluated by the [rule or ruleset](/decision-automation/business-rules-storage). If your rule contains [setters, in-rule methods, or actions](/decision-automation/business-rule-evaluation) that modify the source object, those changes can be inspected from this parameter as evaluation progresses.

	For debugging, you can create a copy of the source object before evaluation begins and compare it to the current state of source at each breakpoint:

	```csharp
	var original = Clone(instanceOfYourSourceType);
	bool success = evaluator.Evaluate(instanceOfYourSourceType);
	```

	Inside the tracer, you can compare the original values to the current values to determine which part of the rule changed the source object and when the change occurred.

- ### expression

	The `expression` parameter is the actual .NET [`Expression`](https://learn.microsoft.com/en-us/dotnet/api/system.linq.expressions.expression?view=net-10.0) instance that has just been evaluated.

	Code Effects compiles rule logic into .NET expressions. The tracer exposes those expressions during evaluation, allowing you to inspect the execution flow directly in your own code. The expression can represent many different parts of the rule, including:

	- a comparison;
	- a calculation;
	- a condition;
	- a condition;
	- a method call;
	- a parameter value;
	- an action invocation.

	In Visual Studio, place a breakpoint inside your tracer method and inspect this parameter to see what kind of expression was evaluated.

- ### result

	The `result` parameter contains the result produced by the `expression` that was just evaluated. For example, it can contain:

	- `true` or `false` for a condition or comparison;
	- a calculated numeric value;
	- a string value;
	- a method return value;
	- a setter value;
	- another value produced during rule evaluation.

	This parameter is useful both for debugging and for [logging](/content/business-rules-native-visual-studio-debugger-tracer-event-logger). It allows your application to record not only that a rule element was evaluated, but also what value it produced.

- ### element

	The `element` parameter is the original [RuleXML](/decision-automation/business-rules-storage) node that represents the expression being evaluated. This is useful when you need to connect runtime evaluation back to the rule definition. You can inspect the XML node to see the original rule element, its attributes, values, and structure as they existed before evaluation.

	For example, while debugging, this parameter helps answer questions such as:

	- Which rule element is currently being evaluated?
	- What value was stored in the rule XML?
	- Which field, method, or action does this expression represent?
	- Which condition produced the current result?

	You can inspect the XML directly in Visual Studio or write it to a log if you need a persistent trace.

	```csharp
	string ruleElementXml = element?.ToString(SaveOptions.DisableFormatting);
	```
## Using the Tracer as a Debugger

The simplest way to use the tracer is to set a Visual Studio breakpoint inside your tracer method.

```csharp
public void Tracer(object source, Expression expression, object result, XElement element)
{
	// Set breakpoint here
}
```

When the evaluator reaches any traceable expression, Visual Studio will stop at the breakpoint. You can then inspect:

- the current source object;
- the expression that was evaluated;
- the result of that expression;
- the Rule XML node that produced the expression.

This makes it possible to step through rule evaluation using the same debugger and development environment you already use for application code.

## Using the Tracer for Logging

The tracer can also be used to write evaluation details to your own logging system.

```csharp
public void MyTracer(object source, Expression expression, object result, XElement element)
{
	var message = new
	{
		Result = result,
		Expression = expression?.ToString(),
		Element = element?.ToString(SaveOptions.DisableFormatting)
	};
	
	Logger.Debug(message);
}
```

The exact logging format is up to your application. You can write trace data to a text file, database table, structured logging provider, telemetry system, or internal audit store.

For production systems, structured logging is usually more useful than plain text because it allows filtering, searching, and reporting by rule element, result, rule ID, user ID, transaction ID, or other application-specific values.

## Logging Evaluation Context

The tracer only receives the values passed by the engine. If your application needs additional context, such as a user ID, request ID, transaction ID, rule ID, or tenant ID, you can capture that context from the surrounding scope.

```csharp
var requestID = currentRequest.ID;
var userID = currentUser.ID;

void MyTracer(object source, Expression expression, object result, XElement element)
{
	Logger.Debug(new
	{
		RequestID = requestID,
		UserID = userID,
		Result = result,
		Expression = expression?.ToString(),
		Element = element?.ToString(SaveOptions.DisableFormatting)
	});
}

var options = new EvaluationParameters { Tracer = MyTracer };
```

This approach allows your trace records to be connected to the business transaction that caused the rule evaluation.

## Capturing Source Object Changes

[Execution type rules](/decision-automation/business-rule-execution-type) as well as [in-rule methods](/decision-automation/rule-common-attributes-method) referenced in any rule type may modify the [source object](/decision-automation/business-rule-source-object). If you need to know exactly when those changes happen, keep a copy of the source object before evaluation and compare it inside the tracer.

```csharp
var beforeEvaluation = Clone(instanceOfYourSourceType);

void MyTracer(object source, Expression expression, object result, XElement element)
{
	// Compare beforeEvaluation with source here.
	// Log or inspect changed values as needed.
}
```

The `Clone` implementation depends on your application and source type. You can use a copy constructor, manual mapping, serialization, or any other cloning method appropriate for your object model.

## Keeping Debug and Production Tracing Separate

For development, the tracer can be used with a breakpoint and no logging code at all. For production, avoid debugger-specific logic and keep the tracer focused on efficient logging or audit capture. A common pattern is to enable tracing conditionally:

```csharp
var options = new EvaluationParameters();

if (enableRuleTracing)
{
	options.Tracer = this.Tracer;
}
```

This allows your application to use the same rule evaluation code with or without tracing.

## Performance Considerations

The tracer is called frequently during rule evaluation. Complex rules may trigger many tracer calls. For that reason:

- keep tracer logic lightweight;
- avoid slow synchronous I/O inside the tracer;
- avoid unnecessary serialization of large source objects;
- log only the data your application needs;
- consider filtering trace output in production;
- use asynchronous or buffered logging if your logging infrastructure supports it.

The tracer gives your application detailed visibility into rule execution, but the amount of data it can produce depends on the complexity of the rule and the amount of information you choose to record.

## Error Handling

Your tracer method is part of the evaluation process. Avoid throwing exceptions from tracing code unless your application intentionally wants tracing failures to interrupt rule evaluation. In most cases, tracing failures should be handled inside the tracer.

```csharp
public void Tracer(object source, Expression expression, object result, XElement element)
{
	try
	{
		Logger.Debug(new
		{
			Result = result,
			Expression = expression?.ToString(),
			Element = element?.ToString(SaveOptions.DisableFormatting)
		});
	}
	catch
	{
		// Handle or suppress logging errors according to your application policy
	}
}
```

## When to Use the Tracer

Use the tracer when you need runtime visibility into rule evaluation. Common uses include:

- debugging rules during development;
- investigating unexpected rule results;
- inspecting source object changes during execution-type rules;
- logging condition outcomes;
- logging method and action execution;
- creating audit trails;
- supporting compliance reviews;
- building internal rule evaluation diagnostics;
- connecting rule execution to application telemetry.

## Summary

To use the Code Effects tracer:

1. Create a method that matches the [`TracerDelegate`](/decision-automation/rule-common-models-tracerdelegate) signature.
1. Assign that method to [`EvaluationParameters.Tracer`](/decision-automation/rule-common-models-evaluationparameters).
1. Pass the `EvaluationParameters` instance to the [`Evaluator`](/decision-automation/rule-engine-evaluator).
1. Set a breakpoint inside the tracer method for debugging, or add logging code for runtime tracing.

The tracer gives your .NET application direct access to the source object, evaluated expression, evaluation result, and original RuleXML element at each trace point in the rule evaluation process.