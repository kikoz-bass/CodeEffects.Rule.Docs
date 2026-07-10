
# Initialization of the Rule Editor.

The [Rule Editor](/decision-automation/business-rule-editor) can be initialized on a web page in one of two ways:

- Server-centric initialization
- Client-centric initialization

The first approach is intended for applications that frequently change editor settings, such as [source objects](/decision-automation/business-rule-source-object), [UI options](/decision-automation/business-rule-editor-settings), [CSS](/decision-automation/business-rule-settings-and-appearance), [current UI culture](/decision-automation/business-rule-multilingual-support), or other server-generated configuration. Since those settings are already produced on the server, it often makes sense to perform the entire editor initialization there. This initialization method has been available since the first release of the Code Effects platform in 2009 and remains supported for backward compatibility.

The second approach is handled primarily by the client-side editor script. It requires only two pieces of data from the server, while all remaining settings can be configured directly by your client-side code.

Both approaches provide identical functionality. Ultimately, they both initialize the editor by calling the same `$rule.init()` method, differing only in the data passed to it.

## Server-Centric Initialization

First, initialize the server-side [`Control`](/decision-automation/rule-editor-control) instance and obtain the two pieces of data required by the client. The following example comes from one of our [demo projects](https://github.com/orgs/codeeffects-software/repositories) and uses the [Reflection source model](/decision-automation/business-rule-source-object-class):

```csharp
using CodeEffects.Rule.Common.Models;
using CodeEffects.Rule.Editor;

public class Settings
{
	public string? GlobalData { get; set; }
	public string? EditorData { get; set; }
	public EvaluationMode Mode { get; set; } = EvaluationMode.Execution;
	
	// The editor supports many more settings.
	// This example uses only the most common ones.
	
	public bool Toolbar { get; set; } = true;
	public bool HelpString { get; set; } = true;
	public bool Dots { get; set; } = false;
}

public static class Api
{
	public static void MapEndpoints(this WebApplication app)
	{
		// Returns client settings for the editor
		app.MapGet("/api/settings", () =>
		{
			var settings = new Settings();
			var editor = GetControl(settings);
			
			// Set UI settings for the editor
			settings.GlobalData = editor.GetGlobalData();
			
			// Set source-related settings
			settings.EditorData = editor.GetEditorData();
			
			return Results.Ok(settings);
		});
	}
	
	private static Control GetControl(Settings settings)
	{
		// Pass the ID of the div element
		// that hosts the editor
		var editor = new Control("divEditor");
		
		// Use Reflection source model
		editor.SourceType = typeof(Patient);
		editor.EvaluationMode = settings.Mode;
		
		editor.ShowHelpString = settings.HelpString;
		editor.ShowToolBar = settings.Toolbar;
		editor.ShowLineDots = settings.Dots;
		
		return editor;
	}
}
```

The client-side implementation is intentionally simple:

```javascript
// Declare editor as a global variable
var editor = null;

// Load settings
const res = await fetch(
	'/api/settings',
	{headers: {'Accept': 'application/json'}});
	
const settings = await res.json();

// Initialize the editor
editor = $rule.init(settings.globalData);

// Attach rule management callbacks.
// See the demo projects for their implementation.
editor.setCallbacks(load, remove, save);

// Load source-related settings
editor.loadSettings(settings.editorData);
```

## Client-Centric Initialization

The client-centric approach gives your client-side code complete control over editor initialization. The following example uses the [Adaptive Source](/decision-automation/business-rule-adaptive-source) model from one of our [demo project repos](https://github.com/orgs/codeeffects-software/repositories).

In this case, the `Settings` class is smaller because UI settings are configured on the client instead of the server. Likewise, the `GetControl()` method only needs to assign the menu provider that implements [`IMenuProvider`](/decision-automation/rule-editor-models-imenuprovider) interface and supplies dynamic menus while the user creates or updates rules.

The endpoint itself remains unchanged:

```csharp
public class Settings
{
	public string? GlobalData { get; set; }
	public string? EditorData { get; set; }

	public EvaluationMode Mode { get; set; } =
		EvaluationMode.Execution;
}

private static Control GetControl(Settings settings)
{
	var editor = new Control();
	editor.MenuProvider = new MenuProvider(typeof(Patient));
	editor.EvaluationMode = settings.Mode;
	
	return editor;
}
```

The client-side initialization is more flexible:

```javascript
var editor = null;

const res = await fetch(
	'/api/settings',
	{headers: {'Accept': 'application/json'}});

const settings = await res.json();

// Initialize the editor
editor = $rule.init(
	{
		id: "divEditor",
		refData: "Anything",
		showHelpString: true,
		showToolBar: true,
		globalData: settings.globalData,
		editorData: settings.editorData,
		saveRuleCallback: save,
		loadRuleCallback: load,
		deleteRuleCallback: remove,
		notifyCallback: info,
		
		// Passing the adaptiveSource object
		// instructs the editor to use the
		// Adaptive Source model.
		adaptiveSource:
			{
				menuCallback: menu,
				behavior: $rule.behavior.replace
			}
	});
```

In this example, the `$rule.init()` method receives a single settings object that controls the editor initialization.

## Properties

<div class="members">

- ### id<br/>
	<small>Type: `String`</small>

	The ID of the `<div>` element that hosts the editor. Required unless it has already been specified on the server.

- ### refData<br/>
	<small>Type: `String`</small>

	Any custom data that you want to pass between the client and server whenever the editor requests a new menu. This can be useful for passing user-specific or source-specific information. Optional. See the [`Context`](/decision-automation/rule-editor-client-context) class documentation for more information.

- ### globalData<br/>
	<small>Type: `String`</small>

	A proprietary JSON string containing global data required by the editor. Required.

- ### editorData<br/>
	<small>Type: `String`</small>

	A proprietary JSON string containing source-related data required by the editor. When using [Adaptive Source](/decision-automation/business-rule-adaptive-source), this value is still required because it contains UI-related information even though the source metadata is loaded dynamically. Required.

- ### saveRuleCallback<br/>
	<small>Type: `Function`</small>

	Called when the user requests to save the rule currently displayed in the Rule Area. Required when the Toolbar is enabled. Optional if your own application provides rule-saving functionality. The callback must not declare any parameters. Use the `editor.extract()` method to retrieve the current rule. The callback may be declared as either synchronous or asynchronous. The editor always invokes it synchronously.

	#### Example

	```javascript
	async function save()
	{
		const res = await fetch(
			'/api/save',
			{
				method: 'POST',
				headers: {'Content-Type': 'application/json'},
				body: JSON.stringify(editor.extract())
			});
	};
	```

- ### loadRuleCallback<br/>
	<small>Type: `Function`</small>

	Loads a rule by its ID. Required when the Toolbar Rules menu is enabled. Optional otherwise. The callback may be declared as either synchronous or asynchronous. The editor always invokes it synchronously.

	#### Parameters

	<div class="params">

	- *ruleID* (`String`) - `GUID` string value of the rule ID.

	</div>

- ### deleteRuleCallback<br/>
	<small>Type: `Function`</small>

	Deletes the rule currently displayed in the Rule Area. Required when the Toolbar is enabled. Optional otherwise. The callback may be declared as either synchronous or asynchronous. The editor always invokes it synchronously.

	#### Parameters

	<div class="params">

	- *ruleID* (`String`) - `GUID` string value of the rule ID.

	</div>

- ### notifyCallback<br/>
	<small>Type: `Function`</small>

	Called whenever the editor needs to display a notification. This callback allows you to integrate the editor with your application's own notification system. If not set, the editor calls the default `alert` function. The callback may be declared as either synchronous or asynchronous. The editor always invokes it synchronously.

	#### Parameters

	<div class="params">

	- *message* (`String`) - Notification text.
	- *notificationType* (`String`) - One of the following values used by the editor internally:

		```javascript
		var notificationTypes = Object.freeze(
		{
			confirmation: "confirmation",
			warning: "warning",
			error: "error"
		});
		```
	</div>

- ### adaptiveSource<br/>
	<small>Type: `Object`</small>

	Passing this object to `$rule.init()` instructs the editor to use the [Adaptive Source](/decision-automation/business-rule-adaptive-source) model. If omitted, the editor automatically selects one of the remaining source models based on the supplied `globalData`.

	<div class="params">

	#### Properties

	- **menuCallback** (`Function`) -  Called whenever the editor requires the next dynamic menu. The callback may be declared as either synchronous or asynchronous. The editor always invokes it synchronously.

		```javascript
		async function menu(args)
		{
				const res = await fetch(
				'/api/menu',
				{
					method: 'POST',
					headers: {'Content-Type': 'application/json'},
					body: JSON.stringify({type: args.type, context: args.context})
				});

				const data = await res.json();
				editor.loadAdaptiveData(data);
		};
		```

		#### menuCallback Parameters
	
		- *args* (`Object`) - Contains the current rule context and the type of menu requested by the editor.
		 
			- The `context` object mirrors the server-side [`CodeEffects.Rule.Editor.Client.Context`](/decision-automation/rule-editor-client-context) class:

			```javascript
			// The editor sets the value of the refData property to the value
			//	the value supplied to $rule.init()
			var context = { refData: null, conditions: [], executions: [] };
			```
			- The `type` property contains one of the members of the following public object, which mirrors the server-side [`RuleSectionType`](/decision-automation/rule-editor-models-rulesectiontype) enum:

			```javascript
			$rule.Common.RuleSectionType = Object.freeze({ Conditions: 0, Executions: 1 });
			```

	- **behavior** (`Object`) - Specifies how the editor processes newly received menu data. The editor can either replace existing menus with matching `CacheID` values or merge them. The default value is `$rule.defaults.behavior.replace`. The object is declared as:

		```javascript
		$rule.behavior = Object.freeze({ merge: 0, replace: 1 });
		```

	- **max** (`Integer`) - Specifies the maximum time, in milliseconds, that the *Loading...* menu remains visible to prevent UI flickering. Default value is `$rule.defaults.max`.
	- **min** (`Integer`) - Specifies the minimum time, in milliseconds, before the editor displays the *Loading...* menu while waiting for the next menu. Default value is `$rule.defaults.min`.

		#### Remarks

		The editor includes a built-in mechanism that automatically waits for slow menu responses and displays the *Loading...* menu when appropriate. The text of this menu can be customized using the [HelpXML](/decision-automation/business-rule-multilingual-support) feature.

		The `$rule` interface exposes default [Adaptive Source](/decision-automation/business-rule-adaptive-source) settings:

		```javascript
		$rule.defaults = { min: 400, max: 800, behavior: $rule.behavior.replace };
		```

		This object is mutable. You can modify these defaults globally or override them for individual editor instances by passing custom values to `$rule.init()`. This is particularly useful when different source objects or [menu providers](/decision-automation/rule-editor-models-imenuprovider) have different response characteristics.

	</div>
</div>
