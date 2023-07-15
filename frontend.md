# Concepts in FE Development

ref: `patterns.dev`

*Understand and document the important concepts in front end development*

- Client side vs Server side rendering.
- Understand metrics that matter.
- What is good performance.
- MPA(multi-page applications) vs SPA(single page environments).
- Server environments and runtimes(js)
- State Management.

## Frontend Frameworks

*what problem do they solve, alternatives, choosing*

- Change in application state requires a update rerender of the User Interface.
- Reduce complexity involved in working with the DOM.
    / make elements, change properties, put el in other els, get them on page, handle user interactions,.
- Introduce a declarative approach to building UIs.
- Frameworks come with tooling that helps with testing, linting among other functions.
- Compartmentalization.
- Routing. (server-side routing vs client-side routing)

## Rendering Strategies.

- Where and How should it be rendered?? Client or Server, Partially or Progressively
- Choosing the correct pattern could lead to faster builds and excellent loading performance at low processing costs.
- To create great UX, usually try to optimize for user-centric metrics such as Core Web Vitals(CWV).
- CWV metrics measure parameters most relevant to user experience and optimizing it can help ensure a great user experience and optimal SEO for our apps.
- For great DX, we have to optimize our development environments by ensuring faster build times, easy rollbacks, scalable infrastructure, low server costs, dynamic content ad reliable uptime
- Important to remember that every pattern was designed to address specific use cases, can be likely that different rendering patterns are required for pages on the same site.

- Client-Side Rendering.
  - render individual pages all in the client's browser.
  - use web apis provided by modern browsers.
  - poor seo as se don't execute js.
  - CSR resulted in large Js bundles which increased the FCP and TTI of the page.
  - load can get huge as js is being executed hence splash screens.
  - entire web application is loaded on first request, no new request for rendering the pages.
  - Hence allows us to have SPA that support navigation without page refresh and provides a great user experience.
  - Allows for clear separation of client side and server side code.
  - Pitfalls
    - SEO considerations
    - Performance - initial load times affected.
    - Code maintainability
    - Data Fetching - increase load/interaction time of the application.
  - Optimizations
    - Budgeting Javascript - tight js budget for your initial page loads, 100kb-170kb.
    - Preloading - preload critical resources earlier in page lifecycle.
    - Lazy loading - non-critical resources loaded when needed.
    - Code splitting - lazy-load js resources
    - Application shell caching with service workers - mostly offline.
- Server-Side Rendering.
  - computes all html before responding and delivering a full page.
  - connect and fetch operations are handled on the server, HTML required to format the content is also generated on the server.
  - avoid making roundtrips for data fetching and templating, thus rendering code is not required on the client and Js need not be sent to the client.
  - every request is treated and processed differently.
  - provided additional budget for client-side Javascript
  - full page reloads required for some interactions.
  - React for the server
    - ReactDOMServer.renderToString(element)
    - used with ReactDOM.hydrate() - rendered html is preserved as is on the client and only event handlers attached after load.
    - implemented with a .js file both on client and server, with server rendering it and client hydrating it.
  - seo friendly.
  - ideal for highly dynamic and personalized pages that are different for every user.
  - content is unique and should not be cached by the CDN.
  - most metrics are fast.
  - ttb slower as all computations are done.
  - Optimizations
    - execution time of getServersideprops
    - deploy database in same region as functions.
    - add cache-control headers
    - upgrade server hardware.
    - Edge SSR + HTTP streaming.
      - Streaming also enables React Server Components, thus allowing for a good hybrid between static and server rendering.
      - rendertoNodeStream() -allows us to send our application in smaller chunks.
      - Performance improvements - TTFB
      - Handles backpressure
      - Support SEO.
- Static Site Generation. 
  - Precomputes dynamic pages up front.
  - all metrics served well too.
  - lower costs too and excellent scalability.
  - changes require rebuilding of the site.
  - Variations
    - Basic/Plain Static Rendering.
      - little to no dynamic content.
      - no layout shift and fast LCP and FCP.
      - goes extremely wel with CDN for caching.
    - Static Rendering with Client-Side fetch
      - best for when you want to update some data on every request.
      - good TTFB and FCP but LCP is sub-optimal.
      - possibility of layout shifts  
      - High server costs
      - Static with getStaticProps
        - allow us to generate HTML with data on the server
    - Incremental Static Regeneration
      - allows us to pre-render only certain static pages and render the dynamic pages on-demand when user requests them.
      - results in shorter build times and allows automatic invalidation of the cache and regeneration of the page after a specific interval.
      - can use a revalidate field to control how the landing page gets refreshed.
      - stale-while-revalidate strategy.
    - On-demand Incremental Static Regeneration
      - solves the ISR of that not every page needs to be regenerated and its cache invalidated.
      - this occurs on certain events rather than on fixed intervals, i.e on new data availability.
- Progressive Hydration
  - Hydration - attaching of listeners and handlers
  - Individually hydrate the nodes over time, making it possible to request only minimum js.
  - Also allow for prioritization of nodes.
  - Provide great performance by activating your app in chunks.
  - Reduce bundle size
  - Requirements for hollistic progressive hydration
    - allows usage of SSR for all components.
    - supports splitting of code into individual components or chunks.
    - supports client-side hydration of these chunks in a developer defined sequence.
    - does not block user input on chunks that are already hydrated.
    - allows usage of some sort of a loading indicator for chunks with deferred hydration.
  - Reacr concurrent mode - allow React to work on different tasks and switch between them based on a given priority.
- Hybrid rendering strategies.
    - universal/isomorphic apps, hydration and client-side fetching.
- React Server Components
  - aim is to enable modern UX with a server-driven mental model.
  - *
- Selective Hydration
  - pipeToNodeStream()
- Islands Architecture
  - encourages small, focused chunks of interactivity within server-rendered web pages.
  - aims to reduce the volume of Js shipped via islands of interactivity that can be independently delivered on top of otherwise static HTML.
- View Transitions
  - chrome

## React

- Helps build complex and tricky interfaces by organizing your interface into three key concepts; - 
  - Components 
    - js functions that accept input(props) and return react elements describing what should be displayed on the screen.
  - Props 
    - refer to the internal data of a component in React.
    - determine value of prop before component building
    - props are read-only
  - State.
    - object that holds some information that may change over the lifetime of the component.
    - current snapshot of data stored in a component's props.
    - data may change over time so techniques to manage the way data changes become necessary hence state management.
    - variable initialized and managed by the component.
  - Lifecycle
    - every react component goes through three stages; mounting, rendering and dismounting.
    - series of events that occur during these three stages can be referred to as component lifecyle.
      - render(), componentDidMount(), componentWillMount(), shouldComponentUpdate(), componentDidUpdate()
  - Higher Order Components
    - takes in a component and returns a component
  - Context
    - share data between components without explicitly passing down props through every level of hierarchy.
  - React Hooks
    - functions that let you hook into React state and lifecycle features from functional components.
    - let you use state and other React features without writing a class.
- Composition-based hence perfectly map to your design system.

## Metrics

- Time to First Byte (TTFB)
    - Used to determine the load on the server and the network between client and server.
    - time it takes a client to receive the first byte of page content. 
- First Paint.
    - Included TTFB and the computation client has to do before rendering job.
- First Contentful Paint
    - Marks time browser takes to render the first text or image after navigation.
- Largest contentful Paint
    - Time until user sees expected results/main page content.
- Time to Interactive
    - Time taken for page to go from loading to fully interactive.
- Cumulative Layout Shift
    - measures visual stability to avoid unexpected layout shift
- First Input Delay
    - Time from when user interacts with the page to the time when the event handlers are able to run.



## State Management

- Scalars(booleans,numbers,strings) vs References(objects, arrays) handled differently.
- useState, useReducer, useMemo, useCallback, useEffect, useRef
- Context and Custom hooks.
- Libraries: Zustand, Valtio, Jotai

## Source Maps

- Provides a way of mapping code within a compressed file back to its original position in a source file.
- Undos the from Module -> Compiler -> Assets process.
- Browsers parse it to give back an almost accurate representation of the code, i.e debugging, stack traces.
- Not automatically donwloaded or included with web pages.
- Transpilation too.
- Base64 VLQ
    - Optimized to make it easier to have mapping between big numbers and corresponding information in source maps.
    - A line of code is represented in a series of segments
        
## Patterns for Web Apps

- Routing
  - Server
  - Client
  - Hybrid
- Rendering
  - Client-side
  - SSR(streaming, async, shell)
  - Static
- Hydration
  - Eager
  - Full
  - Replayable
  - Progressive
  - Load, Bundle/Serialize, Execute on load

## Computational Caching

- Pure Functions
    - for a given set of parameters they always return the same value
    - do not trigger side effects
- Memoization
    - result caching in process
- Serialization and Hashing
    - serialization: turning data into sth that can be stored.
    - hashing: taking data and scrambling it.
- Persistence
    - saving to disk.

## Performance Patterns

- critical gap between developer expectations and how the browser prioritizes resources on the page.
- Causes of gap
  - Sub-optimal sequencing.
    - understand metrcis well and what they stand for and the order of occurence.
    - FCP before LCP before FID, hence resources should be allocated in that order too.
  - Network/CPU utilization
  - Third-party products.
  - Platform quirks
    - different browser prioritization.
  - HTTP2 prioritization
  - Resource level optimization
- Resource-wise recommendations
  - Critical CSS.
  - Fonts
  - Above the Fold images
  - Below the Fold images
  - 3P Js
- Static Import
  - import code exported by another module via import keyword.
- Dynamic Import
  - dynamically import components using React Suspense.
  - lazy() keyword.
  - loadable components
- Import on visibility
  - intersectionObserver API
  - react-loadable-visibility
- Import on interaction
  - load non-critical resources when user interacts with UI requiring it.
  - ways to load different resources
    - eager - load right away.
    - lazy(route-based)
    - lazy(on interaction)
    - lazy(in viewport)
    - prefetch - load prior to needed, but after critical resources are loaded.
    - preload - eagerly with greater level of urgency.
  - fake loading with a UI facade
    - video embeds
    - authentication
    - chat widgets
  - implemented using the dynamic import(), which enables lazy loading of modules and returns a promise.
  - can combine it with the suspense component.
  - uncanny valley- page looks ready but user unable to tap anything.
  - `How does one get import-on-interaction right??`
  - Understand trade-offs
  - Replacing interactive embeds with a static variant.
- Route-based splitting
  - dynamically load components based on the current route.
- Bundle splitting
  - split code into small reusable pieces.
  - `how to efficiently do this??` 
- PRPL pattern
  - Optimise initial load through precaching, lazy loading and minimizing roundtrips.
  - main considerations
    - pushing critical resources efficiently, which minimizes the amount of roundtrips to the server and reducing the loading time.
    - rendering the initial route soon as possible to improve user experience.
    - pre-caching assets in the background for frequently visited routes to minimize the amount of requests to the server and enable a better offline experience.
    - lazily loading assets and routes that aren't frequently requested.
  - uses service workers to cache resources.
- Tree Shaking
  - reduce bundle size by eliminating dead code.
  - web bundlers job.
- Preload
  - inform the browser of critical resources before they are discovered.
  - preload + async
- Prefetch
  - fetch and cache resources that may be requested some time soon.
  - only do this for necessary resources.
- Optimize loading third party
  - reduce performance impact third-party scripts have on your site.
- List virtualization
  - optimise list performance with list virtualization
  - also known windowing i.e using react-virtualized or react-window.
  - `look into the libraries above...very interesting concept` 
  - CSS-content visibility works along the same lines.
- Compressing Javascript
  - reduce time needed to transfer scripts over the network.
  - Js is second biggest contributor to page size and the second most requested web resource after images.
  - We use patterns that reduce transfer, load and execution time for Js to improve website performance.
  - Can combine compression with other techniques such as minification, code-splitting, bundling, caching and lazy-loading though these techniques may be at odds sometime.
  - Gzip and Brotli most common.
  - Brotli offers better compression ratio.
  - Next.js provides Gzip compression by default but recommends enabling it on proxy.
  - Single large bundle gives better compression than multiple smaller ones.
  - HTTP compression(Accept-Encoding Header -> Content Encoding)
  - Minification - remove whitespace and unnecessary code.(Terser, Webpack)
  - Static(ahead-of-time) vs Dynamic(on-the-fly) compression.
  - Granularity trade-off
    - Improve donwload speed
    - Improve cache hits and caching efficiency
    - Execute Fast
  - `Granular chunking` 
  
## Design Patterns

- Singleton Pattern
  - share a single global instance throughout our application.
  - use variable equal to a reference to instance when a new one is created.
  - Object.freeze makes sure consuming code cannot modify singleton.
  - Tradeoffs
    - save on memory, as only one needed.
    - singleton considered anti-pattern.
    - testing is hard
    - dependency hiding
    - global behaviour
  - State management in React, global state over singletons, read-only as opposed to mutable state of singleton.
- Proxy Pattern
  - intercept and control interactions to target objects.
  - new Proxy(obj, {get, set}), {} reps handler.
  - useful to add validation.
  - reflect built-in object to work with target object.
  - formatting, notifications or debugging.
  - not recommended for performance code.
- Provider Pattern
  - make data available to multiple child components.
  - createContext()
  - value prop that can carry data to all components wrapped within this provider.
  - useContext hook
  - useful for sharing global data, i.e UI theme data. 
  - Major con is components have to consume the context to re-render on each state change
- Prototype Pattern
  - share properties among many objects of the same type.
  - easily let objects access and inherit properties from other objects.
- Container/Presentation Pattern
  - enforce separation of concerns by separating view from the application logic.
  - presentation - how vs container - what
  - container elements pass data to presentational elements.
  - Hooks though made this easier as they act as container elements themselves.
- Observer Pattern
  - use observable to notify subscribers when an event occurs.
  - subscribe certain objects, observers, to another object, observable.
  - observable object contains 3 important parts
    - observers - arrays of objects to be notified.
    - subscribe()
    - unsubscribe() - remove observers
    - notify() - fire when event happens
  - useful in asynchronous, event-based data.
  - RxJs
  - prone to complexity.
- Module Pattern
  - split up your code into smaller, reusable pieces.
  - named exports then import{} from where/they/are
  - for cases of collision, we can use rename using as.
  - could also use `default export`, although only one per module.
  - also import using * for all exports.
  - dynamic import using import() which can receive expressions allowing for passing template literals.
- Mixin Pattern
  - Add functionality to objects or classes without inheritance
  - A mixin is an object we can use in order to add reusable functionality to another object or class without using inheritance.
  - Mixins can't be used on their own.
  - Object.assign(obj.prototype, mixin)
- Mediator/Middleware Pattern
  - use a central mediator object to handle communication between components.
  - prevent all objects from talking to every other object, many-to-many relationship
- HOC Pattern
  - pass reusable logic down as props to components throughout your application
  - logic can be styling components, authorization, adding global state.
  - HOC is a component that receives another component, HOC contains logic that we want to apply to passed parameter component, returning component after applying logic.
- Render Props Pattern
  - pass jsx elements to components through props.
  - a render prop is a prop on a component, which value is a function that returns a JSX element.
  - props.render()
  - render props over lifting state
- Hooks Pattern
  - use functions toreuse stateful logic among multiple components throughout the app.
  - manage a component state and lifecycle methods
  - make it possible to
    - add state to functional component
    - manage a component lifecyle without having to use lifecycle methods
    - reuse the same stateful logic among multiple components throughout the app.
  - add state using useState()
  - [value, setValue] = useState(initValue);
  - useEffect combines DidMount,DidUpdate,WillUnmount
  - uses a dependancy array.
- Flyweight Pattern  
  - reuse existing isntances when working with identical objects.
  - 
- Factory Pattern
  - use a factory function in order to create objects
- Compound Pattern
  - create multiple components that work together to perform a single task.
  - make one component a property of another component.
  - 
- Command Pattern
  - decouple methods that execute tasks by sending commands to a commander.
  - replace class methods with one that executes any command passed to it.