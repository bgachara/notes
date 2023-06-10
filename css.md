# CSS tiny reference

ref: `web.dev css`

## Terms and Ideas

### Box model

- Intrinsic and extrinsic sizing
- setting width to min-content allows the content to control its sizing.
- Overflow property used to control how an element handles overflow content.
- Box areas: content box, padding box, border box, margin box.
- Outline and box-shadow occupy the margin area.
- content-box vs border-box

### Selectors

- Types
  - universal 
  - type
  - class
  - id
  - attribute
  - grouping selectors
  - pseudo-classes and pseudo-elements 
    - : and ::
    - :: insert new element with CSS.
  - combinators
  - compound 
  
### The cascade

- cascade is the algorithm for solving conflicts where multiple CSS rules apply to the same HTML element
- stages
  - positiona and order of appearance
  - specificity
  - origin
  - importance

### Specificity

- specificity scoring
- each selector type earns points, you add all of these points up to calculate a selector's overall specificity.
  - universal = 0
  - element or pseudo-element = 1
  - class, pseudo-class, attribute = 10
  - id = 100
  - inline style = 1000
  - !important  = 10000
  
### Inheritance

- some properties are inheritable by other elements
- inheritance cascades downwards
- list of inheritable properties???
- Keywords
  - inherit
  - initial
  - unset

### Color

- Numeric colors
  - hex
    - shorthand for RGB
    - you can also define an alpha value with numerical colors
  - RGB
    - uses numbers or percentages
  - HSL
    - hue, saturation, lightness
- named colors, 148-ish

### Sizing Units

- Can use character units
- numbers: 
- percentages
- absolute vs relative lengths
- angles units: i.e rotate

### Layout

- Display property
- Flexbox and Grid 
