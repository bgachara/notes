# How browsers work + How to build one

ref: `How browsers work`. Tali Garsiel

## Introduction

- Browsers are the most widely used software.
- Main function of a web browser is to present the web resource you choose by requesting it from a server and displaying it in the browser window.
- Location of resource is specified using a Uniform Resource Identifier(URI)
- The way the browser inteprets and displaysHTML files is specified in the HTML and CSS specs, which are maintained by the W3C.
- Specs do not define UI elements a browser must have, but lists some common elements, i.e address bar, status bar, tool bar.

## Browser High Level Structure

- Main components:
  - The user interface:
    - includes address bar, back/forward button, bookmarking menu, all display parts of browser except window where you see the requested page.
  - The browser engine
    - marshals actions between UI and rendering engine.
  - The rednering engine
    - responsible for displaying requested content..i.e HTML or CSS.
  - Networking
    - for network calls such as HTTP requests, using different implementations for different platforms behind a platform-independent interface.
  - UI backend
    - used for drawing basic widgetslike combo boxes and windows.
    - exposes a generic interface that is not platform specific.
    - underneath it uses operating system user interface methods,
  - Javascript interpreter
    - parse and execute Js.
  - Data storage
    - persisitent layer
    - Browser may need to save all sorts of data locally, i.e cookies.
    - also support storage mechanisms such as localstorage, indexedDB, webSQL and filesystem
- Chrome rus multiple instances of the rendering engine...for each tab.

## Rendering Engines

- I.E uses Trident, Firefox uses Gecko, Safari uses Webkit, Chrome and Opera use Blink(Webkit fork)
- Webkit is an open source rendering engine which started as an engine for Linux platform before mod by Apple to support Mac and Windows

## The main flow

- rendering engine will start getting the contents of the requested document from the networking layer, usually done in 8KB chunks.
- After that this is the basic flow of the rendering engine
  - Parsing HTML to construct the DOM tree --> Render tree construction --> Layout of the render tree --> Painting render tree
- This is a gradual process.
- For better user experience, the rendering engine will try to display contents on the screen as soon as possible.
- Parsing, Grammars, Parser - Lexer combination.
- Document --> Lexical Analysis --> Syntax Analysis --> Parse Tree
- Parsing process is iterative, parser asks for next token, try to match it, if rule matches, added to parse tree and another token requested.
- Raise an exception if the token doesnt match
- Translation 
- Context Free Grammar is a grammar that can be entirely expressed in BNF.
- Types of parsers
  - Top-down parser
    - examine high-level structure of the syntax and try to find a rule match.
  - Bottom-up parser
    - start with input and gradually transform it into syntax rules, starting from low level rules until high rules are met.
    - also called a shift reduce parser
- Generating parsers
  - tools that generate parsers, feed them grammar of your language, vocabulary and syntax rules and they generate a working parser.
  - Webkit uses Flex for creating a lexer and Bison for creating a parser.
- HTML parser
  - not a context free grammar
  - formal format for it is Document Type Definition
  - as opposed to XML, its more forgiving hence the lack for stiff definition
  - DOM
    - output tree is a tree of DOM element and attribute nodes
    - object representation of the HTML document and the interface of HTML elements to the outside world like Javascript.
    - has one-to-one relation to the markup.
  - HTML parsing is reentrant, source may change during parsing
  - contains two stages: tokenization and tree construction
  - Algo is expressed as a state machine, each state consumes one or more characters of the input stream and updates the next state according to those characters.
  - This means the same character may yield different results for the correct next state, depending on the current state.
  - Browser tolerance errors
- CSS parsing
  - CSS is a CFG laguage hance parsers can be built for it.
  - Webkit uses Flex and Bison parser generators to create parsers automatically from CSS grammar files.
  - Firefox uses a top down manually written parser.
- Script processing
  - initially was synchronous, parses and executed immediately script tag is reached.
  - defer keyword now included, execute after document has been parsed.
  - speculative parsing: onyl for external resources.
  - style sheets
  - 
- Render Tree construction
  - while DOM tree is being constructed, browser constructs another tree, the render tree.
  - This tree is of visual elements in the order in which they will be displayed
  - It is the visual representation of the document
  - Purpose of this tree is to enable painting the contents in their correct order.
  - Firefox - frames, Webkit - renderer or render object.
  - A renderer knows how to lay out and paint itself and its children, usually represents a rectangular area usually corresponding to a node's CSS box as described by CSS2 spec.
  - render tree relation to DOM tree is not one-to-one as non-visual elements will not be inserted to render tree...i.e head, also els with display none and visibility hidden also elements with multiple components i.e select elements.
- The flow of constructing the tree.
  - in firefox presentation is registered as a listener for DOM updates.
  - presentation delegates frame creation to the FrameConstructor and the constructor resolves style and creates a frame.
  - In webkit, process of resolving the style and creating a renderer is called "attachment", every node has an attach method.
  - Attachment is synchronous, node insertion to the DOM tree calls the new node "attach" method.
- Style computation
  - calculating the style properties of each element, visual properties of each render object.
- Sharing style data
- Using the rule tree to compute style contexts
- Style sheet cascade order
  - cascade order is from low to high
    - Browser declarations
    - User normal decl
    - Author normal dec
    - Author imp dec
    - User imp dec
  - declarations with the same order will be sorted by specificity and order they are specified.
    - Specificity
      - count 1 if the declaration it is from is style  attribute rather than a rule with a selector,0 otherwise(= a)
      - count the number of ID attributes in the selector(= b)
      - count the number of other attributes and pseudo-classes in the selector(= c)
      - count the number of elements names and pseudo-elements in the selector(= d)
    - concatenating the four number gives the specificity
    - after rules are matched they are sorted according to cascade rules, Webkit uses bubble sort for small lists and merge sort for larger ones.
- Webkit uses a flag to mark whether all top level style sheets have been loaded.
- Layout
  - when renderer is created and added to the tree, it does not have a position and size, calculating these values is called layout or reflow.
  - HTML uses a flow based layout model, meaning its possible to compute geometry in a single pass.
  - Elements later in the flow dont affect those earlier in the flow.
  - The coordinate system is relative to the root frame. Top and left coordinates are used.
  - Layout is a recursive process, begins at root renderer and continues through all frame hierarchy, computing geometric information for each renderer that requires it.
- Dirty bit system
  - in order not to do a full layout for every small change, browsers use a dirty bit system, a renderer that is changed or added marks itself and its children as dirty needing layout.
  - there are two flags, dirty and children are dirty for when renderer itself is okay but at least one of its children is dirty.
- Global and incremental layout
  - layour can be triggered globally
    - screen resizing
    - global style change that affects all renderers, font size change
  - incremental layout is triggered when renderers are dirty, i.e when new renderes come in via network and need to be appended to render tree.
- Asynchronous and snychronous layout
  - incremental layout is done asynchronously
  - global layout will be triggered synchronously
- Optimizations
  - use of cache in cases of resizing and or change in renderer position
- Layout process
  - parent renderer determines its own width.
  - parent goes over child and
    - place the child renderer(sets its x and y)
    - calls child layout if needed
    - parent uses childs accumulative heights and heights of margins and padding and sets its own height, used by parent renderer's parent.
    - sets its dirty bit to false.
    - width calculation
      - renderer's width is calculated using the container's block width, the renderer's style widht property, the margins and borders.
  - line breaking
- Painting
  - render tree is traversed  and renderers paint() method is called to display content on the screen, use the UI infrastructure component.
  - can also be global or incremental
  - Painting order
    - background color
    - background image
    - border
    - children
    - outline
- Rendering engine is single threaded.
- Everything except network operations happens in a single thread, in FIrefox and Safari this is the main browser thread, In chrome it is the tab process main thread
- Network operations happen in sevral parallel threads, 2-6 max connections
- Browser main thread is an event loop , infinite loop that keeps the process alive, waits for events and processes them.
- CSS2 visual mode.
  - canvas: the space where the formatting structure is rendered.
  - box model
  - positioning scheme: normal, float, absolute
  - box types: block , inline
  - layered representation: z-index.
  
  