# Code Effects Documentation

Official documentation for [Code Effects](https://codeeffects.com) — an embeddable business rules engine for .NET featuring a visual rule editor, AI-assisted rule authoring and execution, and no-code capabilities for business users.

## About Code Effects Software

Code Effects Software has been building decision automation software since 2009. Our embeddable business rules engine lets non-developers manage complex business logic without waiting on engineering for every rule change. With the release of [Version 6](https://codeeffects.com/decision-automation/new-features-version-6-business-rules-engine), Code Effects decision platform adds AI/LLM-assisted rule authoring and execution, making it faster than ever to describe business rules using web-based UI and have them translated into working logic.

## What's in This Repo

Files in this repo are structured to follow the namespaces of the C# public classes that make up the core functionality of the engine and the editor.

- **Class reference articles** are named after their namespace, minus the leading `CodeEffects.Rule.` prefix. For example, the `CodeEffects.Rule.Common.Attributes.FieldAttribute` class is documented in `Common.Attributes.Field.md`.
- **General articles** that cover concepts or the use of main platform features rather than a specific class follow the pattern `Ui.Feature.Subject.md`. For example, the article documenting the Adaptive Source model is named `Ui.SourceObject.Adaptive.md`.

## Structure

Documentation is organized by language:

```
docs/
└── en/
    ├── Common.Attributes.Field.md
    ├── Ui.SourceObject.Adaptive.md
    └── ...
```

Currently available in **English**. Additional languages will be added under their own folder (e.g. `docs/de/`) as translations become available.

## Live Documentation

The live, browsable version of this documentation is available at [codeeffects.com/decision-automation](https://codeeffects.com/decision-automation).

## Feedback

Found an error or something unclear? Open an issue in this repo - corrections and suggestions are welcome.

## Resources

- **Live demo:** https://codeeffects.com/business-rules-engine-demo
- **Implementation:** https://codeeffects.com/decision-automation/business-rule-implementation
- **How to obtain Code Effects:** https://codeeffects.com/decision-automation/how-to-obtain-business-rules-engine
- **Code Effects Editions:** https://codeeffects.com/content/business-rules-engine-editions
- **Support:** https://codeeffects.com/support

© 2009 - 2026 Code Effects Software, LLC
