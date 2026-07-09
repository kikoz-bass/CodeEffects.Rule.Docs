# Context Resetters in Adaptive Source

Context Resetters is an advanced Adaptive Source feature introduced in version 6 of the Code Effects platform. It allows the rule editor to intelligently recognize when a change to one condition invalidates the logical relevance of subsequent rule elements. In complex rules, fields can depend on each other. Changing a value, operator, or field in one condition could potentially render some or all remaining conditions and actions irrelevant.

This feature introduces semantic dependency awareness directly into natural-language rule authoring. Without it, rules can gradually accumulate logically disconnected conditions that remain syntactically valid but operationally meaningless.

Traditional business rules systems treat every condition as independent. Context Resetters recognize that real-world decision logic is rarely independent. In many enterprise workflows, changing one decision fundamentally changes the meaning of everything that follows.

Context Resetters represent more than a UI feature. They introduce a new category of rule-authoring behavior where the editor understands that the meaning of later conditions may depend on the decisions made earlier in the rule. Combined with Adaptive Source, this creates a fundamentally different architecture for natural-language rule interfaces.

For example, imagine the editor being used as a search or reporting tool on a web page, and a user creates the following rule:

```
Display records where
	Product is Laptop and
	OS is Windows 11
```

If the user later changes the product from `Laptop` to `Monitor`, the entire rule becomes obsolete because the `OS` condition no longer makes sense - monitors do not use operating systems.

Traditional rule editors leave the invalid logic intact and force the user to recognize the issue and manually repair the rule.

This creates several problems:

- Invalid business logic
- Broken reporting conditions
- Inconsistent searches and look-ups
- Confusing user experience
- Hard to detect logical drift inside large rules
- Increased rule maintenance costs
- Hidden semantic errors

The larger and more dynamic the rule becomes, the worse the problem gets.

Context Resetters solve this issue directly at the authoring layer. You can configure specific fields or methods to act as semantic reset points by setting the ResetsContext property to True;

```csharp
using CodeEffects.Rule.Editor.Client;

var field = new Field();
field.Value = "User.Name";
field.DisplayName = "Name";
field.DataType = Common.Models.DataType.String;
field.Nullable = true;
field.ResetsContext = true;

var method = new Function();
method.Value = "User.GetProbability";
method.DisplayName = "AI Score";
method.ResetsContext = true;
```

When the user changes:

- the field or method marked as Context Resetter
- its operator
- or its condition value

the editor automatically removes all rule elements that follow the changed condition and displays the Undo icon at the end of the rule. The user can either click that icon or press Ctrl-Z to restore the original rule, or continue with the changes. This creates a highly intuitive authoring experience where the editor behaves more like an intelligent assistant than a static form editor.

> The editor visually distinguishes Context Resetters using a dedicated UI style.

> You can easily disable this feature for any field or method in your source object without affecting existing rules or making them invalid.

Context Resetters become especially powerful in AI-assisted rule authoring systems. As LLMs and AI-driven workflows begin influencing decision composition, semantic consistency becomes critically important.

All this makes this feature particularly valuable in:

- BYOAI enterprise systems
- Prompt-driven workflows
- Adaptive UI generation
- AI-assisted reporting systems
- Intelligent search builders
- Dynamic decision automation

> Code Effects is the first natural-language rules platform to provide native infrastructure for contextual rule management.