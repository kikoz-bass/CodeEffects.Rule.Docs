
# Decision Automation with Code Effects

Code Effects is not a standalone product that you install, connect to your systems, and rely on to automate your business decisions. Instead, it's an embeddable decision automation platform designed to become a natural part of your .NET application.

It enables your business teams to create, maintain, and execute business rules while your existing application remains in complete control of your data, security, and business processes.

## Our Platform In a Nutshell

The product consists of two core components:

- **The web-based, AI-enabled [Rule Editor](/decision-automation/business-rule-editor)** provides a visual interface for creating and validating business rules without writing code. It can be embedded into virtually any .NET web application.
- **The high-performance, thread-safe [Execution Engine](/decision-automation/business-rule-evaluation)** is a .NET library that loads, evaluates, and executes those rules at runtime. It runs anywhere .NET runs, including Windows, Linux, macOS, mobile platforms, containers, cloud services, and Native AOT deployments.

The editor and engine can run in the same application or in completely different projects, services, or environments. You decide where your rules are stored, how they are retrieved, and who has access to them. Code Effects simply becomes another capability of your existing system.

Integration is straightforward:

- [Embed the Rule Editor](/decision-automation/business-rules-editor-implementation) into your application.
- [Reference the Execution Engine](/decision-automation/business-rules-engine-implementation) where rules need to be evaluated.
- Define the [.NET classes](/decision-automation/business-rule-source-object-class) or [dynamic schemas](/decision-automation/business-rule-adaptive-source) that will serve as the [source objects](/decision-automation/business-rule-source-object) for your rules.
- Decide how and where your application [stores and retrieves rules](/decision-automation/business-rules-storage).
- Use your existing authentication and authorization to control access.

> Your .NET application continues to work exactly as before - it simply gains powerful decision automation.

When a business user creates or updates a rule, the editor simply tells your application, "This rule has changed." Your application decides where to store it and what business processes should follow. Likewise, when a user wants to load an existing rule, your application retrieves it, optionally verifies the user's permissions, and passes it back to the editor for display.

When your application receives [data that needs to be evaluated](/decision-automation/business-rule-source-object), it passes that data together with one or more rules to the Rule Engine. Depending on the [rule type](/decision-automation/business-rule-types), the engine can return a simple `true`/`false` result or invoke your own .NET methods, allowing the rules to drive virtually any business process your application supports.

This is, of course, only a high-level overview. Code Effects is a highly capable and sophisticated product. This documentation covers every aspect of its functionality in detail.

## System Architecture

Code Effects is built on a clean separation of concerns across three core layers: *UI*, *Storage*, and *Execution*.

1. ### Rule Authoring (The Frontend UI)

	The Rule Editor is a browser-native component that dynamically constructs its UI based on the metadata of your source object (your [C# classes](/decision-automation/business-rule-source-object-class) or [dynamic schema](/decision-automation/business-rule-adaptive-source)).

	- **Dynamic Context Menus:** The UI automatically generates operators and value selectors based strictly on the data types present in your [source object](/decision-automation/business-rule-source-object).
	- **Native Iteration:** Full support for evaluating `IEnumerable<T>` [collections](/decision-automation/business-rules-collection-data-types) directly within the rule logic.
	- **External Data Mapping:** Map native .NET enumerations or [dynamic menu data sources](/decision-automation/business-rules-dynamic-menu-data-sources) (like database key-value pairs) directly into the UI menus.
	- **Strict UI Validation:** The editor prevents the saving of syntactically invalid rules, missing operands, or circular dependencies.
	- **Attribute-Driven Customization:** Developers can decorate source object properties and methods with [custom attributes](/decision-automation/rule-common-attributes-namespace) to explicitly control how fields are rendered and evaluated.

1. ### Rule Persistence (The Storage Layer)

	Code Effects does not dictate your database schema. Rules are persisted as plain-text [Rule XML documents](/decision-automation/business-rules-storage) under a `<codeeffects>` root element.

	- **Zero Serialization Overhead:** Store rules received from the editor as an XML strings directly in your database (e.g., as an `nvarchar(max)` fields).
	- **Self-Contained:** The XML payload contains the complete AST (Abstract Syntax Tree) of the rule, including operator hierarchy and referenced sub-rules, making it completely portable across environments.

1. ### Rule Evaluation (The Backend Engine)

	The Execution Engine is a standalone, thread-safe NuGet package. It loads the Rule XML, compiles it in-memory into Common Intermediate Language (CIL), and evaluates it against your source object.

	- **Blistering Performance:** Because rules are compiled to native delegates rather than interpreted at runtime, evaluation overhead is near-zero.
	- **Thread-Safe Execution:** Safely evaluate rules concurrently across multiple threads in high-throughput web APIs or background workers.
	- **Action Invocation:** For [Execution Type rules](/decision-automation/business-rule-execution-type), the engine doesn't just return a `boolean`; it can automatically invoke mapped public local or external, instance or static C# methods, triggering data retrieval, notifications, or subsequent business processes.

	#### Basic Evaluation Implementation:

	```csharp
	using CodeEffects.Rule.Engine;

	public static class Demo
	{
		public static bool Evaluate(Customer data, string ruleXml)
		{
			// Instantiating the generic Evaluator compiles the rule logic
			var evaluator = new Evaluator<Customer>(ruleXml);
        
			// Executes the compiled native delegate against your object
			return evaluator.Evaluate(data);
		}
	}
	```

## LINQ Integration (Data Filtering)

Beyond standard `boolean` evaluation, any [Evaluation Type rule](/decision-automation/business-rule-evaluation-type) can be passed directly into collections or queryable data sources to act as a [dynamic filter](/decision-automation/business-rule-data-filtering). The engine translates the rule's XML syntax tree directly into a LINQ predicate `Expression<Func<T, bool>>`.

**Example rule**

```
Check if
	Order Amount is greater than 1000 and
	Order Status equals to Pending
```

**Applied as a Filter:**

```csharp
var filtered =
	orders.Where(
		o => o.Amount > 1000 &&
		o.Status == Statuses.Pending);
```

## Supported Frameworks & Deployment

Built on .NET Standard 2.0, the Execution Engine is entirely platform-agnostic. It runs anywhere the .NET runtime is supported:

- **Runtimes:** .NET Framework 4.6.2+, .NET 5.0+ (through .NET 8/9).
- **Web Frameworks:** ASP.NET Core, MVC, Razor Pages, Blazor, WebForms.
- **Client/Mobile:** Xamarin, MAUI.
- **Hosting:** Kestrel, IIS, Docker containers, AWS, Azure, Google Cloud, and Native AOT.

## Architectural Benefits

- **Pure Managed Code:** No external API calls, native C/C++ dependencies, or COM interop required.
- **Infinite Loop Protection:** The engine performs circular-dependency checks on all referenced rules prior to execution.
- **Composable Logic:** Rules can reference and execute [other rules](/decision-automation/business-rules-reusable) within the same storage scope, keeping rule definitions DRY.
- **Parentheses-Aware Parsing:** Complex `AND`/`OR` precedence is preserved exactly as authored in the UI without logic degradation.
- **Stateless by Design:** The engine maintains no internal state between evaluations, making it perfectly suited for load-balanced environments and serverless functions.

## TL;DR

Code Effects is an embeddable, zero-dependency .NET library that turns C# object models into a web-based visual rule editor. It compiles authored XML rules into native CIL delegates in-memory, delivering enterprise-grade decision automation and dynamic LINQ filtering at compiled-code speeds.