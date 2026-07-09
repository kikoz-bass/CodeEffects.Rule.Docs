
# Cut, Copy, and Paste of Rule Elements

The [Rule Editor](/decision-automation/business-rule-editor) supports cut, copy, and paste operations for individual rule elements as well as for entire groups of conditions. This allows rule authors to reorganize complex rules quickly without manually re-creating repeated logic.

To select one or more rule elements:

- Use **Ctrl + Click** to select elements individually, or
- Use **Ctrl + Arrow Keys** to expand or reduce the selection in the direction of the arrow key pressed.

Once the desired elements are selected:

- Press **Ctrl + C** to **copy** the selection, or
- Press **Ctrl + X** to **cut** the selection.

Next, use either a mouse click or the arrow keys to move the selection to the rule element **after which** you want to insert the copied or cut elements.

- Press **Ctrl + V** to **paste**.

If a group of conditions was selected, the entire group structure (including nested elements and logical operators) will be pasted as a complete unit. This makes it easy to replicate complex logic patterns across different branches of the same rule.

## Example Workflows

- **Reorganizing rule flow**: Select a group of related conditions and move them to a different branch of the rule.
- **Duplicating a sub-expression**: Copy a condition or group and paste it under another part of the rule that requires similar logic.
- **Template-based authoring**: Copy a core group once and reuse it throughout the rule instead of re-creating it manually.

## Mobile Devices

Cut, Copy, and Paste operations are currently **not supported on touch devices** when using gesture interactions.