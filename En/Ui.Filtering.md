
# Code Effects Data Filtering

The Data Filtering feature of Code Effects extends traditional rule evaluation beyond simple decision logic. It allows you to use any [evaluation type](/decision-automation/business-rule-evaluation-type) rule as a dynamic predicate for LINQ queries, effectively transforming human-authored business rules into executable query filters.

This feature enables seamless reuse of the same rule definitions both for evaluating single data objects and for filtering large datasets, such as those retrieved from databases, entity models, or in-memory collections.

## Concept Overview

In typical decision automation systems, rules are executed only as boolean evaluators: a given object either meets the defined conditions or not. Code Effects goes further by allowing those same conditions to drive data selection operations.

When a rule evaluates to `true` for a specific object, that object satisfies the rule’s logic and can be included in a query result. The `Filter(..)` [extension method](/decision-automation/rule-engine-ruleextensions) automatically converts the [XML definition](/decision-automation/business-rules-storage) of a rule into a LINQ expression that represents the same logical condition tree.

To understand the concept intuitively, consider this simple rule:

> Check if First Name is John

That same logic can be applied to datasets as:

> Get records where First Name is John

This logical transformation - from rule evaluation to query filtering - is the core idea behind Data Filtering in Code Effects. The system automatically performs this translation at runtime, enabling a single rule definition to serve both as a decision condition and as a database or collection filter.

This process - called <b>rule-to-query conversion</b> - is native to Code Effects and does not require manual translation or expression building.

## How It Works

When you load and compile a rule of *evaluation type*, Code Effects builds an internal tree of expression nodes representing each condition and logical operator. The `Filter(..)` method then traverses this tree and generates a LINQ predicate compatible with both `IEnumerable<T>` and `IQueryable<T>` sources.

For example, consider a rule defined as:

> *Check if Customer Age is greater than or equal to 18 and Customer Active is equal to True*

That same rule can be applied to an in-memory collection or to an entity query:

```csharp
using CodeEffects.Rule.Engine;

// 'customers' is an IQueryable<Customer> or IEnumerable<Customer>
// 'rule' is the Rule XML string of an evaluation type rule

var adults = customers.Filter(rule);
```

The engine translates this rule into the equivalent LINQ expression:

```csharp
customers.Where(c => c.Age >= 18 && c.IsActive);
```

This allows a business rule (written and maintained by non-developers) to act as a live filter on any compatible dataset.

## Usage Scenarios

The Data Filtering feature is highly versatile and can be integrated into a wide range of applications. Typical use cases include:

- <b>Web search forms</b> — Allow users to define advanced search conditions through a friendly rule editor instead of hardcoded query builders.
- <b>Complex data mining or analytics filters</b> — Dynamically filter large datasets in reporting or dashboard applications without redeploying code.
- <b>Dynamic report generation</b> — Enable users or administrators to define filtering conditions for reports directly in the UI.
- <b>Administrative panels</b> — Build rule-based filters that determine which records or users appear in management interfaces.
- <b>Workflow automation</b> — Use rules to filter which entities or transactions should trigger a given process stage.
- <b>AI and decision-support systems</b> — Apply adaptive filters that refine datasets before model evaluation or business decision execution.

Essentially, any scenario that requires a user-defined data query or conditional selection can benefit from Code Effects Data Filtering.

## Supported Data Sources

You can apply the `Filter(..)` method to any LINQ-compatible data source, including:

- `IEnumerable<T>` and `IQueryable<T>` objects
- Entity Framework or LINQ-to-SQL queries
- In-memory lists and arrays
- Custom LINQ providers

When used with `IQueryable<T>`, Code Effects automatically generates an expression tree that the underlying provider translates into a database query. When used with `IEnumerable<T>`, filtering happens in memory.

No configuration changes or special adapters are required: the same Rule XML can be used interchangeably across both types of sources.

For obvious reasons, the `Filter(..)` method works only with [evaluation type](/decision-automation/business-rule-evaluation-type) rules, since [execution type](/decision-automation/business-rule-execution-type) rules can modify field values or trigger actions and therefore cannot be represented as pure expressions.

## Performance and Optimization

Because `Filter(..)` produces a real LINQ expression tree, the final query executes natively in the target provider. When applied to large datasets through LINQ-to-SQL or Entity Framework, filtering is performed directly in the database, ensuring maximum performance and minimal memory usage.

The engine caches parsed rules internally for reuse, reducing the overhead of repeatedly translating the same Rule XML into an expression tree.

## Example: Filtering Entity Data

```csharp
using System.Linq;
using CodeEffects.Rule.Engine;

public IQueryable<Customer> GetFilteredCustomers(string ruleXml)
{
	var customers = dbContext.Customers; // IQueryable<Customer>

	// Filter customers based on a business rule
	var result = customers.Filter(ruleXml);

	return result; // IQueryable<Customer> representing the filtered query
}
```

This simple code snippet enables dynamic, user-defined queries that can change at runtime without modifying or redeploying your application.

## Integration Notes

- Only **evaluation type** rules are eligible for data filtering.
- Data Filtering works transparently with complex nested conditions, parentheses, and collection logic (**Exists...**, **Does not exist...** menu options).
- Custom fields and in-rule methods are supported **as long as** they can be represented as LINQ expressions.
- Rules referencing non-translatable logic (such as I/O, external services, or side-effects) will raise a `RuleException` when used in `Filter(..)`.

## Live Demonstration

You can explore a live demonstration of Code Effects data filtering. The demo showcases how an end-user can visually author rules in the [Rule Editor](/decision-automation/business-rule-editor) and immediately apply them as filters to Entity Framework data source, all without writing or modifying a single line of code.

## Summary

Data Filtering turns business rules into reusable, query-ready expressions. By bridging decision logic and data selection, Code Effects enables developers to:

- Centralize business logic in readable XML rules
- Reuse rules for both object evaluation and dataset filtering
- Allow non-technical users to define live filters through a web interface
- Maintain strict separation between business policy and data access layers

This capability is unique to the Code Effects platform and forms a key component of its overall decision automation architecture.

## Production Example

<a href="/Resources/Images/Doc/CodeEffects-Data-Filtering-Report-Sample.jpg" target="_blank"><img src="/Resources/Images/Doc/CodeEffects-Data-Filtering-Report-Sample.jpg" alt="Code Effects Report Sample" width="50%"/></a>