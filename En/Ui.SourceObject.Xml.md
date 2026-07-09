
# Working with Source XML

## Introduction

The [attribute-driven](/decision-automation/business-rule-source-object-class) customization of [source objects](/decision-automation/business-rule-source-object) in Code Effects is highly effective. However, there are scenarios where you may not have a concrete .NET class at design time, or you want full control over the authoring structure — for example, in [multilingual scenarios](/decision-automation/business-rule-multilingual-support) or when you prefer not to rely on class reflection. In such cases, a declarative XML representation of your source object - called Source XML - is a viable alternative.

## Why Use Source XML?

You might choose Source XML for any of the following reasons:

- You want to skip the reflection process and reuse the same source definition across multiple sessions or HTTP requests.
- You need to present fields, in-rule methods, and actions in different languages and want to externalize display text.
- You need precise control over the metadata that the [Rule Editor](/decision-automation/business-rule-editor) uses, beyond what reflection allows.

## Why use Source XML instead of Adaptive Source?

The Source XML option is ideal when you need to dynamically shape the Rule Editor's available fields and methods before the editor is initialized, but do not require [Adaptive Source](/decision-automation/business-rule-adaptive-source)'s ability to build each rule interactively based on its current authoring context. For example, an application can generate Source XML on the fly - using [AI](/content/ai-powered-business-rules-engine), user roles, tenant settings, external configuration, or any other runtime criteria - to expose only the elements relevant to a particular user or scenario. This approach provides much of the flexibility of a dynamic source model while preserving the simplicity of the traditional editor initialization process.

The trade-off is that the entire generated source becomes available to the editor from the start of rule authoring, rather than evolving dynamically as the rule itself is being constructed. As a result, features that depend on continuous context awareness during authoring, such as [Context Resetting](/decision-automation/business-rule-context-resetters), are not available when using the Source XML option.

## How to Create Source XML

You can create a Source XML document in one of two ways:

- <b>Generate from an existing .NET class</b> — Use the Rule Editor on a temporary page, set its `SourceType` to your class, then call `GetSourceXml()` to extract the XML. Edit and validate the XML against the schema, then store it (file system, database) for reuse.

	```csharp
	using CodeEffects.Rule.Editor;

	var editor = new Control("divEditorContainer") { SourceType = typeof(MySourceClass) };
	string sourceXml = editor.GetSourceXml();
	```

- <b>Create manually from scratch</b> — Author the XML document based on the published schema instead of generating it via reflection. This offers full control. The [`Xml`](/decision-automation/rule-editor-utils-xml) class can help you get an empty SourceXML document:

	```csharp
	using System.Xml;
	using CodeEffects.Rule.Editor.Utils;

	string sourceSchemaLocation = Xml.SourceNamespace;
	XmlDocument sourceXml = XML.GetEmptySourceDocument();
	```

## Loading Source XML into the Rule Editor

Once you have a valid Source XML document, you can configure the Rule Editor to use it instead of a class:

```csharp
using System.Xml;
using CodeEffects.Rule.Editor;

string sourceXmlString = YourStorage.GetSourceXmlSomehow();
var sourceXml = new XmlDocument();
sourceXml.LoadXml(sourceXmlString);

var editor = new Control("divEditorContainer")
{
    SourceXml = sourceXml
};
```

Alternatively, you can use the `Control.SourceXmlFile` property to specify a file path.

## Multilingual and Variant Use-Cases

In applications supporting multiple languages/cultures, you may maintain separate Source XML documents per culture. Each document sets `DisplayName` and/or `Description` attributes for [fields](/decision-automation/rule-common-attributes-field), in-rule [methods](/decision-automation/rule-common-attributes-method), and [actions](/decision-automation/rule-common-attributes-action) in the target language. At runtime you load the appropriate version.

## Important Considerations

- Source XML must adhere to the specified schema. Since version 3.0, the Rule Editor uses schema versioning namespaces.
- Generic methods (methods with generic parameters) cannot be used in Source XML as in-rule methods or rule actions. Use a [.NET class](/decision-automation/business-rule-source-object-class) instead for those.
- If you later switch from Source XML to a corresponding .NET class (or vice-versa), ensure consistency of field, method, and action names so that stored rules remain valid.
- For multilingual scenarios, maintain consistent IDs or field names across culture variants to avoid authoring errors when switching.

## Summary

Using Source XML gives you full control of the authoring surface for the Rule Editor - allowing you to define available fields, methods, actions, and display names externally, without relying solely on reflection of classes. It is especially useful when a class is unavailable, when you need variant sources, or when you require multilingual labeling.