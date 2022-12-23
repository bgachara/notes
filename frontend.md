# Concepts in FE Development

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

- Client-Side Rendering.
    - render individual pages all in the client's browser.
    - use web apis provided by modern browsers.
    - poor seo as se don't execute js.
    - load can get huge as js is being executed hence splash screens.
- Server-Side Rendering.
    - computes all html before responding and delivering a full page.
    - seo friendly.
    - most metrics are fast.
    - ttb slower as all computations are done.
- Static Site Generation. 
    - Precomputes dynamic pages up front.
    - all metrics served well too.
    - lower costs too and excellent scalability.
    - changes require rebuilding of the site.
- Hybrid rendering strategies.
    - universal/isomorphic apps, hydration and client-side fetching.
    - 
## Metrics

- Time to First Byte (TTFB)
    - Used to determine the load on the server and the network between client and server.
- First Paint.
    - Included TTFB and the computation client has to do before rendering job.
- First Contentful Paint
    - Marks time first text or image is painted.
- Largest constentful Paint
    - Time until user sees expected results.
- Time to Interactive
    - Time taken for page to be fully interactive.


## State Management

- Scalars(booleans,numbers,strings) vs References(objects, arrays) handled differently.
- useState, useReducer, useMemo, useCallback, useEffect, useRef
- Context and Custom hooks.
- Libraries: Zustand, Valtio, Jotai