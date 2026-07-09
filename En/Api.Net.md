
# Code Effects .NET API

## Assemblies

- ### CodeEffects.Rule.Editor.dll

	#### Namespaces

	- <em><small>[`CodeEffects.Rule.Editor`](/decision-automation/rule-editor-namespace)</small></em>
	- <em><small>[`CodeEffects.Rule.Editor.Client`](/decision-automation/rule-editor-Client-namespace)</small></em>
	- <em><small>[`CodeEffects.Rule.Editor.Models`](/decision-automation/rule-editor-models-namespace)</small></em>
	- <em><small>[`CodeEffects.Rule.Editor.Utils`](/decision-automation/rule-editor-utils-namespace)</small></em>

- ### CodeEffects.Rule.Engine.dll

	#### Namespaces

	- <em><small>[`CodeEffects.Rule.Engine`](/decision-automation/rule-engine-namespace)</small></em>

- ### CodeEffects.Rule.Common.dll

	#### Namespaces

	- <em><small>[`CodeEffects.Rule.Common`](/decision-automation/rule-common-namespace)</small></em>
	- <em><small>[`CodeEffects.Rule.Common.Attributes`](/decision-automation/rule-common-attributes-namespace)</small></em>
	- <em><small>[`CodeEffects.Rule.Common.Models`](/decision-automation/rule-common-models-namespace)</small></em>

## Remarks

The **Code Effects .NET API** provides a comprehensive set of classes, interfaces, and extension methods for building, managing, and executing business rules within any .NET application. It powers the Code Effects [business rules engine](https://codeeffects.com) and the web-based rules editor [Rule Editor](/decision-automation/business-rule-editor).

This API is designed for developers who need to evaluate business rules dynamically, integrate rule logic into existing data models, or enable non-technical users to author and manage rules visually through the web-based editor.

## Key Features

- **Unified .NET Standard 2.0 assemblies** supporting .NET Framework 4.8.2+, .NET Standard 2.0+, .NET Core 2.0+, and .NET 8.0+
- **LINQ-based filtering** via the [`Filter()`](/decision-automation/business-rule-data-filtering) extension method for data mining and query scenarios
- **Runtime evaluation** of rules through the [`Evaluator`](/decision-automation/rule-engine-evaluator-generic) class
- **No external dependencies** — runs in any .NET environment, server or client
- **Intuitive Rule Editor UI** for non-developers
- **Full source licensing options** for enterprise and government use