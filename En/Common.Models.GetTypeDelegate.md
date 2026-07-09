
# GetTypeDelegate Delegate

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Models`</small></br>
<small>**Introduced in Version:** [5.5.1.8](/version/new-gettype-delegate-and-client-enhancements/5518)</small></em>

## Summary

Defines the signature of a public method that takes a type's assembly-qualified name as its single parameter and returns the corresponding `System.Type`.

## Syntax

```csharp
public delegate Type GetTypeDelegate(string typeFullName);
```

## Parameters

- ### typeName<br/>
	<small>Type: `System.String`</small>

	The assembly-qualified name or the alias name of the type.

## Returns

<small>Type: `System.Type`</small>

## Remarks

If assemblies that declare in-rule methods or rule actions are updated to newer or different versions, all references saved in [Rule XML](/decision-automation/business-rules-storage) or [Source XML](/decision-automation/business-rule-source-object-xml) become obsolete because they are stored as *assembly-qualified names*. In such cases, you need to update those references accordingly.

This delegate helps resolve type versioning issues. Implement your own type resolver method following the signature of the `GetTypeDelegate` delegate, and assign it to the `TypeGetter` property of the [`CodeEffects.Rule.Editor.Control`](/decision-automation/rule-editor-control) editor's class to ensure that the editor loads your types according to your custom logic.

If set, the editor will attempt to invoke your method and use the resulting `System.Type`. If your method returns `default`, the editor will attempt a standard `Type.Load(..)` call. The editor rethrows standard reflection exceptions.

## Example

```csharp
using CodeEffects.Rule.Editor;

namespace MyProject
{
	public class MyClass
	{
		public System.Type GetMyType(string typeFullName)
		{
			return MyTypeLoader.GetThatType(typeFullName);
		}

		public Control GetControl(string divEditorClientContainerID)
		{
			var editor = new Control(divEditorClientContainerID)
			{
				Mode = RuleType.Execution,
				SourceType = typeof(Patient),
				TypeGetter = GetMyType
			};
		}
	}
}
```