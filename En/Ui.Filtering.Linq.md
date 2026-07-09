
# Rule-Based Data Filtering Using LINQ to Objects

This topic describes how to use Code Effects to filter any in-memory collection of objects using [evaluation type](/decision-automation/business-rule-evaluation-type) rules created in the [Rule Editor](/decision-automation/business-rule-editor). This feature is based on the standard <em>LINQ to Objects</em> provider built into .NET and requires no special configuration. It works with any `IEnumerable<T>` value, whether the data originates from a database, an API, a file, or any other custom source.

All examples below assume that:

- the rule was created in the Rule Editor using its Filter [mode](/decision-automation/business-rule-editor-modes),
- the rule was saved as [Rule XML](/decision-automation/business-rules-storage), and
- the variable `ruleXml` contains that XML.

## Overview

The `Filter(..)` [extension method](/decision-automation/rule-engine-ruleextensions) allows you to apply a rule to any in-memory sequence by converting [Rule XML](/decision-automation/business-rules-storage) into a LINQ predicate expression and filtering your objects accordingly.

Example:

```csharp
public class Product
{
    public decimal ListPrice { get; set; }
    public string Name { get; set; } = string.Empty;
}

// Rule: "Get records where Product.ListPrice is greater than [999]"
string ruleXml = GetRuleFromYourStorage();

var products = new List<Product>
{
    new() { ListPrice = 100m, Name = "Clamp" },
    new() { ListPrice = 200m, Name = "Spacer" },
    new() { ListPrice = 1200m, Name = "Crank Arm" },
    new() { ListPrice = 800m, Name = "Front Derailleur" },
    new() { ListPrice = 1500m, Name = "Mountain Tire" }
};

var result = products.Filter(ruleXml);

foreach (var p in result)
{
    Console.WriteLine($"{p.Name}  {p.ListPrice}");
}
```

This example prints only the products whose `ListPrice` is greater than `999`. The logic comes entirely from the rule stored in `ruleXml`.

## Deferred vs. Immediate Execution

LINQ to Objects is evaluated immediately when the `Filter(..)` method is applied, because the underlying provider (`Enumerable`) executes eagerly. This differs from [EF Core queries](/decision-automation/business-rule-sql-auto-generation-entity-framework), which are translated into SQL and executed only when enumerated.

This behavior is identical to other LINQ-to-Objects operations such as `.Where()`, `.Select()`, and `.OrderBy()`.

## Null Handling

By default, the `Filter(..)` [extension method](/decision-automation/rule-engine-ruleextensions) performs null checks during rule evaluation. If a rule attempts to access a member of a null object, the default behavior is to throw an exception, ensuring that rule authors are notified of potentially invalid data paths.

To use standard C# null semantics, where evaluating a nested property on a null object simply returns `null` and the rule evaluates to `false`, you must call the overload of `Filter(..)` that accepts an [`EvaluationParameters`](/decision-automation/rule-common-models-evaluationparameters) instance and set its `PerformNullChecks` property to `false`.

Example:

```csharp
var parameters = new EvaluationParameters
{
    PerformNullCheck = false   // Use standard C# null behavior
};

var filtered = products.Filter(ruleXml, parameters);
```

With `PerformNullCheck` set to `false`, LINQ to Objects behaves exactly like normal C#:

- If an object in the property chain is `null`,
- the resulting value is `null`,
- and the rule simply evaluates to `false` for that item,
- with no exceptions thrown.

This mode is useful when the dataset may contain optional or incomplete objects, or when you prefer natural C# null propagation.

## Methods in Rules

If the data surface includes in-rule methods, those methods can be used inside rules and executed during in-memory evaluation. The in-rule method is invoked for each item in the sequence.

## Combining LINQ Operators

You can freely combine standard LINQ operators with Code Effects filtering. Use them before or after calling `Filter(..)`.

#### Using LINQ before applying the rule:

```csharp
var active = customers.Where(c => c.IsActive);
var result = active.Filter(ruleXml);
```

#### Using LINQ after applying the rule:

```csharp
var filtered = customers.Filter(ruleXml);
var sorted = filtered.OrderBy(c => c.LastName);
```

Because all data is in memory, any LINQ operator is supported.

## Summary

Code Effects fully supports rule-based filtering of any in-memory collection using the standard LINQ to Objects provider. The `Filter(..)` method converts Rule XML into a LINQ predicate expression and applies that expression directly to your objects. All evaluation semantics are identical to standard C# behavior, including null-handling (if set as described above), method invocation, and chaining with other LINQ operators.

This feature is ideal for:

- cached or pre-loaded data,
- custom object collections,
- processing pipelines that already work in memory,
- UI-level filtering,
- and rule testing without a database.

No configuration is required: if your data implements `IEnumerable<T>`, Code Effects can filter it.