
# ActionAttribute Class

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Attributes`</small></em>

## Summary

**IMPORTANT** Generic methods with generic params cannot be used in [Source XML](/decision-automation/business-rule-source-object-xml) source model as in-rule methods or rule actions. Use plain [Reflection](/decision-automation/business-rule-source-object-class) model if your [source object](/decision-automation/business-rule-source-object) declares generic methods.

If applied to a qualified public method of the source object, this attribute marks that method as a rule action available for use in [execution-type](/decision-automation/business-rule-execution-type) rules. To qualify, the method must be `public`, static or instance, return `System.Void` and either be parameterless or have only parameters of the source object type or any [value type](/decision-automation/business-rules-data-types) supported by Code Effects.

This attribute can also be applied to a qualified method of any other public class declared in any assembly accessible to the code that evaluates the rule, making that method available as a rule action as well. In such cases, the source object must be decorated with the `ExternalActionAttribute`, which references the external method by its name and class type.

## Syntax

```csharp
[AttributeUsage(AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
public class ActionAttribute : System.Attribute,
	CodeEffects.Rule.Common.Attributes.IDescribableAttribute
```

## Properties

<div class="members">

- ### Description<br/>
	<small>Type: `System.String`</small>

	Gets or sets the description of the rule action method. Rule authors can view this description when they hover the mouse pointer over the action element in the Rule Area. The value of this property is ignored if [`Control.ShowDescriptionsOnMouseHover`](/decision-automation/rule-editor-control) is set to `false`. It is not recommended to use this property in [multilingual applications](/decision-automation/business-rule-multilingual-support); instead, use culture-specific custom [Source XML](/decision-automation/business-rule-source-object-xml) documents. This property is optional.

- ### DisplayName<br/>
	<small>Type: `System.String`</small>

	Gets or sets the display name of the action method in the Rule Area. Use this property to provide a more user-friendly name for rule actions when the method name is not sufficiently descriptive for non-technical rule authors, or when multiple overloads of the same method need to appear as distinct actions. It is not recommended to use this property in [multilingual applications](/decision-automation/business-rule-multilingual-support); instead, use culture-specific custom [Source XML](/decision-automation/business-rule-source-object-xml) documents. This property is optional. The default value is the name of the qualified method.

</div>

## Remarks

This attribute is optional. The [Rule Editor](/decision-automation/business-rule-editor) uses any qualified method of the [source object](/decision-automation/business-rule-source-object) as a rule action, regardless of whether it is decorated with the `ActionAttribute`. The Rule Editor ignores any qualified methods that are decorated with the `ExcludeFromEvaluationAttribute`. If the `ActionAttribute` is applied to a non-qualified method, an exception is thrown. All non-qualified methods that are not decorated with the `ActionAttribute` are ignored.

Remember that action methods can accept your source object as a parameter. It is a useful feature for static action methods. When a rule author uses such an action in a rule, parameters of the source object type are not displayed — Rule Editor passes them automatically. In the [Rule XML](/decision-automation/business-rules-storage), the parameter of the source object type is represented as `this`.

The following example illustrates how the rule would appear in Rule Area when a user uses both actions from the code sample below:

```
If ID is greater than 10 then Rename the Thing (New Name) and Set New ID (20)
```

Note that the second method is displayed with only the `int` parameter. Your instance of the `Thing` type is passed to the method automatically at evaluation time; it is omitted by the editor in the Rule Area.

## Example

```csharp
using CodeEffects.Rule.Common.Attributes;

namespace TestLibrary
{
	[Source]
	public class Thing
	{
		public int ID {get; set; } = 0;
		public string Name { get; set; }

		[Action("Rename the Thing", "Gives a different name to the Thing")]
		public void Rename(string newName)
		{
			this.Name = newName;
		}

		[Action("Set New ID", "Sets a new ID of the Thing")]
		public static void SetID(Thing thing, int id)
		{
			thing.ID = id;
		}
	}
}
```