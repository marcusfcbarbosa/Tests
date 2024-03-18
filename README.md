# Tests
eslint
const createRule = ESLintUtils.RuleCreator((name) => name);

module.exports = createRule({
    name: 'require-routerlink-events',
    meta: {
        type: 'suggestion',
        docs: {
            description: 'Require key events for <div> elements with [routerLink] directive in Angular templates.',
            category: 'Best Practices',
            recommended: true,
            url: 'https://example.com/docs/rules/require-routerlink-events',
        },
        fixable: null,
        schema: [], // no options
    },

    create: function (context) {
        return {
            'Property[key.name="routerLink"]': function (node) {
                const parentDiv = findParentDiv(node);
                if (parentDiv) {
                    const hasKeyEvent = parentDiv.value.elements.some(attr => {
                        if (attr.type === 'JSXAttribute' && (attr.name.name === '(keydown)' || attr.name.name === '(keypress)' || attr.name.name === '(click)')) {
                            return true;
                        }
                        return false;
                    });
                    if (!hasKeyEvent) {
                        context.report({
                            node: parentDiv,
                            message: 'Missing key event (keydown, keypress, or click) for <div> element with [routerLink] directive.',
                        });
                    }
                }
            },
        };
    },
});

function findParentDiv(node) {
    let current = node;
    while (current && current.type !== 'JSXElement') {
        current = current.parent;
    }
    return current && current.openingElement.name.name === 'div' ? current.openingElement : null;
}
