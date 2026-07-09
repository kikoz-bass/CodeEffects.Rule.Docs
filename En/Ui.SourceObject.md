
# Source Object

## Overview

A source object defines the data surface that Code Effects uses for rule [authoring](/decision-automation/business-rule-management) and [evaluation](/decision-automation/business-rule-evaluation). At design time, the [Rule Editor](/decision-automation/business-rule-editor) reflects the source object to build menus for fields, operators, values, in‑rule methods, and rule action methods. At run time, the [Rule Engine](/decision-automation/business-rule-evaluation) evaluates rules against instances of the same source type (or its compatible representations) to produce a boolean result ([evaluation type](/decision-automation/business-rule-evaluation-type) rules) and/or execute actions ([execution type](/decision-automation/business-rule-execution-type) rules).

## Supported Forms of a Source Object

You can provide the source object to the editor in any of the following forms:

- [Adaptive Source](/decision-automation/business-rule-adaptive-source) - the newest and most advanced source option introduced in version 6. It dynamically generates the Rule Editor's menus at runtime based on the current authoring context. Instead of relying on a predefined source model, your application determines which fields, methods, actions, and options are available at each step, enabling context-aware rule authoring and seamless integration with external systems, services, and AI-driven experiences.
- [Reflection / .NET class](/decision-automation/business-rule-source-object-class) - the easiest to implement source option. The editor reflects public, value‑typed members as rule fields, in-rule methods, or rule actions. You can customize behavior with Code Effects [attributes](/decision-automation/rule-common-attributes-namespace) on the class, its members, parameters, and return values.
- [Source XML](/decision-automation/business-rule-source-object-xml) - a declarative XML representation of a source object that follows the public schema. Useful when the .NET type is unavailable, must be generated dynamicaly, or must be shaped differently for rule authors - in multilingual projects, for example.
- [FlexSource](/decision-automation/business-rule-flex-source) - a provider pattern that supplies source metadata dynamically when the underlying type is not declared or not available at design/run time.

> The same rule can be evaluated against objects of the .NET source type, or (when authored from Source XML / FlexSource) against data projected to the same field/method shape.

## Reflection and Field Discovery

By default, the editor discovers rule fields and methods as follows:

- Uses qualified methods as in-rule methods and actions, and public value‑typed properties and fields of the source type (excluding `System.Guid` and nullable enums) as rule fields.
- Traverses reference‑typed members breadth‑first up to `SourceAttribute.MaxTypeNestingLevel` (default: `4`) to discover additional value‑typed members. Reference types can be customized using the `ParentAttribute`.
- Converts .NET types to editor [field types](/decision-automation/business-rule-types): numeric, string, date, time, boolean, enum, and [collections](/decision-automation/business-rules-collection-data-types). This simplifies authoring while preserving accurate types during evaluation.
- Adds `isNull` / `isNotNull` operators for nullable members and strings. Labels of the static UI and rule elements such as `If`, `Else if`, or `Then` flow elements, and rule operators can be localized or overridden via [Help XML](/decision-automation/business-rule-multilingual-support).
- Ignores any field or method decorated with the `ExcludeFromEvaluationAttribute` to prevent its use in rule authoring or evaluation.

## Example: Limiting Deep Traversal

```csharp
using CodeEffects.Rule.Common.Attributes;

[Source(MaxTypeNestingLevel = 3)]
public class Car
{
	// Value-typed members are discovered as fields
	[Field(Max = 17, DisplayName = "VIN number")]
	public string VIN { get; set; }

	[Field(Min = 0, Max = 1000000)]
	public decimal Price { get; set; } = 0;

	public DateTime? Sold { get; set; } // gets isNull/isNotNull

	public Color Color { get; set; } = Color.White; // enum field

	// Reference-typed members may be traversed up to the nesting limit
	public Powertrain Powertrain { get; set; }
}
```

> Increasing `MaxTypeNestingLevel` may impact initial render time because more members must be reflected and shaped for the UI.

## In‑Rule Methods (Fields Backed by Methods)

An in‑rule method is a qualified public instance or static method that the editor can expose as if it were a field or a value source. To qualify:

- The return type must be a value type or `System.String` (except `System.Guid` and nullable enums).
- Parameters, if any, must be value types, `IEnumerable` collections, or `System.String`; the editor can also pass the source object itself as a special parameter. Methods that accept reference types  are ignored.
- Use [attributes](/decision-automation/rule-common-attributes-namespace) to control naming and input constraints.

```csharp
using CodeEffects.Rule.Common.Attributes;

[Method("Had Accidents")]
[return: Return(ValueInputType = ValueInputType.User)]
public bool HadAccidents([Parameter(ValueInputType = ValueInputType.Fields)] string vin)
{
	var events = VehicleService.GetAccidents(vin);
	return events != null && events.Count > 0;
}

// Appears to authors as a field:
// If Had Accidents ( VIN ) is equal to True then Ask For Discount
```

When a method takes the source object as its only parameter, it appears indistinguishable from a regular field to the author:

```csharp
using CodeEffects.Rule.Common.Attributes;

[Method("Resale Price")]
public static decimal GetResalePrice(Car car) // car is the source object
{
	decimal? price = VehicleService.GetPriceByVIN(car.VIN);
	return price is null or < 0 ? 0 : price.Value;
}

// Authoring example. The Contact Junkyard is a parameterless action method
// If Resale Price is less than 100 then Contact Junkyard
```

## Actions (Execution Type Rules)

Actions are instance or static public qualified methods that return `System.Void`. Their parameter type requirements are the same as for the in-rule methods. They can be declared on the source type or on external classes (see below) and are available only to execution type rules - they are ignored by evaluation type rules.

```csharp
using CodeEffects.Rule.Common.Attributes;

[Action("Change Oil")]
public void ChangeOil(Car car)
{
	var oil = VehicleService.GetOil();
	car.Oil.Replace(oil);
}
```

## External Methods and Actions

You can attach methods from external classes or even separate .NET assemblies to the source via `ExternalMethodAttribute` and `ExternalActionAttribute` classes:

```csharp
using CodeEffects.Rule.Common.Attributes;

[ExternalMethod(typeof(Helper), nameof(Helper.HadAccidents))]
[ExternalAction(typeof(Helper), nameof(Helper.ChangeOil))]
public class Car { /* … */ }
```

## Enumerations and Dynamic Menus

- Enum-typed members become enum fields. To hide specific enum items from authoring, mark them with `ExcludeFromEvaluationAttribute`. Use the `EnumItemAttribute` to customize each individual member of the enumeration.
- For data that should appear like enums but is stored externally, configure [Dynamic Menu Data Sources](/decision-automation/business-rules-dynamic-menu-data-sources). These present key–value pairs from databases or services as editor menus with the same UX as native enums and support display/value separation, filtering, and localization.

## Customization via Attributes (High Level)

Use attributes from `CodeEffects.Rule.Common.Attributes` namespace to shape the authoring surface:

- [Source](/decision-automation/rule-common-attributes-source) — scope, traversal depth, and source‑level metadata.
- [Field](/decision-automation/rule-common-attributes-field) — display name, description, min/max, date/time format, input mode (user vs fields), etc.
- [Method](/decision-automation/rule-common-attributes-method), [Action](/decision-automation/rule-common-attributes-action), [Return](/decision-automation/rule-common-attributes-return), [Parameter](/decision-automation/rule-common-attributes-parameter) — naming and input semantics for in‑rule methods, rule actions, and their parameters/return value.
- [ExternalMethod](/decision-automation/rule-common-attributes-externalmethod), [ExternalAction](/decision-automation/rule-common-attributes-externalaction) — attach qualified methods from other public classes or libraries.
- [Parent](/decision-automation/rule-common-attributes-parent) - customize reference types reflected and used by the editor.
- [ExcludeFromEvaluation](/decision-automation/rule-common-attributes-excludefromevaluation) — suppress a member, a method, or enum item from authoring/evaluation.

See the [API reference](/decision-automation/business-rule-engine-api) for precise property lists and defaults.

## Best Practices

- <b>Design for authors.</b> Shape the source so that field names read naturally. Prefer `DisplayName` and [Help XML](/decision-automation/business-rule-multilingual-support) for non‑technical labels and descriptions.
- <b>Control surface area.</b> Keep `MaxTypeNestingLevel` reasonable to balance completeness and performance.
- <b>Avoid leaky abstractions.</b> Use in‑rule methods to encapsulate computations and external lookups rather than exposing raw infrastructure types.
- <b>Guard side effects.</b> Only expose actions that are idempotent or operationally safe; use execution rules deliberately.
- <b>Version with intent.</b> When changing the source type, treat it as a contract with rule authors. Provide migration guidance if field or method semantics change.

## Compatibility

Code Effects assemblies target modern .NET platforms (via .NET Standard–based builds). The source‑object concepts above apply uniformly whether rules are authored from a [.NET class](/decision-automation/business-rule-source-object-class), [Source XML](/decision-automation/business-rule-source-object-xml), or [FlexSource](/decision-automation/business-rule-flex-source). Evaluation works against instances compatible with the authored source surface.