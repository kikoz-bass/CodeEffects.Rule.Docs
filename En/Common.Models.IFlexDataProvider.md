
# IFlexDataProvider Interface

<em><small>**Assembly:** CodeEffects.Rule.Common.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Common.Models`</small></em>

## Syntax

```csharp
using System;

namespace CodeEffects.Rule.Common.Models
{
	public interface IFlexDataProvider
	{
		object GetProperty(object target, string name);

		object CallMethod(object target, string name, params object[] args);

		void SetProperty(object target, string name, object value);

		Type GetPropertyType(string propertyName);

		Type GetMethodReturnType(string methodName);
	}
}
```

## Remarks

This interface is used by the [FlexSource](/decision-automation/business-rule-flex-source) technology. Refer to the corresponding topic for more details.