
# Code Effects Rule Types

Code Effects supports two complementary types of rules:

- [Evaluation Type rules](/decision-automation/business-rule-evaluation-type) — return a boolean evaluation result; may invoke [in-rule methods](https://localhost:44300/decision-automation/business-rule-source-object) during rule evaluation.
- [Execution Type rules](/decision-automation/business-rule-execution-type) — return a boolean evaluation result; may invoke [in-rule methods](https://localhost:44300/decision-automation/business-rule-source-object) during rule evaluation and may set/update/delete field values and invoke rule actions (void methods) after the evaluation is complete.

Both rule types are [authored](/decision-automation/business-rule-management) in the same [Rule Editor](/decision-automation/business-rule-editor) UI and stored in the same [Rule XML](/decision-automation/business-rules-storage) format. The selected Rule Type determines which menus are enabled during authoring and which engine behaviors are active at runtime.

## When to Use Which Type

Use evaluation rules when you need a decision that can be expressed as a predicate (eligibility, segmentation, routing, pricing tiers). These rules also support [Data Filtering](/decision-automation/business-rule-data-filtering) (rule-to-query conversion) so you can apply the same rule to collections with the `Evaluator.Filter()` method.

Use execution rules when, in addition to a decision, you need to perform side effects such as sending notifications, generating files, invoking HTTP calls, or updating state via actions.

## Authoring Differences

<table style="margin-top:40px;margin-bottom:40px;width:95%;">
	<tr>
		<th>Capability</th>
		<th width="15%;" style="text-align:center;">Evaluation Type</th>
		<th width="15%;" style="text-align:center;">Execution Type</th>
	</tr>
	<tr>
		<td>Returns boolean</td>
		<td style="color:green;font-weight:700;text-align:center;">True</td>
		<td style="color:green;font-weight:700;text-align:center;">True</td>
	</tr>
	<tr>
		<td>Actions and field setters</td>
		<td style="color:red;font-weight:700;text-align:center;">False</td>
		<td style="color:green;font-weight:700;text-align:center;">True</td>
	</tr>
	<tr>
		<td>In‑rule methods</td>
		<td style="color:green;font-weight:700;text-align:center;">True</td>
		<td style="color:green;font-weight:700;text-align:center;">True</td>
	</tr>
	<tr>
		<td>Filter mode (Data Filtering)</td>
		<td style="color:green;font-weight:700;text-align:center;">True</td>
		<td style="color:red;font-weight:700;text-align:center;">False</td>
	</tr>
	<tr>
		<td>Loop evaluation mode</td>
		<td style="color:red;font-weight:700;text-align:center;">False</td>
		<td style="color:green;font-weight:700;text-align:center;">True</td>
	</tr>
	<tr>
		<td>Ruleset evaluation mode</td>
		<td style="color:red;font-weight:700;text-align:center;">False</td>
		<td style="color:green;font-weight:700;text-align:center;">True</td>
	</tr>
	<tr>
		<td>Reusable rules</td>
		<td style="color:green;font-weight:700;text-align:center;">True</td>
		<td style="color:red;font-weight:700;text-align:center;">False</td>
	</tr>
	<tr>
		<td>Rule templates</td>
		<td style="color:green;font-weight:700;text-align:center;">True</td>
		<td style="color:green;font-weight:700;text-align:center;">True</td>
	</tr>
	<tr>
		<td>Multilingual support and Help XML</td>
		<td style="color:green;font-weight:700;text-align:center;">True</td>
		<td style="color:green;font-weight:700;text-align:center;">True</td>
	</tr>
</table>