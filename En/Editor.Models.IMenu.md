
# IMenu Interface

<em><small>**Assembly:** CodeEffects.Rule.Editor.dll</small></br>
<small>**Namespace**: `CodeEffects.Rule.Editor.Models`</small></em>

> <span style="color:red;">This page is currently being updated to reflect the features and API changes introduced in Code Effects Decision Platform 6. Please check back in a few days for the completed documentation.</span>

## Syntax

```csharp
using System.Collections.Generic;
using CodeEffects.Rule.Editor.Client;

namespace CodeEffects.Rule.Editor.Models
{
	public interface IMenu
	{
		string CacheID { get; set; }
		ICollection<Field> Fields { get; set; }
		ICollection<Function> Functions { get; set; }
	}
}
```