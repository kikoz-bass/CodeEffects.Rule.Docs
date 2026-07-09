
# Execution Type Rules

> Returns a boolean evaluation result and may invoke field value setters and rule actions.

## Purpose

Evaluate conditions and perform side effects such as sending notifications, calling services, writing files, enriching data, and changing state via rule actions.

## Engine Behavior

- Returns a boolean result, same as evaluation rules.
- May invoke:
	- In‑rule methods (public value‑returning methods) inside conditions.
	- Rule actions (public void methods) declared on the source type or attached via external classes.
	- Field value setters.
- Supports Loop and Ruleset editor/evaluation [modes](/decision-automation/business-rule-editor-modes) where enabled by editor settings for iterative scenarios.
- Not eligible for [Rule-Based Data Filtering](/decision-automation/business-rule-data-filtering).

## C# - Execution

```csharp
using CodeEffects.Rule.Engine;

bool ok = new Evaluator<Order>(ruleXml).Evaluate(order);
```

## Authoring Surface

- Everything available to evaluation rules plus Actions and Field Setters context menus.
- Actions and Field Setters can be invoked in `Then` / `Else` rule sections.

## Best practices

- <b>Guard side effects.</b> Keep actions idempotent and reversible where possible; add operational safeguards and limits for loops.