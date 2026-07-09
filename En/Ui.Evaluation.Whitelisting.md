
# Assembly Whitelisting in Code Effects

Code Effects 6 introduces Assembly Whitelisting as a runtime security feature for rule evaluation.

The feature allows your application to explicitly define which assemblies are approved for rule execution. If a rule attempts to access a source property, invoke an in-rule method, call an external method, or execute a rule action declared outside the approved assembly list, the evaluator throws a `RuleException` and immediately stops evaluation.

Assembly Whitelisting is designed for enterprise systems where Rule XML may originate from external sources, distributed storage, tenant environments, third-party systems, or AI-assisted rule generation workflows.

## Overview

Code Effects business rules are stored and delivered to the engine as Rule XML documents. This provides significant flexibility because rules can be stored, generated, exchanged, imported, restored, or synchronized using any infrastructure chosen by the host application. Rules may come from:

- databases
- file systems
- external repositories
- tenant-specific storage
- third-party integrations
- administrative tools
- backup systems
- AI-assisted rule generation processes

However, in systems where Rule XML may originate from sources that are not fully trusted or fully controlled, organizations may need an additional runtime security boundary.

Assembly Whitelisting provides that boundary.

It ensures that rule evaluation can interact only with code declared in assemblies explicitly approved by the host application.

## Why Assembly Whitelisting Matters

Rule XML defines executable decision behavior. In normal deployments, rules are created and managed through the Code Effects editor and evaluated by the engine inside the host application.

However, some enterprise architectures allow Rule XML to be imported, exchanged, synchronized, or supplied by external systems. In such environments, a maliciously modified Rule XML document could theoretically include references to members that were never intended to participate in rule evaluation but are still available to the application process at runtime.

For example, an altered rule could attempt to:

- invoke unauthorized external methods
- access unintended application services
- execute internal infrastructure code
- call unapproved AI integrations
- assign invalid or malicious values to writable properties
- interact with code paths outside the approved decision layer

If no runtime assembly restrictions are configured, the evaluator may be able to resolve and execute such references when the referenced assemblies are available to the application.

Assembly Whitelisting eliminates this category of risk by enforcing an explicit list of trusted assemblies during evaluation.

## How It Works

Assembly Whitelisting is implemented through the `AssemblyWhitelist` property of the `EvaluationParameters` class:

```csharp
public ICollection<string> AssemblyWhitelist { get; set; }
```

An instance of `EvaluationParameters` can be passed to the `Evaluator.Evaluate(...)` method. When the whitelist is `null`, no assembly whitelist restrictions are applied. When it is an empty collection, nothing can be evaluated or invoked, including members of the rule’s source object. When the whitelist contains one or more assembly names, the `Evaluator` validates all rule references against that list during evaluation.

If a rule attempts to access or invoke a member declared in an assembly that is not included in the whitelist, evaluation stops immediately and the evaluator throws a `RuleException`.

> **Why does an empty whitelist block all evaluation?**
>
> The `Evaluator` intentionally applies assembly validation to every member referenced by a rule, including properties and methods declared on the rule's source object. Rule definitions can originate from external systems, third parties, databases, user uploads, or other untrusted sources. A malicious rule could attempt to reference members from an unexpected assembly that happens to be available to the application at runtime. By requiring the source object's assembly to be explicitly whitelisted as well, the `Evaluator` guarantees that only assemblies deliberately approved by the application can participate in rule evaluation. As a result, an empty whitelist represents a complete **deny-all** policy and prevents any rule from being evaluated.

## Protected Rule References

Assembly Whitelisting applies to rule references that access or execute application members, including:

- source object properties
- source object property setters
- in-rule methods
- external methods
- rule actions
- external rule actions
- enums

This includes both direct source members and external methods referenced by the rule. The goal is to ensure that rules can only interact with approved application code during evaluation.

## Example Configuration

A typical implementation passes the whitelist as part of the evaluation parameters:

```csharp
using CodeEffects.Rule.Common.Models;
using CodeEffects.Rule.Engine;

var parameters = new EvaluationParameters
{
    AssemblyWhitelist = new List<string>
    {
        "MyCompany.DecisionServices, Version=2.1.0.0",
        "MyCompany.RuleActions",
        "MyCompany.AiIntegration, Version=3.0.12.56, Culture=neutral, PublicKeyToken=abc123xyz098"
    }
};

var result = evaluator.Evaluate(ruleXml, sourceObject, parameters);
```

Names of the assemblies you add to the whitelist matter. Depending on your specific needs, you might want to allow only specific version(s) of certain assemblies, enforce that certain assemblies be signed, or allow assemblies by name regardless of their versions or security characteristics.

- **To allow assemblies by name only** - Use just the assembly identifier, without version or signing metadata, by running `typeof(YourType).Assembly.GetName().Name`. In most cases, you can also obtain this name from the assembly file name without its extension. This is the least restrictive option, allowing you to use any version of the assembly should you update it later, without modifying the whitelist or recompiling and redeploying your code.
- **To allow only specific assembly names and versions** - Use the assembly name together with the version you want to enforce, separated by commas and whitespace. You can obtain it by running `typeof(YourType).Assembly.FullName` and removing the culture and signing metadata from the resulting string. In this case, the `Evaluator` will allow only the specified version of the assembly. Otherwise, it will stop evaluation and throw the `RuleException`.
- **To allow only signed assemblies by their public key token** - Name-only matching is spoofable: anyone can compile an unsigned assembly using the same name. If you include an entry such as `MyCompany.AiIntegration, Version=3.0.12.56, Culture=neutral, PublicKeyToken=abc123xyz098`, the `Evaluator` closes that loophole by enforcing the public key token. Naturally, use this option only if the assembly is actually signed. You can obtain the full assembly identity by running `typeof(YourType).Assembly.FullName`.

> Assembly matching is case-insensitive, consistent with the way the .NET runtime treats assembly simple names. However, the `Evaluator` does not merely compare strings using `OrdinalIgnoreCase`. Instead, it uses the `System.Reflection.AssemblyName` class to reliably parse and enforce matching of each segment of every assembly identity.

## Protecting Against Untrusted Rule Sources

Assembly Whitelisting is especially important when Rule XML can come from outside the immediate control of the application. Examples include:

- externally submitted rules
- rules imported from partners or customers
- rules restored from storage that may have been modified
- rules synchronized across environments
- rules generated by automated systems
- rules created with AI-assisted tooling
- rules transferred between tenants or deployments

In these scenarios, Rule XML should be treated as executable configuration. Even if the editor restricts what users can select during rule authoring, runtime validation is still necessary when the final XML may be modified outside the editor.

Assembly Whitelisting ensures that Rule XML cannot expand its execution surface simply by referencing code that happens to be available to the application process.

## Runtime Enforcement, Not UI Filtering

Editor configuration controls what rule authors can see and select during rule creation. Assembly Whitelisting controls what the evaluator is allowed to execute during runtime. These are separate security layers.

Editor restrictions improve the authoring experience and reduce user error. Assembly Whitelisting provides runtime enforcement even when Rule XML is imported, edited manually, restored, generated, or otherwise supplied outside the normal editor workflow.

For security-sensitive systems, both layers should be used together.

## Recommended Architecture

For most enterprise implementations, the recommended approach is to expose rule-callable logic through dedicated assemblies. For example:

```
MyCompany.DecisionServices.dll
MyCompany.RuleActions.dll
MyCompany.AIIntegration.dll
```

The application can then pass only these assemblies and assembly that declares the main source object to `AssemblyWhitelist`. This creates a clean execution boundary between:

- approved decision services
- rule-callable business logic
- AI integration logic
- general application code
- internal infrastructure
- administrative services
- unrelated third-party libraries

Only reviewed and approved assemblies become available to rule evaluation.

## Assembly Whitelisting and AI Integration

Assembly Whitelisting is particularly useful when using Prompt-enabled methods or other AI-related rule features introduced in Code Effects 6.

Organizations can isolate AI integration logic into a dedicated assembly and include only that assembly in the whitelist. This ensures that AI-enabled rules can call only approved AI methods and cannot invoke experimental, internal, or unrelated AI infrastructure accidentally or maliciously. This helps preserve:

- prompt governance
- model access control
- approved inference paths
- compliance auditing
- operational consistency
- security boundaries

while still allowing AI inference to participate in deterministic rule evaluation.

## Security Benefits

Assembly Whitelisting strengthens rule evaluation security by providing:

- explicit runtime assembly control
- protection against modified or untrusted Rule XML
- reduced execution surface
- prevention of unauthorized method invocation
- protection against unintended property access or assignment
- stronger separation between rule-callable code and internal application code
- safer use of externally generated or AI-generated rules
- improved governance for multi-tenant and regulated systems

Because enforcement happens inside the evaluator, the whitelist remains effective regardless of how the Rule XML was created or delivered.

> In Code Effects, rules are treated as potentially untrusted input, while the Assembly Whitelist is treated as trusted configuration. The rule determines what it **wants** to access; your application determines whether it is **permitted** to access it.