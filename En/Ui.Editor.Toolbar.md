# Managing Business Rules with Toolbar

The purpose of the Toolbar is to give rule authors an easy-to-use UI that provides basic rule management functionality such as creating, editing, and deleting [rules](/decision-automation/business-rule-types). It can also be used to select (load) an existing rule, and to name and describe new or existing rules.

The Toolbar <b>does not</b> actually store your rules anywhere, nor does it delete or update them in their physical location. Instead, it simply provides you, the developer, with the rule's data every time a rule author wants to work with that rule. Upon receiving that data, you must decide where to store the rule, where to load it from, and whether the current rule author has the right to perform any actions on that rule.

The Toolbar can be removed from the [Rule Editor](/decision-automation/business-rule-editor)'s UI by setting the [`Control.ShowToolBar`](/decision-automation/rule-editor-control) property to `false`. It is shown by default. You must implement your own controls, such as `Save` and `Load` buttons, and “connect” those controls with the server-side code if you hide the Toolbar but still need to manage rules.

If shown, the Toolbar always appears on top of the Rule Area. It consists of several UI elements:

- <b>Rules menu.</b> This menu provides options to create new rules and load existing ones, if there are any. The number of options to create new rules and the labels of those options depend on the `Mode` property of the [`Control`](/decision-automation/rule-editor-control) class. If [Mode](/decision-automation/business-rule-editor-modes) is set to `RuleType.Execution`, the Toolbar will include two options — `New execution type rule...` and `New evaluation type rule...`. Selecting one of those options will clear the Rule Area for a new rule of the selected type. If Mode is set to `RuleType.Evaluation` or `RuleType.Loop`, the Toolbar will include only one option named `New rule...`. Selecting that option will clear the Rule Area for a new rule of [evaluation](/decision-automation/business-rule-evaluation-type) type in `Evaluation` mode or [execution](/decision-automation/business-rule-execution-type) type in `Loop` mode. As you can see, it is important to understand [Rule Editor](/decision-automation/business-rule-editor)'s [modes](/decision-automation/business-rule-editor-modes) and how they operate. You can easily change the labels of all these menu options by using [Help XML](/decision-automation/business-rule-multilingual-support).

	![Toolbar 1](/resources/images/doc/toolbarrulesmenu.jpg)

	You can also include options in this menu to load existing rules for editing. It is entirely up to you, the developer, to decide which rules to load into the <em>Rules</em> menu, as long as they correlate with the current value of `Control.Mode`. You can load any type of rule when Mode is set to `RuleType.Execution`. However, the [Rule Editor](/decision-automation/business-rule-editor) <b>may not</b> work properly if you load any [execution](/decision-automation/business-rule-execution-type) type rule into the <em>Rules</em> menu while `Mode` is set to `RuleType.Evaluation` (its default value). Most of our demo projects demonstrate the use of the Toolbar and how to load rules into its <em>Rules</em> menu.
	
	[`Control.ToolBarRules`](/decision-automation/rule-editor-control) is the property that must be populated for existing rules to appear in the <em>Rules</em> menu on the client. It is of the `ICollection.MenuItem` type.

- <b>Name text box.</b> Rule authors can name rules by typing the rule name into the <em>Name</em> text box. The name is stored in [Rule XML](/decision-automation/business-rules-storage). By default, it is a required field unless the Toolbar is hidden. Set `Control.RuleNameIsRequired` to `false` to make this field optional. The default `placeholder` attribute of the box can easily be changed with [Help XML](/decision-automation/business-rule-multilingual-support).

	![Toolbar 2](/resources/images/doc/toolbarnamebox.jpg)

- <b>Description text box.</b> Rule authors can also give rules short descriptions by typing them into the <em>Description</em> box. This field is optional. The [Rule Editor](/decision-automation/business-rule-editor) displays rule descriptions in the <em>Help String</em> when rule authors move through the list of existing rules in the <em>Rules</em> menu with the mouse or keyboard. The [Rule Editor](/decision-automation/business-rule-editor) also shows descriptions of reusable rules when a rule author hovers the mouse over the rule in the Rule Area. The default `placeholder` attribute of the box can easily be changed with [Help XML](/decision-automation/business-rule-multilingual-support).

	![Toolbar 3](/resources/images/doc/toolbardescbox.jpg)

- <b>Save button.</b> The <em>Save</em> button allows rule authors to notify the server that the current rule needs to be saved. The [Rule Editor](/decision-automation/business-rule-editor) does not actually save the rule for you since it has no knowledge of the infrastructure or systems involved in your rule processing. Instead, it passes the rule data to the server and makes it available to your code as a compact, string-based [XML document](/decision-automation/business-rules-storage), together with other useful data. It is up to you how to handle that data.

	![Toolbar 4](/resources/images/doc/toolbarsavebutton.jpg)

- <b>Delete button.</b> The <em>Delete</em> button notifies your server code that the rule author wants to delete the currently loaded rule. As with every other part of the Toolbar’s functionality, the [Rule Editor](/decision-automation/business-rule-editor) does not (and cannot) actually delete your rules. Instead, it informs your code which rule the user wants to delete. This button is visible on the Toolbar only when a previously saved rule is loaded into the Rule Area.

	![Toolbar 5](/resources/images/doc/toolbardeletebutton.jpg)

Please refer to our [demo projects](https://github.com/orgs/codeeffects-software/repositories) for details on implementing particular features described in this article.
