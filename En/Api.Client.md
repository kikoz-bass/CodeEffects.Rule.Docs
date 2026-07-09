
# Code Effects Client-Side API

## Package

- ### codeeffects - [npm](https://www.npmjs.com/package/codeeffects)

	#### Files

	- <em><small>codeeffects.editor.js</small></em>
	- <em><small>codeeffects.common.css</small></em>
	- <em><small>codeeffects.light.css</small></em>

	#### Remarks

	The **Code Effects Rule Editor** provides a powerful, modular client-side interface that integrates seamlessly with any modern web application. It enables users to visually create, edit, and manage business rules directly in the browser, while your server-side code handles rule evaluation through the [Code Effects Rule Engine](/decision-automation/business-rule-evaluation).

	The editor is implemented as a **self-contained JavaScript library** that exposes a compact set of public functions via two global variables — `$rule` and `$ce` — while encapsulating all other logic within its internal scope. This makes it completely safe to add the editor to any environment without risking conflicts with existing scripts, frameworks, or global objects.

	Starting with script's **version 6.1.5**, the editor has been **modularized** to support both modern frameworks and traditional web applications:

	- It can be **imported as an ES module** in client frameworks such as **React**, **Angular**, or **Vue**.  
	- It can also be **referenced as a plain script** in any HTML markup using the `<script type="module">` tag.  
	- The editor’s package is distributed through [npm](https://www.npmjs.com/package/codeeffects) under the package name `codeeffects`, making it easy to integrate into any build pipeline.

	The editor includes two CSS files:

	- `codeeffects.common.css` — the core stylesheet shared across all themes  
	- `codeeffects.light.css` — the new default Light theme (a Dark theme will be introduced in a future release)

	The Rule Editor is fully self-contained and has **no dependencies on third-party client or server libraries**. It supports all current versions of all major browsers.

	Refer to our [demo project repos](https://github.com/orgs/codeeffects-software/repositories) for examples of referencing, configuring, and using the editor across different web platforms.

## Global Shortcuts

<div class="members">

- ### $rule<br/>
	<small>Type: (`Object`) - The `CodeEffects.Rule.Models` namespace</small>

	This global shortcut is declared to make the footprint of `CodeEffects` namespace smaller.

- ### $ce(..)<br/>
	<small>Returns: (`Object`) - Instance of the Rule Editor</small>

	This is a global shortcut to the `$rule.Context.getControl(..)` function that can be used to get an instance of a Rule Editor by the client id of its hosting `div` element.

	#### Parameters

	<div class="params">

	- *id* (`String`) - Client id of the hosting `div` element of Rule Editor. Required.

	</div>

	#### Example

	```javascript
	var codeeffects = $ce("divRuleEditor");
	```

## Global Functions

- ### $rule.init(..)<br/>
	<small>Returns: (`Object`) - Newly created instance of Rule Editor</small>

	This method initializes and returns an instance of the [rule editor](/decision-automation/business-rule-editor) on the client. For details about editor initialization, please refer to the dedicated documentation article [available here](/decision-automation/business-rule-editor-client-initialization).

	#### Parameters

	<div class="params">

	- *settings* (`String`) - JSON string received from the `GetInitialSettings()` method of the [`CodeEffects.Rule.Editor.Control`](/decision-automation/rule-editor-control) .NET class. This parameter is required.

	</div>

## Public Event Handlers

- ### onBlur(..)<br/>
	<small>Returns: (`undefined`)</small>

	Gets invoked when the Rule Area loses focus (i.e., when the user first clicks or taps on any DOM element in the current document that is not a child node of the main `div` container of the Rule Area while it is focused). Subscribe to the `onblur` event of the Rule Area by calling this handler and passing a callback function as its only parameter. Unsubscribe by calling this handler without any parameters or by passing `null` as its only parameter.

	#### Parameters

	<div class="params">

	- *callback* (`Function`) - A callback function that the editor calls when the Rule Area loses focus. The editor passes no parameters to the callback.

	</div>

	#### Example

	```javascript
	var editor = $ce("divEditor");
	editor.onBlur(function() { alert("The onBlur event is handled"); });
	```
- ### onFocus(..)<br/>
	<small>Returns: (`undefined`)</small>

	Gets invoked when the Rule Area receives focus (i.e., when the user first clicks or taps on any child node of the main `div` container of the Rule Area while it is not focused). Subscribe to the `onfocus` event of the Rule Area by calling this handler and passing a callback function as its only parameter. Unsubscribe by calling this handler without any parameters or by passing `null` as its only parameter.

	#### Parameters

	<div class="params">

	- *callback* (`Function`) - A callback function invoked when the Rule Area loses focus. The editor passes a reference to the `event.target` element that was clicked or tapped within the Rule Area.

	</div>

	#### Example

	```javascript
	var editor = $ce("divEditor");
	editor.onFocus( function (el) { alert("The onFocus event is handled. Target element: " + el.id); } );
	```

## Public Instance Functions

- ### clear()<br/>
	<small>Returns: (`Object`) - Rule Editor instance</small>

	Removes all elements of the current rule from the Rule Area.

	#### Parameters

	<div class="params">

	- None.

	</div>

- ### deleted(..)<br/>
	<small>Returns: (`Object`) - Rule Editor instance</small>

	Calling this function after deleting a rule is necessary to clear editor's cache and remove the deleted rule from the Rules menu (if the [Toolbar](/decision-automation/business-rule-toolbar) is displayed) and context menus.

	#### Parameters

	<div class="params">

	- *id* (`String`) - ID of the rule that is being deleted. This parameter is required.

	</div>

- ### disable()<br/>
	<small>Returns: (`Object`) - Rule Editor instance</small>

	Prevents users from adding new rules or modifying the rule currently loaded in the Rule Area.

	#### Parameters

	<div class="params">

	- None.

	</div>

	```javascript
	var editor = $ce("divEditor");
	editor.disable();
	```

- ### dispose()<br/>
	<small>Returns: (`Object`) - Rule Editor instance</small>

	Disposes all resources used by Rule Editor and removes it from the current context.

	#### Parameters

	<div class="params">

	- None.

	</div>

- ### enable()<br/>
	<small>Returns: (`Object`) - Rule Editor instance</small>

	Restores ability to add new rules or modify the rule currently loaded in the Rule Area.

	#### Parameters

	<div class="params">

	- None.

	</div>

- ### extract()<br/>
	<small>Returns: (`String`) - JSON string</small>

	Returns a JSON string containing the current rule and a value indicating whether it is valid. If the Rule Area is empty, returns an empty JSON.

	#### Parameters

	<div class="params">

	- None.

	</div>

- ### isAltered()<br/>
	<small>Returns: (`Boolean`)</small>

	Returns `true` if the user has modified an existing rule after it was loaded into the Rule Area or a new rule after its creation was initiated. Otherwise, returns `false`.

	#### Parameters

	<div class="params">

	- None.

	</div>

- ### isEmpty(..)<br/>
	<small>Returns: (`Boolean`)</small>

	Returns `true` if the Rule Area currently contains no rule elements. Otherwise, returns `false`.

	#### Parameters

	<div class="params">

	- *includeFormattingElements* (`Boolean`) - Indicates whether formatting elements of the rule, such as tabs and new lines, should be counted. Optional. Default value is `true`.

	</div>

- ### isEvaluationType()<br/>
	<small>Returns: (`Boolean`)</small>

	Returns `true` if the loaded rule is of the [evaluation type](/decision-automation/business-rule-evaluation-type) or if the rule author selected the New evaluation-type rule option from the Rules menu of the [Toolbar](/decision-automation/business-rule-toolbar). This function does not check which rule elements the author has added to the Rule Area so far.

	#### Parameters

	<div class="params">

	- None.

	</div>

- ### getRuleId()<br/>
	<small>Returns: (`String`) - String value of the Rule ID (.NET `System.Guid`)</small>

	Returns the string ID of the current rule assigned by the Rule Editor. The editor generates new rule IDs by calling .NET’s `System.Guid.NewGuid().ToString()`. Returns `null` if no rules have been loaded into the editor, if the current rule has not yet been assigned an ID, or if the Rule Area is empty.

	#### Parameters

	<div class="params">

	- None.

	</div>

- ### loadInvalids(..)<br/>
	<small>Returns: (`Object`) – Rule Editor instance</small>

	Call this function to load invalid rule elements into the Rule Editor.

	#### Parameters

	<div class="params">

	- *data* (`String`) - JSON string received from the `GetClientInvalidData()` method of the [`CodeEffects.Rule.Editor.Control`](/decision-automation/rule-editor-control) .NET class. Fork the related [demo project](/decision-automation/business-rules-engine-demo-project) to see how to load invalid rule data into the Rule Editor. This parameter is required.

	</div>

- ### loadRule(..)<br/>
	<small>Returns: (`Object`) – Rule Editor instance</small>

	Call this function to load a rule into the Rule Editor.

	#### Parameters

	<div class="params">

	- *data* (`String`) - JSON string received from the `GetClientRuleData()` method of the [`CodeEffects.Rule.Editor.Control`](/decision-automation/rule-editor-control) .NET class. This parameter is required.de

	</div>

- ### loadSettings(..)<br/>
	<small>Returns: (`Object`) – Rule Editor instance</small>

	Call this function to load the Rule Editor client settings. You should also call it whenever the rule author changes any of the editor’s settings, such as the visibility of the [Toolbar](/decision-automation/business-rule-toolbar), [Help String](/decision-automation/business-rule-editor), and similar options.

	#### Parameters

	<div class="params">

	- *data* (`String`) - JSON string received from the `GetClientSettings()` method of the [`CodeEffects.Rule.Editor.Control`](/decision-automation/rule-editor-control) .NET class. This parameter is required.

	</div>

- ### saved(..)<br/>
	<small>Returns: (`Object`) – Rule Editor instance</small>

	Call this function after saving a new rule or updating an existing one to refresh the Rules menu of the [Toolbar](/decision-automation/business-rule-toolbar) and context menus of the editor with the new or updated values.

	#### Parameters

	<div class="params">

	- *id* (`String`) - ID of the rule that has been saved or updated. This parameter is required for new rules and optional for updates.

	</div>

- ### setClientActions(..)<br/>
	<small>Returns: (`Object`) – Rule Editor instance</small>

	Call this function during page initialization to specify which functions the Rule Editor should call when the rule author wants to load, delete, or save a rule. Do not call it if the [Toolbar](/decision-automation/business-rule-toolbar) is disabled.

	#### Parameters

	<div class="params">

	- *loadCallback* (`Function`) - Callback function that handles loading a rule. This function must declare a single parameter of type `string`. The Rule Editor calls this function and passes it the rule ID when the rule author selects that rule from the Toolbar’s Rules menu. This parameter is required.
	- *deleteCallback* (`Function`) - Callback function that handles deleting a rule. This function must declare a single parameter of type `string`. The Rule Editor calls this function and passes it the rule ID when the rule author clicks the Delete button on the Toolbar. This parameter is required.
	- *saveCallback* (`Function`) - Callback function that handles saving a rule. This function must declare a single parameter of type `object`. The Rule Editor calls this function and passes it an object containing the rule data when the rule author clicks the Save button on the Toolbar. This callback works in tandem with the `LoadClientData()` method of the [`CodeEffects.Rule.Editor.Control`](/decision-automation/rule-editor-control) .NET class. This parameter is required.

	</div>