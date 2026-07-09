
# CSS Themes

Code Effects [Rule Editor](/decision-automation/business-rule-editor) comes with built-in, customizable CSS themes. They are available through the Code Effects [npm package](https://www.npmjs.com/package/codeeffects), which includes several CSS files ready to be added to your web application:

- `codeeffects.common.css` - Declares the core styles of the editor.
- `codeeffects.light.css` - Declares the Light theme.
- `codeeffects.dark.css` - Declares the Dark theme (available in Dec 2025).

Each theme is implemented in a separate CSS file and uses the same set of class names. All class names are prefixed with `ce`. Code Effects themes are intentionally simplified and provide only the styles essential for normal use of the Rule Editor. You can use a theme as provided, modify it to meet your specific requirements, or choose not to use any default Code Effects theme and instead declare and implement all `ce` classes elsewhere in your code.

To implement Code Effects styles, reference the `codeeffects.common.css` and one of the theme files on your web page as you would with any other CSS file. Refer to one of our [demo projects](https://github.com/orgs/codeeffects-software/repositories) for implementation examples.