
# Adaptive Source in Code Effects

Adaptive Source is an advanced [source object model](/decision-automation/business-rule-source-object) in Code Effects that enables fully dynamic, context-aware rule authoring. Unlike other source models that require the entire source object to be declared and loaded before the [editor initializes](/decision-automation/business-rule-editor-client-initialization), Adaptive Source allows your application to supply rule menus on demand, as the rule author creates or updates the rule.

Instead of forcing the [Rule Editor](/decision-automation/business-rule-editor) to operate on a static schema, Adaptive Source lets your application decide which [fields](/decision-automation/rule-editor-client-field), [methods](/decision-automation/rule-editor-client-function), setters, actions, [reusable rules](/decision-automation/business-rules-reusable), [enumerations](/decision-automation/rule-editor-client-enum), and [dynamic menu data sources](/decision-automation/business-rules-dynamic-menu-data-sources) should be available at each point in the rule.

This makes the editor behave more like an intelligent decision interface than a static form designer.

Adaptive Source was introduced in [version 6](/decision-automation/new-features-version-6-business-rules-engine) of the Code Effects platform and represents an industry-first approach to dynamic menu generation in natural-language [rule interfaces](/content/business-rules-ai-driven-user-interface). You can read more about the general concept behind Adaptive Source [here](/content/ai-powered-business-rules-engine). This article explains its basic implementation. Fork any of our [demo projects](https://github.com/orgs/codeeffects-software/repositories) for a complete implementation of [rule authoring](/decision-automation/business-rule-management) and [evaluation](/decision-automation/business-rule-evaluation) using Adaptive Source.

## Why Adaptive Source Matters

Traditional business rules systems assume that the entire source object must exist at design time. That assumption creates architectural limitations in modern enterprise systems where data, metadata, permissions, workflows, and AI-generated structures are often dynamic.

Adaptive Source removes those limitations. The editor no longer has to depend on reflection or statically declared source models. Instead, your application supplies only the rule elements that are relevant to the current rule context. This enables:

- Context-aware rule authoring
- AI-assisted rule composition
- Dynamic menu generation
- User-specific rule interfaces
- Runtime schema construction
- Multi-tenant metadata models
- External data-driven rule authoring
- Rules against JSON, XML, CSV, Excel, APIs, or non-.NET systems
- Integration with LLM-generated decision structures
- Massive enterprise schemas without loading full object graphs into the browser

## How Adaptive Source Works

Adaptive Source is based on a menu provider class that implements the [`CodeEffects.Rule.Editor.Models.IMenuProvider`](/decision-automation/rule-editor-models-imenuprovider) interface.

The provider becomes the editor’s source of rule metadata. It supplies [fields](/decision-automation/rule-editor-client-field), [methods](/decision-automation/rule-editor-client-function), [setters](/decision-automation/rule-editor-client-field), [actions](/decision-automation/rule-editor-client-function), [reusable rules](/decision-automation/business-rules-reusable), [enums](/decision-automation/rule-editor-client-enum), [data sources](/decision-automation/rule-editor-client-datasource) as dynamic UI menus whenever the editor needs them.

At a high level, the workflow looks like this:

1. The server initializes the [`Control`](/decision-automation/rule-editor-control) and assigns its `MenuProvider` property to implementation of [`IMenuProvider`](/decision-automation/rule-editor-models-imenuprovider).
1. The server returns editor settings and source-related initialization data to the client.
1. The client initializes the editor and passes the `adaptiveSource` object to [`$rule.init()`](/decision-automation/business-rule-editor-client-initialization) that includes `menuCallback`.
1. When the editor needs the next menu, it calls the configured `menuCallback`.
1. The callback sends the current rule's [`Context`](/decision-automation/rule-editor-client-context) object received from the editor to the server.
1. The server calls `Control.GetAdaptiveData()` and asks the supplied menu provider to generate the next menu using the rule context.
1. Server returns the generated data of the next menu to the callback on the client.
1. The callback passes it to the editor.

This keeps the editor lightweight while giving your application complete control over what rule authors see at each step.

## The IMenuProvider Interface

Adaptive Source revolves around the [`IMenuProvider`](/decision-automation/rule-editor-models-imenuprovider) interface:

```csharp
using System.Collections.Generic
using CodeEffects.Rule.Editor.Client;

namespace CodeEffects.Rule.Editor.Models
{
	public interface IMenuProvider
	{
		string SourceObjectTypeOrAliasName { get; }
		IMenu GetSettersAndActions(Context context);
		IMenu GetFieldsAndMethods(Context context);
		Field GetField(string fieldName);
		Field GetSetter(string fieldName);
		Field GetReusableRule(string ruleID);
		Function GetMethod(string methodName);
		Function GetAction(string methodName);
		Enum GetEnum(string enumName);
		DataSource GetDataSource(string dataSourceName);
	}

	public interface IMenu
	{
		string CacheID { get; set; }
		ICollection<Field> Fields { get; set; }
		ICollection<Function> Functions { get; set; }
	}
}
```

The provider has two main responsibilities:

- Generate dynamic menus during rule authoring.
- Resolve existing rule elements when a rule is loaded or updated.

The `GetFieldsAndMethods()` method supplies condition fields and in-rule methods. The `GetSettersAndActions()` method supplies value setters and rule actions. Both methods receive the current rule context, which allows your implementation to decide what should be available next.

The remaining methods resolve individual fields, methods, actions, reusable rules, enums, and dynamic menu data sources when the editor needs them.

## Implementing a Menu Provider

To use Adaptive Source, create a class that implements `IMenuProvider`. You can implement the provider in one of two ways:

1. First, you can use an existing .NET class as the source object. In this case, your provider can inspect that class and optionally use attributes declared in the `CodeEffects.Rule.Common.Attributes` namespace, much like the Reflection source model.
1. Second, you can manually supply all required metadata by creating `Field`, `Function`, `DataSource`, and `Enum` objects declared in the `CodeEffects.Rule.Editor.Client` namespace.

This means Adaptive Source can be used with regular .NET classes or with completely external metadata sources such as JSON, XML, CSV, Excel files, database schemas, APIs, or AI-generated structures.

The Adaptive Source demo project available for download on our website provides an implementation of `IMenuProvider` for an existing `Patient` class and demonstrates the entire functionality of rule authoring and evaluation using dynamic menus.

## Server-Side Initialization

With all source object models in Code Effects, the editor is represented on the server by the `CodeEffects.Rule.Editor.Control` class. In Adaptive Source model, the important step is assigning the `Control.MenuProvider` property. Setting this property tells the editor to use Adaptive Source.

The following ASP.NET Core API example returns the editor settings required by the client:

```csharp
public static class Api
{
	public static void MapEndpoints(this WebApplication app)
	{
		// Returns client settings for the editor
		app.MapGet("/api/settings", () =>
		{
			var settings = new Settings();
			var editor = GetControl(settings);
			
			// Set UI settings for the editor (Help String, etc.)
			settings.GlobalData = editor.GetGlobalData();
			
			// Set the source-related settings for the editor
			settings.EditorData = editor.GetEditorData();
			
			return Results.Ok(settings);
		});
		
		app.MapPost("/api/menu", (MenuRequest req) =>
		{
			var editor = GetControl(new Settings());
			
			// Load the rule context into the editor and generate
			// the next Adaptive Source menu using the MenuProvider
			var data = editor.GetAdaptiveData(req.Type, req.Context);
			
			return Results.Ok(data);
		});
	}
	
	private static Control GetControl(Settings settings)
	{
		// Use the ID of the div element that hosts the editor
		var editor = new Control("divEditor");
		
		// Set the editor to use Adaptive Source by supplying the menu provider
		editor.MenuProvider = new MenuProvider(typeof(Patient));
		
		editor.EvaluationMode = settings.Mode;
		editor.ShowHelpString = settings.HelpString;
		editor.ShowToolBar = settings.Toolbar;
		editor.ShowLineDots = settings.Dots;
		
		return editor;
	}
}

public class Settings
{
	public string? GlobalData { get; set; }
	public string? EditorData { get; set; }
	public bool Toolbar { get; set; } = true;
	public bool HelpString { get; set; } = true;
	public bool Dots { get; set; } = false;
	public EvaluationMode Mode { get; set; } = EvaluationMode.Execution;
}
```

The `/api/settings` endpoint prepares the editor settings and source-related initialization data. The `/api/menu` endpoint receives the current rule context from the client, initializes the editor with the same menu provider, generates the next adaptive menu, and returns it to the client in the editor’s proprietary JSON format.

## Client-Side Initialization

On the client, Adaptive Source is enabled by passing the `adaptiveSource` object to the `$rule.init()` method. This object defines the callback that the editor will call whenever it needs the next dynamic menu.

```javascript
// Declare editor as a global variable
var editor = null;

// Returns client settings for the editor
async function settings()
{
	const res = await fetch(
		'/api/settings',
		{
			headers: { 'Accept': 'application/json' }
		});
		
	const settings = await res.json();
	
	// Initialize the editor
	editor = $rule.init(
		{
			id: "divEditor",
			refData: "AnyStringDataYouWouldLikeToSendBetweenClientAndServer",
			showHelpString: true,
			showToolBar: true,
			globalData: settings.globalData,
			editorData: settings.editorData,
			saveRuleCallback: save,
			loadRuleCallback: load,
			deleteRuleCallback: remove,
			notifyCallback: info,
			
			// Passing the adaptiveSource object to the init() method
			// instructs the editor's main script to use Adaptive Source
			// on the client
			adaptiveSource:
			{
				menuCallback: menu
			}
		});
	
}

// Gets the rule context from the editor, sends it to the server,
// receives the menu back, and returns it to the editor
async function menu(args)
{
	const res = await fetch(
		'/api/menu',
		{
			method: 'POST',
			headers: { 'Content-Type': 'application/json' },
			body: JSON.stringify( { type: args.type, context: args.context })
		});
		
	const data = await res.json();
	editor.loadAdaptiveData(data);
}
```

The `menu()` function receives the current rule context from the editor and sends it to the `/api/menu` endpoint. The server uses the assigned `MenuProvider` to generate the next menu and returns that menu to the client. The client then calls `editor.loadAdaptiveData(data)` to load the menu into the editor.

Your `menu()` callback can be declared as either synchronous or asynchronous; the editor always invokes it synchronously. To maintain a responsive user experience, the editor automatically detects slow server responses and displays the "Loading..." menu until the requested menu data is received. You can customize the `min` delay before the loading menu is shown and the `max` time it remains visible to prevent UI flickering.

The editor client initialization article provides more details and available options for the `adaptiveSource` object and its properties.

## Source Metadata

When implementing the `IMenuProvider` interface, use the metadata objects declared in the `CodeEffects.Rule.Editor.Client` namespace to populate your menu provider:

- `Field`: Describes a property, field, setter, or reusable rule exposed to rule authors.
- `Function`: Describes an in-rule method or rule action.
- `Parameter`: Describes a method parameter.
- `Return`: Describes the return type of a method.
- `Settings`: Describes settings associated with a field, method, parameter, or return value.
- `Collection`: Describes collection-related settings.
- `Enum`: Describes an enumeration and its member values.
- `DataSource`: Describes a dynamic menu data source and its items.

Your menu provider can create these objects manually, load them from stored metadata, generate them from an existing .NET class, or build them dynamically from any external system.

The provider can be as simple or as sophisticated as your application requires.

The easiest way to implement `IMenuProvider` is to use an existing .NET class as your source object, provided it is available to the project hosting the Rule Editor at design time. This approach is very similar to the Reflection source model.

Our demo projects use this approach with the `Patient` test class. It uses the static helper methods of the `CodeEffects.Rule.Editor.Models.Source` class, such as `GetFields()`, `GetMethods()`, `GetEnums()`, and `GetDataSources()`, to extract metadata from the `Patient` class. These helper methods are provided as a convenient way to work with existing .NET source objects.

Besides implementing the `IMenuProvider` interface, the demo provider also caches fields and methods locally to minimize processing overhead. We included this caching implementation as a production-ready, copy-and-paste solution for typical projects. Your own menu provider can be much simpler and does not have to implement caching or any other additional functionality unless your application requires it.

Using an existing .NET class is not always possible, however. In many systems, the Rule Editor is implemented in a separate project or even a separate application that has no direct access to the source objects used later during rule evaluation. In other cases, the source metadata may come from external systems rather than .NET types.

In these scenarios, one approach is to create a lightweight "metadata" class that mimics the actual source object by declaring the same fields, methods, enumerations, and dynamic menu data sources that should be available in rules. The `Source` helper class can be used to extract that metadata and serialize it into one or more JSON files. Your menu provider would simply deserialize those JSON files and returns the requested metadata to the editor.

Storing source metadata as JSON provides several advantages. Metadata can be modified without recompiling your application, multiple metadata versions can be maintained, and metadata can be generated automatically from external systems. Internally, this is effectively the same metadata that the editor consumes every time it requests a new Adaptive Source menu.

## Context Resetters

Adaptive Source introduces Context Resetters, a unique feature that helps maintain the logical consistency of rules as users modify them. Any field or in-rule method can be designated as a context resetter. When the user changes the field, operator, or value of a resetter condition, the editor automatically removes all subsequent rule elements that may no longer be relevant in the new context. The removed elements can be restored immediately using the built-in Undo functionality.

Context Resetters are especially useful in dynamic search, reporting, workflow, and AI-assisted decision systems where earlier selections determine which fields and methods should be available later in the rule. They help prevent logically inconsistent rules while providing a more intuitive authoring experience.

This feature is available only with Adaptive Source. For implementation details and best practices, see the dedicated Context Resetters documentation article.

## RuleXML and Evaluation

> Adaptive Source affects rule authoring, not rule evaluation.

The `Evaluator` class and the RuleXML format are completely agnostic of the source model used by the editor to create or update a rule. For example, you can create a rule using SourceXML and later update it using Adaptive Source.

At evaluation time, Code Effects evaluates the rule against the source object and data supplied by your application. Adaptive Source simply gives you a dynamic way to author and manage those rules.

## AI-Assisted Authoring

Adaptive Source is especially useful in AI-assisted decision systems.

Because every menu request includes the current rule context, your menu provider can use that context to decide what should appear next. That decision can be based on static metadata, user permissions, workflow state, previous rule selections, other rules, or any LLM of your choice.

Combined with prompt-enabled method parameters, Adaptive Source supports true BYOAI rule authoring. Your application remains in control of the AI model, prompts, data, and decision logic.

## Recommended Implementation Approach

For small projects, a menu provider can generate metadata directly from an existing .NET source object.

For larger systems, it is often better to store source metadata separately as JSON, database records, or another external format, then have the menu provider return only the items required by the editor. This makes it possible to change source metadata without recompiling the application and keeps large enterprise source models from being loaded into the browser all at once.

The editor also uses proprietary caching technology to minimize server round trips during rule authoring. The IMenu documentation provides details on that.

## Summary

Adaptive Source turns source metadata into dynamic infrastructure.

By assigning `Control.MenuProvider` on the server and configuring `adaptiveSource.menuCallback` on the client, your application can generate rule menus on demand based on the current rule context. This makes the Code Effects Rule Editor:

- Context-aware
- AI-ready
- Metadata-driven
- Efficient with large source models
- Suitable for multi-tenant and enterprise systems
- Capable of authoring rules against .NET and non-.NET data sources

Download the Adaptive Source demo project to see a complete implementation of `IMenuProvider`, server-side initialization, client-side initialization, dynamic menus, rule authoring, and rule evaluation.