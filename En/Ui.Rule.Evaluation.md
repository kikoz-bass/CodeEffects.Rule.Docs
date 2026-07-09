
# Evaluation Type Rules

> Returns a boolean result without executing actions. Supports [Rule-Based Data Filtering](/decision-automation/business-rule-data-filtering).

## Purpose

Evaluate a condition tree against a [source object](/decision-automation/business-rule-source-object) and produce a boolean decision. Typical uses include eligibility checks, segmentation, routing, pricing tiers, KYC/AML gates, underwriting criteria, and feature flags.

## Engine Behavior

- Compiles the rule into a predicate over the source type; evaluation yields `true` or `false`.
- Supports Data Filtering: apply the same rule as a LINQ predicate via `Filter(..)` method to filter `IEnumerable<T>` / `IQueryable<T>` sources.
- Supports [Reusable Rules](/decision-automation/business-rules-reusable); circular references are blocked by automatic validation built into the [Rule Editor](/decision-automation/business-rule-editor).
- Authoring/evaluation semantics are identical whether the source surface comes from a [.NET class](/decision-automation/business-rule-source-object-class), [Source XML](/decision-automation/business-rule-source-object-xml), or [FlexSource](/decision-automation/business-rule-flex-source).

## C# - Single Evaluation

```csharp
using CodeEffects.Rule.Engine;

bool isApproved = new Evaluator<Application>(ruleXml).Evaluate(app);
```

## LINQ - Data Filtering

```csharp
var filteredData = data.Filter(ruleXml); // IEnumerable<T> or IQueryable<T>
```

## Authoring Surface

- Fields, operators, constants, in‑rule methods (methods exposed as fields), numeric calculations, and parentheses/grouping.
- Actions are disabled for evaluation type rules.

## Best Practices

- <b>Prefer evaluation</b> when a pure decision is sufficient; this keeps authoring simple.
- <b>Encapsulate complexity</b> using in‑rule methods instead of exposing infrastructure fields.