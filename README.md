# v-parse-cfg

*V-parse-cfg* is a general context free grammar (CFG) parser based on a [novel chart parsing algorithm](v-parse-cfg.md). In this package, you can find its javascript implementation.

## package specifics

Input to the parser is consisted of JSON array representing a grammar, and a string to parse by that grammar. On parsing error, error position and expected symbols are reported, including the whole parsing chart before the error place. On parsing success, an abstract syntax tree (AST), a s-expression representing the tree, and the complete parsing chart is returned. In an ambiguous parsing case (parsing forest), only the first successful tree is stored in AST and s-expression properties, but within the chart property, the whole forest is returned in terms of chart parser.

## testing grammars online

To test any grammar, [online parser playground](https://contrast-zone.github.io/v-parse-cfg/playground) is provided. Edit a grammar, edit a text to parse, and click on the `parse` button to examine s-expression and AST output. Also, several simplified grammar examples are offered to be loaded in the playground interface.

## using parser in javascript code

Include `src/v-parse-cfg.js` (that's the only file you need) in your javascript code and use the parser like in the following code:

    var grammar = [
        [[    "<start>"], ["<sum>"                          ]],
        [[      "<sum>"], ["<fact>", "+", "<sum>"           ]],
        [[      "<sum>"], ["<fact>", "-", "<sum>"           ]],
        [[      "<sum>"], ["<fact>"                         ]],
        [[     "<fact>"], ["<primary>", "*", "<fact>"       ]],
        [[     "<fact>"], ["<primary>", "/", "<fact>"       ]],
        [[     "<fact>"], ["<primary>"                      ]],
        [[  "<primary>"], ["/\\s*/", "<alternate>", "/\\s*/"]],
        [["<alternate>"], ["/[0-9]+/"                       ]],
        [["<alternate>"], ["(", "<sum>", ")"                ]]
    ];
    
    var text = "1 + 2 * (3 / 4 - 5)";
    
    var parsed = parser.parse(grammar, text);

The above is an example of parsing a simple math expression. Adjust the grammar rules to fit your requirements. The left sides of the grammar are production rule names, while the right sides are rule bodies. Grammars are required to have at least one `<start>` rule that represents the top of the parsed AST. Production rule names are enclosed within `<` and `>` pairs, while regex-es are enclosed within pairs of `/` symbols. Every other expression is treated as literal, and is passed through standard `JSON.parse('"' + ... + '"')` javascript call. This means that to use `"` character in literal, we may use escape sequence `\\\"`. Likewise, if we want to use `<` or `/` as the first character of a literal, we may use escape sequences like `\\u003C` or `\\u002F`, respectively. The unusual number of `\` characters in escape sequences are due to JSON string literals nature, as we utilize JSON native format to specify grammars. Note that because of this, all literals must be enclosed not in single, but in double quotes despite the fact that javascript also allows writing string literals enclosed in single quotes. What we get in return is possibility of using `<` and `/` as the first characters of literals without confusing them with rule names or regex-es.

## license

The *v-parse-cfg* algorithm and its code in this package are licensed under [MIT license](LICENSE).

