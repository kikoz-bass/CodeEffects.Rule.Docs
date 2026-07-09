
# Rule Evaluation Performance Tips

## Rulesets

The Code Effects [rules engine](/decision-automation/business-rule-evaluation) is *extremely* fast. We have demonstrated evaluating an array of ONE MILLION randomly filled objects against an [evaluation type](/decision-automation/business-rule-types) rule in a single thread in less than 10 milliseconds (0.010 sec).

Even though [performance is excellent](/content/high-performance-business-rules-engine), it still makes sense to optimize your evaluations where possible.

The most expensive operation is compiling your rule(s) during initialization of the [`Evaluator`](/decision-automation/rule-engine-evaluator-generic) class. If you need to evaluate multiple rules against the same data, you can combine them into a single ruleset by inserting them into an empty XML document and passing that ruleset as your [RuleXML](/decision-automation/business-rules-storage) to the `Evaluator` constructor:

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

## Evaluator Instances

If at all possible, reuse `Evaluator` instances to run multiple data sets against the same rules. Each instance already holds the compiled rules, and recompilations are expensive, so caching and reusing one avoids unnecessary overhead.