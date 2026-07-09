# Editor Implementation

Code Effects business rules editor is a **FREE** web-based component and must be implemented in a web application. All current .NET web platforms as well as client frameworks such as React, Angular, and Vue are supported.

- ### Step 1

	Reference the latest version of the `CodeEffects.Rule.Editor` assembly from [NuGet](https://www.nuget.org/packages/CodeEffects.Rule.Editor) in your .NET web project.

- ### Step 2

	Reference the rule editor in your client-side. The Editor’s client functionality is available as [npm package](https://www.npmjs.com/package/codeeffects):

	- `codeeffects.editor.js` – the main JavaScript file that defines all client-side editor functionality.
	- `codeeffects.common.css` – the core CSS file of the editor.
	- `codeeffects.light.css` – the Light (default) theme.

	The main script has been modularized in order to support all existing installations, both as a module or as a plain script, without the need to develop and maintain multiple npm packages.

	- Either import the main script as a module (React, Angular, Vue, etc):

		```javascript
		// If using the npm bundle
		import { $rule, $ce } from "codeeffects";

		// If referencing the main script as a file
		import { $rule, $ce } from "../your-scripts/codeeffects.editor.js";
		```
	- ... or use the `<script>` tag to reference the main script in your markup (notice the use of the `module` value as the script's type):

		```html
		<script type="module" src="/your-scripts/codeeffects.editor.js"></script>
		```

- ### Step 3

	The editor requires some .NET code on the server to generate settings and manage rules. You can use any type of server API to transfer rule data between the server and the client. Refer to our [demo projects](https://github.com/orgs/codeeffects-software/repositories) for complete examples of rule-management functionality.

	```csharp
	using CodeEffects.Rule.Common.Attributes;
	using CodeEffects.Rule.Editor;
	using CodeEffects.Rule.Editor.Models;

	namespace CodeEffects.Demo.Asp
	{
		public static class Api
		{
			public static void MapEndpoints(this WebApplication app)
			{
				// Returns client settings for the editor
				app.MapGet("/api/settings", () =>
				{
					var settings = new Settings();

					var editor = new Control();
					editor.EvaluationMode = EvaluationMode.Execution;
					// Set the type of the source object
					editor.SourceType = typeof(YourClass);
					// Get UI settings
					settings.GlobalData = editor.GetGlobalData();
					settings.EditorData = editor.GetEditorData();
			
					return Results.Ok(settings);
				});
			}
		}
		public class Settings
		{
			public string? GlobalData { get; set; }
			public string? EditorData { get; set; }
		}
	}
	```
	In your HTML:

	```javascript
	// On page load
	const res = await fetch(
		'/api/settings',
		{ headers: {'Accept': 'application/json'} });
	
	const settings = await res.json();

	// This renders the editor on your page
	// with default settings inside your
	// "divEditor" div container
	var editor = $rule.init(
	{
		id: "divEditor",
		globalData: settings.globalData,
		editorData: settings.editorData,
		saveRuleCallback: saveFunction,
		loadRuleCallback: loadFunction,
		deleteRuleCallback: deleteFunction,
		notifyCallback: alertFunction
	});
	```
## Remarks

The example above demonstrates the simplest way to configure and render the [Rule Editor](/decision-automation/business-rule-editor) on your page using the [Reflection source model](/decision-automation/business-rule-source-object-class). The Rule Editor offers many additional features and configuration options, so be sure to refer to our online documentation for detailed guidance. If you need assistance with your implementation, please [contact us](/support).

## Resources

- [How to Obtain Code Effects](/decision-automation/how-to-obtain-business-rules-engine)
- [Code Effects Basics](/decision-automation)
- [Live Demo](/business-rules-engine-demo)
- [Demo Project Repos](https://github.com/orgs/codeeffects-software/repositories)