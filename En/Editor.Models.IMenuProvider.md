
# IMenuProvider Interface

<em><small>**Assembly:** CodeEffects.Rule.Editor.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Editor.Models`</small></em>

## Syntax

```csharp
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
}
```

## Summary

Provides the server-side contract used by [Adaptive Source](/decision-automation/business-rule-adaptive-source) to supply fields, methods, setters, actions, reusable rules, enumerations, and dynamic menu data sources to the [Rule Editor](/decision-automation/business-rule-editor) on demand.

## Properties

<div class="members">

- ### SourceObjectTypeOrAliasName<br/>
	<small>Type: `System.String`</small>

	Gets the assembly-qualified type name or [alias name](/decision-automation/business-rule-type-handling-and-tyle-alias-names) of the source object used by the rules. Required.

## Methods

- ### GetAction<br/>
	<small>Returns: [`CodeEffects.Rule.Editor.Client.Function`](/decision-automation/rule-editor-client-function)</small>

	Returns metadata for a rule action referenced by a rule. The editor calls this method when it needs to resolve an action method used by an [execution type rule](/decision-automation/business-rule-execution-type).

	#### Parameters

	<div class="params">

	- *methodName* (`System.String`) - "Combined" name of the method (see Remarks below for definition.) This value must match the `Function.Value` assigned to the method metadata.

	</div>

- ### GetEnum<br/>
	<small>Returns: [`CodeEffects.Rule.Editor.Client.Enum`](/decision-automation/rule-editor-client-enum)</small>

	Returns metadata for an enumeration used by a field, method parameter, or method return value. Adaptive Source does not require the editor to reflect enum types at design time. Your menu provider supplies the enum metadata when the editor requests it.

	#### Parameters

	<div class="params">

	- *enumName* (`System.String`) - "Combined" name of the enum (see Remarks below for definition.) This value must match the `Enum.Name` assigned to the enum metadata.

	</div>

- ### GetDataSource<br/>
	<small>Returns: [`CodeEffects.Rule.Editor.Client.DataSource`](/decision-automation/rule-editor-client-datasource)</small>

	Returns metadata for a [dynamic menu data source](/decision-automation/business-rules-dynamic-menu-data-sources). These data sources can be loaded from any source available to your application, including databases, APIs, configuration files, or cached metadata.

	#### Parameters

	<div class="params">

	- *dataSourceName* (`System.String`) - "Combined" name of the data source (see Remarks below for definition.) This value must match the `DataSource.Name` assigned to the data source metadata.

	</div>

- ### GetField<br/>
	<small>Returns: [`CodeEffects.Rule.Editor.Client.Field`](/decision-automation/rule-editor-client-field)</small>

	Returns metadata for a field referenced by a rule. The editor calls this method when it needs to resolve a field by name, usually while loading or updating existing [RuleXML](/decision-automation/business-rules-storage).

	#### Parameters

	<div class="params">

	- *fieldName* (`System.String`) - "Combined" name of the field (see Remarks below for definition.) This value must match the `Field.Value` assigned to the method metadata.

	</div>

- ### GetFieldsAndMethods<br/>
	<small>Returns: [`CodeEffects.Rule.Editor.Models.IMenu`](/decision-automation/rule-editor-models-imenu)</small>

	Returns [fields](/decision-automation/rule-editor-client-field) and [in-rule methods](/decision-automation/rule-editor-client-function) that should be available in the next condition menu.

	#### Parameters

	<div class="params">

	- *context* ([`CodeEffects.Rule.Editor.Client.Context`](/decision-automation/rule-editor-client-context)) - Provides the current rule context and can be used to decide what should appear in the next menu.

	</div>


- ### GetMethod<br/>
	<small>Returns: [`CodeEffects.Rule.Editor.Client.Function`](/decision-automation/rule-editor-client-function)</small>

	Returns metadata for an in-rule method referenced by a rule. The editor calls this method when it needs to resolve an in-rule method by name.

	#### Parameters

	<div class="params">

	- *methodName* (`System.String`) - "Combined" name of the method (see Remarks below for definition.) This value must match the `Function.Value` assigned to the method metadata.

	</div>

- ### GetReusableRule<br/>
	<small>Returns: [`CodeEffects.Rule.Editor.Client.Field`](/decision-automation/rule-editor-client-field)</small>

	Returns metadata for a [reusable rule](/decision-automation/business-rules-reusable). Reusable rules are exposed to the editor as `Boolean` fields. The `ruleID` parameter identifies the reusable [evaluation type](/decision-automation/business-rule-evaluation-type) rule that should be loaded into the menu or resolved from [RuleXML](/decision-automation/business-rules-storage).

	#### Parameters

	<div class="params">

	- *ruleID* (`System.String`) - String value of the rule's `GUID` ID.

	</div>

- ### GetSettersAndActions<br/>
	<small>Returns: [`CodeEffects.Rule.Editor.Models.IMenu`](/decision-automation/rule-editor-models-imenu)</small>

	Returns [setters](/decision-automation/rule-editor-client-field) and [rule actions](/decision-automation/rule-editor-client-function) that should be available in the next action menu.

	#### Parameters

	<div class="params">

	- *context* ([`CodeEffects.Rule.Editor.Client.Context`](/decision-automation/rule-editor-client-context)) - Provides the current rule context and can be used to decide what should appear in the next menu.

	</div>

- ### GetSetter<br/>
	<small>Returns: [`CodeEffects.Rule.Editor.Client.Field`](/decision-automation/rule-editor-client-field)</small>

	Returns metadata for a settable field referenced by a rule. The editor calls this method when it needs to resolve a field used as a value setter. The returned field should represent a field that can be assigned a value by an [execution type rule](/decision-automation/business-rule-execution-type).

	#### Parameters

	<div class="params">

	- *fieldName* (`System.String`) - "Combined" name of the setter (see Remarks below for definition.) This value must match the `Field.Value` assigned to the method metadata.

	</div>

</div>

## Remarks

`IMenuProvider` is the main implementation point of Adaptive Source. To use Adaptive Source, create a class that implements this interface and assign its instance to the [`Control.MenuProvider`](/decision-automation/rule-editor-control) property:

```csharp
var editor = new Control("divEditor");
editor.MenuProvider = new MenuProvider(typeof(YourSourceObject));
```

Setting the `MenuProvider` property tells the editor to use the Adaptive Source model. From that point on, the editor uses your provider to request rule metadata as the rule author creates, loads, or updates rules.

The provider has two main responsibilities:

- Generate dynamic menus based on the current rule context.
- Resolve existing rule elements referenced by [RuleXML](/decision-automation/business-rules-storage).

This allows the editor to work without loading the entire source object into the browser.

> **IMPORTANT!** The `Value` properties of [fields](/decision-automation/rule-editor-client-field) and [methods](/decision-automation/rule-editor-client-function), as well as the `Name` property of [enums](/decision-automation/rule-editor-client-enum) and [data sources](/decision-automation/rule-editor-client-datasource) supplied by an Adaptive Source menu provider use **combined** names. A combined name must be in dot notation and consist of the source object's class name (or its [alias name](/decision-automation/business-rule-type-handling-and-tyle-alias-names)) followed by the declared name of the member.

The menu provider included in one of our [demo projects](https://github.com/orgs/codeeffects-software/repositories) automatically generates combined names. It provides a production-ready implementation of the `IMenuProvider` interface that you can use as a starting point or copy directly into your own projects.

## Combined Name Example

```csharp
// Source object
public class MySource
{
	public string UserID { get; set; } = 0;
}

// Meta description of the UserID property
var field = new Field { Value = "MySource.UserID", DisplayName = "User ID", Min = 0 };
```