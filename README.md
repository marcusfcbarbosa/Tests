module.exports = {
  create: function (context) {
    return {
      'TemplateElement, Literal' (node) {
        if (node.value && typeof node.value.raw === 'string') {
          const rawValue = node.value.raw.trim();

          // Regular expression to match <div> elements with routerLink directive
          const divRegex = /<div\s[^>]*routerLink\s*=\s*['"][^'"]+['"][^>]*>/g;

          // Check each match
          let match;
          while ((match = divRegex.exec(rawValue)) !== null) {
            // Check if key events are present
            if (!/\(keydown\)|\(keypress\)|\(keyup\)/.test(match[0])) {
              context.report({
                node,
                message: 'Div with routerLink directive should have key events for accessibility.',
              });
            }
          }
        }
      },
    };
  },
};
