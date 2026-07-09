
# Using Type Aliases in Code Effects

Code Effects rules often reference .NET types: source objects, collection element types, enum types, external method declaring types, action declaring types, method parameters, return values, and data providers. In simple projects, those types are usually available directly to both the Rule Editor and the Rule Engine.

Larger systems are often different. Rules may be authored in one application and evaluated in another. The editor may receive only metadata about a source object, while the actual .NET types exist only in the service, network location, or codebase that hosts the engine.

Code Effects 6 introduced **Type Aliases** to support this scenario.

Type Aliases are supported by all Code Effects source object models and allow the editor and the engine to share a common logical name for any .NET type referenced by RuleXML. That name can be the assembly-qualified name of the type, its full name, its simple name, or any custom alias your application can resolve.

## Why Type Aliases Exist

The Rule Editor and Rule Engine do not need to be part of the same project. You can create rules in one application and evaluate them in another.

That separation is powerful, but it also means that the editor may not always have direct access to the same .NET classes that the engine uses during evaluation.

Type Aliases solve this problem by allowing both sides to agree on stable type names that are stored in RuleXML and resolved at runtime by your application. For example, instead of storing a full assembly-qualified name such as:

```
MyCompany.Healthcare.Models.Status, MyCompany.Healthcare,
	Version=6.0.0.0, Culture=neutral, PublicKeyToken=null
```

your rules can reference:

```
Status
```

or any other alias your organization defines.

## Creating a Type Resolver

To enable Type Aliases, create a method that matches the [`GetTypeDelegate`](/decision-automation/rule-common-models-gettypedelegate) delegate. The method receives a type name or alias and returns the corresponding .NET `Type`. Here is an example of such method taken from one of our [demo projects](https://github.com/orgs/codeeffects-software/repositories):

```csharp
public class Utils
{
	public static Type GetTypeByName(string typeName)
	{
		// Remove assembly name if present
		typeName = typeName.Contains(",") ? typeName.Split(',').First() : typeName;
		
		// Remove namespace if present
		typeName = typeName.Contains(".") ? typeName.Split('.').Last() : typeName;
		
		switch(typeName)
		{
			case "Patient": return typeof(Patient);
			case "Hospital": return typeof(Hospital);
			case "Hospitals": return typeof(ICollection<Hospital>);
			case "Visit": return typeof(Visit);
			case "Visits": return typeof(ICollection<Visit>);
			case "Status": return typeof(Status);
			case "State": return typeof(State);
			case "VisitType": return typeof(VisitType);
			case "Utils": return typeof(Utils);
			
			default:
			
				// Return null for any type this resolver does
				// not handle such as system value types
				return default;
		}
	}
}
```

The alias names are entirely up to your application. You can use short names, full type names, assembly-qualified names, database-driven names, or any custom naming convention.

## Setting the Type Resolver in the Rule Editor

The Rule Editor must use the same resolver when rules reference aliases:

```csharp
using CodeEffects.Rule.Editor;

var editor = new Control("divEditor");
editor.TypeResolver = Utils.GetTypeByName;
```

## Setting the Type Resolver in the Rule Engine

The Rule Engine uses the `EvaluationParameters.TypeResolver` property:

```csharp
using CodeEffects.Rule.Common.Models;
using CodeEffects.Rule.Engine;

var options = new EvaluationParameters { TypeResolver = Utils.GetTypeByName };
var ruleXml = YourStorage.GetRuleXml();

var evaluator = new Evaluator<YourSourceType>(ruleXml, options);
var success = evaluator.Evaluate(instanceOfYourSourceObject);
```

## How Type Resolution Works

When a type resolver is set, Code Effects first passes the type name found in RuleXML to the resolver. If the resolver returns a `Type`, Code Effects uses that type. If the resolver returns `null`, Code Effects attempts to load the type by name using the standard .NET type-loading mechanism.

If .NET cannot load the type, the original .NET exception is not suppressed. Your application is responsible for handling that case if needed.

This behavior allows your resolver to handle only application-specific types and aliases while letting Code Effects and .NET resolve common framework types such as system value types.

> If a type resolver is provided, the Rule Engine passes every type referenced in the rule to it, including common .NET system value types. Unless you have a specific reason to resolve those types yourself, your resolver should only handle your application's types and return `null` for any type it does not recognize. The Rule Engine will then resolve all standard .NET types automatically.

# Using Type Aliases in Adaptive Source

In Adaptive Source, use the `TypeOrAliasName` property of the `Settings` class. This property is available for fields, parameters, returns, and enums:

```csharp
using CodeEffects.Rule.Common.Models;
using CodeEffects.Rule.Editor.Client;

var field = new Field
{
	Value = "Patient.Status",
	DisplayName = "Patient's Status",
	DataType = DataType.Enum,
	Settings = new Settings
	{
		TypeOrAliasName = "Status"
	}
};
```

External methods can also use aliases for their declaring types, parameters, and return values:

```csharp
var externalMethod = new Function
{
	Value = "Patient.ExternalMethod",
	DisplayName = "External Method",
	Settings = new Settings
	{
		TypeOrAliasName = "Utils"
	},
	Parameters = new List<Parameter>
	{
		new Parameter
		{
			DataType = DataType.Enum,
			Description = "Patient's Status",
			Settings = new Settings 
			{
				TypeOrAliasName = "Status"
			}
		}
	},
	Return = new Return
	{
		DataType = DataType.Enum,
		Settings = new Settings
		{
			TypeOrAliasName = "Status"
		}
	}
};
```

## Using Type Aliases in Source XML

To use aliases in Source XML, replace the `type` and `class` attributes with alias names and set all `assembly` attributes to an empty string:

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes"?>
<codeeffects xmlns="https://codeeffects.com/schemas/source/6">
	<source name="Patient" webrule="6.0.2.13" utc="2026-05-30T06:22:08.8030" type="Patient" persisted="true">
		<fields>
			<function methodName="ExternalMethod" displayName="External Method" class="Utils" assembly="">
				<parameters>
					<input valueInputType="all" class="System.DateTime" type="date" nullable="false" format="MMM dd, yyyy" />
					<input type="enum" nullable="false" class="Status" assembly="" valueInputType="all" />
				</parameters>
				<returns valueInputType="all" class="Status" type="enum" nullable="false" assembly="" />
			</function>
			<enum propertyName="Status" class="Status" nullable="false" valueInputType="all" displayName="Status" assembly="" />
			<collection propertyName="Visits" displayName="Visits" generic="true" array="false" class="Visits" comparisonName="">
				<reference class="Visit" displayName="Visit" />
			</collection>
		</fields>
	</source>
</codeeffects>
```

If you already have rules that reference actual type names, you do not have to update every rule. Add those existing names to your resolver and return the corresponding types.

## Using Type Aliases in Reflection Source and FlexSource

In Reflection Source and FlexSource, set the `TypeOrAliasName` property on attributes from the `CodeEffects.Rule.Common.Attributes` namespace. This applies to fields, returns, external methods, external actions, data providers, and parameters:

```csharp
[ExternalAction(declaringTypeOrAliasName: "Utils", "ExternalAction")]
[Data(name: "Education", declaringTypeOrAliasName: "Utils", methodName: "ListOfSchools")]
public class Patient
{
	[Field(TypeOrAliasName = "Status", DisplayName = "Status")]
	public Status Status { get; set; } = Status.Undefined;

	[Field(DataSourceName = "Education", DisplayName = "School", AllowInCalculations = false)]
	public int SchoolID { get; set; } = 0;
}
```

## Type Aliases or Assembly Whitelisting?

Type Aliases and Assembly Whitelisting are separate features.

The job of the type resolver is to resolve type names. It maps names or aliases found in RuleXML to actual .NET types. If it cannot resolve a name, it should normally return `null` so Code Effects can continue with its default type-loading process.

The job of Assembly Whitelisting is different. It safeguards rule evaluation by blocking access to members declared in assemblies that your application has not explicitly allowed.

A type resolver can technically throw an exception or refuse to resolve certain names, but that is not its primary purpose. Its purpose is type resolution, not evaluation security.

For that reason, use the type resolver when you need aliases or custom type lookup. Use Assembly Whitelisting when you need to restrict which assemblies are allowed during evaluation. In applications that need both capabilities, use both features together.

## Type Names, Versions, and Strong Names

`GetTypeDelegate` treats type names in the same general way as Assembly Whitelisting treats assembly names with regard to versions and strong-name signatures.

You can resolve names by simple type name, full type name, assembly-qualified name, or your own custom aliases. The exact strategy belongs to your application and should match how your rules are authored and evaluated. For additional details about assembly versioning and signatures, see the Assembly Whitelisting article.

## Summary

Type Aliases make RuleXML more portable across applications, services, and deployment environments.

They are especially useful when rules are authored in one application but evaluated in another, when the editor works with metadata instead of declared source classes, or when organizations want stable logical names instead of full .NET type names inside rule documents.

> By sharing the same `GetTypeDelegate` implementation or its underlying configuration between the Rule Editor and Rule Engine, your application can keep rule authoring and rule evaluation independent while ensuring that every referenced .NET type is resolved consistently.