# Understanding Code Effects FlexSource

This article walks you through a minimal, production-ready implementation of **FlexSource** — a technology that lets the Rule Engine evaluate rules against runtime-defined data (no compile-time POCOs, no code generation).

## Why FlexSource Exists

Most .NET applications rely on strongly typed classes to define the “shape” of data used by business rules.  
But in many systems - such as multi-tenant, configurable, or metadata-driven applications - the data shape isn’t fixed until runtime.

FlexSource solves this by allowing Code Effects to interact with **dynamic data models**.  
You can define and evaluate rules against data whose fields and methods are defined at runtime, without recompiling or generating code.

## Why use FlexSource instead of Adaptive Source?

The FlexSource option is ideal when you want to define the [Rule Editor](/decision-automation/business-rule-editor)'s [source object](/decision-automation/business-rule-source-object) entirely in code at runtime without introducing the additional infrastructure required by [Adaptive Source](/decision-automation/business-rule-adaptive-source). It allows your application to construct fields, methods, actions, and other source elements programmatically using familiar .NET patterns, making it a natural choice when the available rule elements depend on runtime conditions such as user roles, tenant configuration, feature flags, database settings, or other application-specific logic.

Like [Source XML](/decision-automation/business-rule-source-object-xml), FlexSource determines the editor's available elements before rule authoring begins. Once the source has been built and the editor initialized, the entire generated source remains available throughout the authoring session. This provides a straightforward way to create dynamic editor experiences while retaining a relatively simple implementation model.

The trade-off is that FlexSource does not continuously adapt as the rule itself evolves. Unlike Adaptive Source, it cannot refine menus and available options based on the rule's current authoring context. Consequently, features that rely on real-time context awareness during rule construction, such as [Context Resetting](/decision-automation/business-rule-context-resetters), are not available when using the FlexSource option.

## What is FlexSource?

FlexSource creates a bridge between the **Rule Editor** and the **Rule Engine** when no static C# type exists.

<table style="margin-top:40px;margin-bottom:40px;width:100%;">
	<tr>
		<th width="35%">Role</th>
		<th width="15%">Used by</th>
		<th>Purpose</th>
	</tr>
	<tr>
		<td>FlexSource Type (inherits System.Type)</td>
		<td>Rule Editor</td>
		<td>Describes what fields, properties, and methods exist in your dynamic object so rule authors can use them.</td>
	</tr>
	<tr>
		<td>Flex Data Provider (implements IFlexDataProvider)</td>
		<td>Rule Engine</td>
		<td>Provides property values and executes methods when evaluating a rule at runtime.</td>
	</tr>
</table>

Instead of referencing a static C# class, you provide these two lightweight components that tell Code Effects **how to discover**, **display**, and **execute** your dynamic object’s members.

## Key Concept: “Mocking a Type”

Think of the `System.Type` - derived class as a *mock type*.  
The Rule Editor reflects over it as if it were a real class.  
You decide what `GetProperties()` and `GetMethods()` should return — based on your runtime metadata.

Meanwhile, the `IFlexDataProvider` tells the Rule Engine how to actually **get**, **set**, or **invoke** those members on live data when the rule executes.

## What the Rule Editor and Engine Expect

- <b>At design time (Rule Editor)</b> — You must declare a class (the FlexSource) that inherits from `System.Type` and overrides several of its members. The editor uses reflection to query your class for all public fields, properties, and methods not decorated with the [`ExcludeFromEvaluation`](/decision-automation/rule-common-attributes-excludefromevaluation) attribute.  
  The goal is to give the editor a `Type` that represents your dynamic structure.

	Here are the required `System.Type` members to override:

	```csharp
	using System;
	using System.Reflection;

	string Name { get; }
	string FullName { get; }
	string AssemblyQualifiedName { get; }
	Type BaseType { get; }
	Module Module { get; }
	Type UnderlyingSystemType { get; }
	Assembly Assembly { get; }
	object[] GetCustomAttributes(bool inherit);
	FieldInfo[] GetFields(BindingFlags bindingAttr);
	Type[] GetInterfaces();
	MethodInfo[] GetMethods(BindingFlags bindingAttr);
	PropertyInfo[] GetProperties(BindingFlags bindingAttr);
	TypeAttributes GetAttributeFlagsImpl();
	```

- <b>At evaluation time (Rule Engine)</b> — The evaluator only needs a small set of methods to get or set property values and invoke methods on your runtime data. Your data class must implement the [`IFlexDataProvider`](/decision-automation/rule-common-models-iflexdataprovider) interface:

	```csharp
	using System;
	
	object GetProperty(object target, string name);
	object CallMethod(object target, string name, params object[] args);
	void SetProperty(object target, string name, object value);
	Type GetPropertyType(string propertyName);
	Type GetMethodReturnType(string methodName);
	```

You can combine both roles in one class (inherit `System.Type` and implement `IFlexDataProvider` at the same time), but most real-world mission-critical systems keep them separate because these data-oriented classes typically have different implementations, expose different sets of members, and are often defined on different platforms.

> <b>Tip:</b> When combining both roles, ensure your `System.Type` overrides are safe for reflection and your `IFlexDataProvider` calls are deterministic to maintain consistent evaluations.

## When to Use FlexSource

Use FlexSource when:

- Your data model is <b>defined at runtime</b> (for example, metadata tables or configurable schemas).
- You need to author rules <b>before</b> the final data shape is known.
- You want to [evaluate](/decision-automation/business-rule-evaluation-type) and optionally [execute](/decision-automation/business-rule-execution-type) rules without compiling classes.

Avoid FlexSource when:

- You already have stable, strongly-typed POCOs.  
  In that case, the regular [Source Object](/decision-automation/business-rule-source-object-class) flow is simpler and allows [Data Filtering](/decision-automation/business-rule-data-filtering) (LINQ translation) more directly.

> <b>Note:</b> [Data Filtering](/decision-automation/business-rule-data-filtering) with FlexSource may be limited depending on your LINQ provider. Use strongly-typed sources for LINQ translation scenarios.

## Mental Model

- **Design time (Editor)** — The editor reflects over your `System.Type`-derived class to list fields and methods. Authors build rules visually.  
- **Runtime (Engine)** — The engine asks your `IFlexDataProvider` to read or write those values on real data objects when rules are evaluated.

## Step-by-Step Example

FlexRecord - runtime container and FlexSource type

```csharp
using System;
using System.Collections.Generic;
using System.Globalization;
using System.Reflection;

public sealed class FlexRecord : Type
{
	private readonly Dictionary<string, object?> values = new(StringComparer.OrdinalIgnoreCase);

	public FlexRecord(IDictionary<string, object?> seed)
	{
		foreach (var kv in seed) values[kv.Key] = kv.Value;
	}

	public bool ContainsKey(string key) => values.ContainsKey(key);

	public object? this[string key]
	{
		get => values.TryGetValue(key, out var v) ? v : null;
		set => values[key] = value;
	}

	public bool InSegment(string segment)
	{
		var current = Convert.ToString(this["Segment"], CultureInfo.InvariantCulture) ?? string.Empty;
		return string.Equals(current, segment, StringComparison.OrdinalIgnoreCase);
	}

	public override string Name => nameof(FlexRecord);
	public override string FullName => "DynamicSources.FlexRecord";
	public override string AssemblyQualifiedName => typeof(FlexRecord).AssemblyQualifiedName!;
	public override Type BaseType => typeof(object);
	public override Module Module => typeof(FlexRecord).Module;
	public override Type UnderlyingSystemType => typeof(FlexRecord);
	public override Assembly Assembly => typeof(FlexRecord).Assembly;
	public override object[] GetCustomAttributes(bool inherit) => Array.Empty<object>();
	public override FieldInfo[] GetFields(BindingFlags bindingAttr) => Array.Empty<FieldInfo>();
	public override Type[] GetInterfaces() => Type.EmptyTypes;
	public override MethodInfo[] GetMethods(BindingFlags bindingAttr) => typeof(FlexRecord).GetMethods(bindingAttr);
	public override PropertyInfo[] GetProperties(BindingFlags bindingAttr) => typeof(FlexRecord).GetProperties(bindingAttr);
	protected override TypeAttributes GetAttributeFlagsImpl() => TypeAttributes.Public;
}
```

FlexShape - shape metadata

```csharp
using System;
using System.Collections.Generic;

public static class FlexShape
{
	public static readonly IReadOnlyDictionary<string, Type> Properties =
		new Dictionary<string, Type>(StringComparer.OrdinalIgnoreCase)
		{
			["Id"]        = typeof(int),
			["Name"]      = typeof(string),
			["Segment"]   = typeof(string),
			["IsActive"]  = typeof(bool),
			["Balance"]   = typeof(decimal)
		};

	public static readonly IReadOnlyDictionary<string, (Type ReturnType, (string Name, Type Type)[] Parameters)> Methods =
		new Dictionary<string, (Type, (string, Type)[])>(StringComparer.OrdinalIgnoreCase)
		{
			["InSegment"] = (typeof(bool), new[] { ("segment", typeof(string)) })
		};
}
```

`IFlexDataProvider` implementation

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Reflection;
using System.Globalization;
using CodeEffects.Rule.Common.Models;

public sealed class DictionaryFlexDataProvider : IFlexDataProvider
{
	private readonly IReadOnlyDictionary<string, Type> _props = FlexShape.Properties;
	private readonly Dictionary<string, MethodInfo> _methods;

	public DictionaryFlexDataProvider()
	{
		_methods = typeof(FlexRecord)
			.GetMethods(BindingFlags.Public | BindingFlags.Instance | BindingFlags.DeclaredOnly)
			.Where(m => FlexShape.Methods.ContainsKey(m.Name))
			.ToDictionary(m => m.Name, m => m, StringComparer.OrdinalIgnoreCase);
	}

	public object? GetProperty(object target, string name)
	{
		var rec = (FlexRecord)target;
		return rec.ContainsKey(name) ? rec[name] : null;
	}

	public void SetProperty(object target, string name, object value)
	{
		if (!_props.TryGetValue(name, out var expected))
			throw new InvalidOperationException($"Property '{name}' is not defined.");

		var rec = (FlexRecord)target;
		rec[name] = ChangeType(value, expected);
	}

	public object CallMethod(object target, string name, params object[] args)
	{
		if (!_methods.TryGetValue(name, out var mi))
			throw new MissingMethodException($"Method '{name}' not found.");

		var rec = (FlexRecord)target;
		return mi.Invoke(rec, args);
	}

	public Type GetPropertyType(string propertyName) =>
		_props.TryGetValue(propertyName, out var t) ? t : typeof(object);

	public Type GetMethodReturnType(string methodName) =>
		_methods.TryGetValue(methodName, out var mi) ? mi.ReturnType : typeof(void);

	private static object? ChangeType(object? value, Type targetType)
	{
		if (value is null) return null;
		var t = Nullable.GetUnderlyingType(targetType) ?? targetType;
		return Convert.ChangeType(value, t, CultureInfo.InvariantCulture);
	}
}
```

Wiring It Up

```csharp
// Design-time
var flexType = new FlexRecord(new Dictionary<string, object?>());
var editor = new CodeEffects.Rule.Editor.Control("divRuleEditor")
{
	Mode = CodeEffects.Rule.Common.Models.RuleType.Execution,
	SourceType = flexType
};

// Run-time
var target = new FlexRecord(new Dictionary<string, object?>
{
	["Id"]       = 123,
	["Name"]     = "Acme",
	["Segment"]  = "Enterprise",
	["IsActive"] = true,
	["Balance"]  = 2500.00m
});

var provider = new DictionaryFlexDataProvider();
string ruleXml = /* load from storage */;

var evaluator = new CodeEffects.Rule.Engine.Evaluator(ruleXml, provider);
bool success = evaluator.Evaluate(target);
```

## Practical Tips

- **Method whitelisting** — Only expose deterministic, safe methods.  
- **Type coercion** — Always convert inputs to their declared property or parameter types.  
- **Centralized metadata** — Keep your property/method catalog centralized.  
- **Null handling** — Decide whether missing fields are treated as `null` or as errors.  
- **Diagnostics** — Log missing members or type mismatches.  
- **Performance** — Cache reflection results when safe.

> FlexSource doesn’t store your data — it teaches Code Effects how to pretend your runtime-defined object is a real .NET class.
