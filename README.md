# To me, performance is a feature, and I simply like using fast websites more than slow websites. 
Interfaces succeed because of hundreds of choices. This is a living, non-exhaustive list of those decisions.

## Interactions
- **design and optimize for two classes of users: anonymous, and logged in.** - we minimize the footprint of HTML, CSS and Javascript for anonymous users so they get their pages even faster. We load a stub of very basic functionality and dynamically “rez in” things like editing when the user focuses the answer input area. For logged in users, the footprint is necessarily larger, but we can also add features for our most avid community members at will without fear of harming the experience of the vast, silent majority of anonymous users)
- **made sure we’re serving the absolute minimum necessary to our anonymous users**
- **you can never serve a webpage faster than it you can render it on the server** - The simple act of putting a render time in the upper right hand corner of every page we serve forced us to fix all our performance regressions and omissions.
