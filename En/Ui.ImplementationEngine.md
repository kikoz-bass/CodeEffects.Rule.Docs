# Engine Implementation

The Code Effects [business rules engine](/decision-automation/business-rule-evaluation) is a .NET Standard 2.0 assembly that runs on virtually any modern platform supporting the .NET runtime, including Windows, Linux, macOS, Docker containers, mobile platforms, and cloud environments. It supports all current .NET implementations, from .NET Framework 4.6.2+ to .NET 8.0+ and later, and can be integrated into virtually any application.

- ### Step 1

	Reference the Rule Engine in your project:

	- If you are using the [Engine Free](/content/business-rules-engine-editions) edition, reference the latest version of the `CodeEffects.Rule.Engine` assembly from [NuGet](https://www.nuget.org/packages/CodeEffects.Rule.Engine). It also installs the `CodeEffects.Rule.Common` assembly.
	- For all paid editions, [obtain](/decision-automation/how-to-obtain-business-rules-engine) the package from Code Effects Software and reference the `CodeEffects.Rule.Common` assembly together with either `CodeEffects.Rule.Engine.Enterprise` or `CodeEffects.Rule.Engine.Subscription`, depending on your [license edition](/content/business-rules-engine-editions).

- ### Step 2

	Use the `Evaluator` class to write the code that evaluates [your rules](/decision-automation/business-rules-storage) against your [source object(s)](/decision-automation/business-rule-source-object). Here is a simple example:

	```csharp
	using System;
	using E = CodeEffects.Rule.Engine;

	namespace MyProject
	{
		// The source object
		public class MySource
		{
			public int ID { get; set; } = 0;
			public string? Name { get; set; }
		}

		public class MyClass
		{
			public bool Test(MySource dataToEvaluate)
			{
				// Get the rule you want to use to evaluate the data
				var ruleXml = YourRuleStorage.GetYourRuleXml();
				// Create an instance of the Evaluator class
				var ev = new E.Evaluator<MySource>(ruleXml);
				// Evaluate the rule
				var success = ev.Evaluate(dataToEvaluate);
			}
		}
	}
	```

	### Remarks

	This example uses the generic [`Evaluator`](/decision-automation/rule-engine-evaluator-generic) class with default [options and settings](/decision-automation/rule-common-models-evaluationparameters). Depending on your implementation, you may prefer to use either the non-generic [`Evaluator`](/decision-automation/rule-engine-evaluator) class or one of the [`Evaluate()`](/decision-automation/rule-engine-ruleextensions) extension method overloads. Passing a rule or ruleset to the `Evaluator` constructor [compiles it into IL](/content/high-performance-business-rules-engine), allowing it to be evaluated efficiently against your [source object(s)](/decision-automation/business-rule-source-object).

	You may prefer to use the [`Filter()`](/decision-automation/rule-engine-ruleextensions) extension method if your application processes collections of [source objects](/decision-automation/business-rule-source-object) and you only need to remove items that do not satisfy one or more [evaluation type rules](/decision-automation/business-rule-evaluation-type). For more information about this unique capability of the Code Effects Decision Automation platform, see the [Data Filtering](/decision-automation/business-rule-data-filtering) article or use the [live demo](/linq-to-sql-entity-data-filter-demo).

	For additional guidance on optimizing rule evaluation performance, see the [Evaluation Performance Tips](/decision-automation/business-rules-evaluation-performance-tips) page.

## Engine Subscription Edition

If you have the [Engine Subscription](/content/business-rules-engine-editions) edition of the engine, your project must maintain a special license text file `codeeffects.ce` that needs to be renewed every 6 (six) month without needing to recompile or redeploy your project. This file is included in the [package](/decision-automation/how-to-obtain-business-rules-engine) that you get from Code Effects Software together with your product key and .NET assemblies.

Save the `codeeffects.ce` file anywhere on the machine or VM that runs your project and set the value of `Evaluator.LicenseFileFullPath` property to its full physical path **BEFORE** any Code Effects code:

```csharp
CodeEffects.Rule.Engine.Evaluator.LicenseFileFullPath = "C:\\FullPath\\codeeffects.ce";
```

To renew this file, log in to the [Code Effecst Portal](https://portal.codeeffects.com) using your product key, and click the License File icon to the right of your license title. This will generate and download the file to your drive.

Our system will **attempt** to notify you when your license is about to expire by sending an email to all addresses we have on record; however, we cannot guarantee that this notification will be delivered.

If the current license file in your project has expired, the `Evaluator` class adds a five-second delay to all invocations of the `Evaliate()` and `Filter()` methods, effectively turning your assembly into the [Engine Free](/content/business-rules-engine-editions) edition until the license file is renewed. It throws an `InvalidOperationException` if the license file is missing or corrupt.

## Resources

- [How to Obtain Code Effects](/decision-automation/how-to-obtain-business-rules-engine)
- [Code Effects Basics](/decision-automation)
- [Live Demo](/business-rules-engine-demo)
- [Demo Project Repos](https://github.com/orgs/codeeffects-software/repositories)