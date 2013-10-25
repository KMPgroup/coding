### Selector's specificity

- `1,0,0,0` - for inline style declarations
- `0,1,0,0` - for every ID attribute value
- `0,0,1,0` - for every class attribute value, attribute selection, or pseudo-class
- `0,0,0,1` - for every element and pseudo-element given in the selector
- `0,0,0,0` - for combinators and the uneversal selector

#### Examples:
```css
h1 {color: red;} /* specificity = 0,0,0,1 */
p em {color: purple;} /* specificity = 0,0,0,2 */
.grape {color: purple;} /* specificity = 0,0,1,0 */
*.bright {color: yellow;} /* specificity = 0,0,1,0 */
p.bright em.dark {color: maroon;} /* specificity = 0,0,2,2 */
#id216 {color: blue;} /* specificity = 0,1,0,0 */
div#sidebar *[href] {color: silver;} /* specificity = 0,1,1,1 *
```