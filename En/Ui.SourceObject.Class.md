
# Using an Existing .NET Class as a Source Object

If you already have — or plan to declare — a public .NET class, you can configure the [Rule Editor](/decision-automation/business-rule-editor) to use that class as its [source object](/decision-automation/business-rule-source-object).

Any public .NET class can be used as a source object, provided it declares at least one public property or one public method that returns a value type (an [in-rule method](/decision-automation/rule-common-attributes-method)). Make sure to read the Code Effects [Basics](/decision-automation) topic for details.

The [`CodeEffects.Rule.Editor.Control`](/decision-automation/rule-editor-control) class declares several properties you can use to specify which type the editor should use as its source object. The most common is `SourceType`. The code sample below creates an instance of the `Control` with the `Patient` class as its source type:

```csharp
using CodeEffects.Rule Editor;

var editor = new Control("divRuleEditorId")
{
    SourceType = typeof(Patient)
};
```

You can also use the full type name of the source object and the name of its declaring assembly:

```csharp
using CodeEffects.Rule Editor;

var editor = new Control("divRuleEditorId")
{
    SourceTypeName = "CodeEffects.Rule.Demo.Models.Patient",
    SourceAssembly = "CodeEffects.Rule.Demo"
};
```

The `SourceAssembly` property of the `Control` can be either the name of the referenced assembly that declares the source object or the fully qualified name of the assembly that you plan to load programmatically. It can be retrieved as `Assembly.GetAssembly(SourceObjectType).FullName`. The `SourceTypeName` property is the full name of the source object’s type. It can be retrieved as `sourceInstance.GetType().FullName`.

The `Control` class exposes additional source-related properties that can be used to further configure the source object. You can also customize how properties and methods are presented and evaluated by applying the custom attributes available in the [`CodeEffects.Rule.Common.Attributes`](/decision-automation/rule-common-attributes-namespace) namespace.

Our [demo projects](https://github.com/orgs/codeeffects-software/repositories) provide detailed examples of how to use .NET classes as source objects in Code Effects.