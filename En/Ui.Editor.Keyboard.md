
# Keyboard and User‐Gesture Support

## Keyboard Support

The most efficient way to add rule elements, select menu items, or navigate an existing rule is via keyboard input. Below is the list of supported keys and their descriptions:

- **Space bar**: When a rule element is selected, the context menu opens, showing the next possible options related to that element. This mirrors the downward swipe in the Rule Area on a mobile device, or a mouse click in the Rule Area where no element is present.

	> *THE MOST IMPORTANT TIP*: Whenever you are not sure where you are in the rule or what to do next, click the bottom-right corner of the Rule Area (away from any rule element). This selects the last element in the rule. Then press the Space bar. This brings up the menu containing all valid next steps for the selected element, helping you continue rule authoring without losing your place.

- **Left, Right, Up, Down Arrow Keys**:

	- If a context menu is open, the Up and Down arrows move the selection among the menu items.
	- If no menu is open, the arrow keys move the selection between rule elements (left, right, above, or below).

- **Enter**:

	- If a context menu is open, selects the highlighted menu option.
	- If a value is being edited, finalizes the input.
	- Otherwise, inserts a new rule line.

- **Home**: Moves the selection to the left-most rule element in the current line.
- **End**: Moves the selection to the right-most rule element in the current line.
- **Page Up**: Moves the selection to the `If`/`Check If` flow element, which is always the first rule element.
- **Page Down**: Moves the selection to the last rule element currently visible in the editor.
- **Delete**: Deletes the currently selected rule element and moves the selection to the next element to the right. This mirrors a right swipe in the Rule Area on mobile.
- **Backspace**: Deletes the previous sibling of the selected rule element and keeps the selection on the current element. This mirrors a left swipe in the Rule Area on mobile.
- **Esc (Escape)**: Closes the context menu, date picker, or time picker if one is visible.
- **Tab**: If the first empty element in a line is selected, creates a new empty “tab” (indentation) and moves the selection into it.

## User Gesture Support on Mobile Devices

The [Rule Editor](/decision-automation/business-rule-editor) also supports tap and swipe gestures for rule authoring on mobile devices. All gestures must originate from the Rule Area. Swiping in the Rule Area also disables standard scrolling to ensure accuracy during rule construction.

- **Short tap on a rule element**: Selects that element and opens a context menu showing source properties and methods of the same type (or return type). This mirrors a mouse click on a rule element.
- **Short tap on the Rule Area (empty space)**: Selects the last element in the tapped line and opens a list of properties or methods for replacement. This mirrors a mouse click on an empty part of the Rule Area.
- **Long tap on the currently selected rule element**: Inserts a new rule line immediately after the selected element. This mirrors pressing Enter on a keyboard.
- **Long tap on the Rule Area (empty space)**: Opens the context menu showing next possible options for the currently selected element. This mirrors a downward swipe or clicking an empty part of the Rule Area.
- **Swipe left**: Deletes the previous sibling of the currently selected rule element and keeps the selection on the current element. This mirrors Backspace.
- **Swipe right**: Deletes the currently selected element and moves the selection to the next element. This mirrors Delete.
- **Swipe down**: Opens the context menu for the currently selected element. This mirrors the Space bar key or a long tap.
- **Swipe up**: Not currently in use.