
# Code Effects Implementation

The Code Effects decision automation platform consists of two major components: the [Rule Editor](/decision-automation/business-rule-editor) and the [Rule Engine](/decision-automation/business-rule-evaluation).

## Engine

The Rule Engine is a high-performance, thread-safe .NET component with no third-party dependencies. It can be integrated into any .NET project with just a few lines of code:

```csharp
using CodeEffects.Rule.Engine;

public class Test
{
	public static bool Evaluate<TData>(TData dataToBeEvaluated, string ruleFromEditor)
	{
		var evaluator = new Evaluator<TData>(ruleFromEditor);
		bool success = evaluator.Evaluate(dataToBeEvaluated);

		return success;
	}
}
```

There is much more to rule evaluation than this short code example. Refer to our comprehensive [API documentation](/decision-automation/business-rule-engine-api) for detailed information.

The **Engine Free** edition is available on [NuGet](https://www.nuget.org/packages/CodeEffects.Rule.Engine). Other editions are also [available](/content/business-rules-engine-editions). For implementation details, refer to the [Engine Implementation](/decision-automation/business-rules-engine-implementation) article. You can also fork any [demo project](https://github.com/orgs/codeeffects-software/repositories) for a working example.

## Editor

The Rule Editor is a **free**, web-based .NET/JavaScript component with no third-party dependencies. It can be implemented in a matter of hours in any new or existing .NET web application. It supports all current and legacy .NET web platforms, as well as all major client frameworks including React, Angular, and Vue.

Refer to the [Editor Implementation](/decision-automation/business-rules-editor-implementation) article for detailed instructions on integrating the Rule Editor into your web project. Fork, compile, and run any of our [demo projects](https://github.com/orgs/codeeffects-software/repositories) to see real-world rule management in action.

## Resources

- [How to Obtain Code Effects](/decision-automation/how-to-obtain-business-rules-engine)
- [Code Effects Basics](/decision-automation)
- [Live Demo](/business-rules-engine-demo)
- [Demo Project Repos](https://github.com/orgs/codeeffects-software/repositories)