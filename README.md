# OpenCraft Instance Manager Frontend

A single-page app for the [OpenCraft Instance Manager (OCIM)](https://github.com/open-craft/opencraft).

# Frontend Architecture/Stack

We use React, TypeScript, Bootstrap, and SCSS for the frontend.

For global state shared among different parts of the application, we use Redux. 
So things like the user's login/logout status, the user's details etc. should 
be kept in the Redux state and modified using actions.

For all other state, such as data required just for a particular 
widget/component/page, we just use "normal" React props/state; this is because 
Redux imposes a lot of boilerplate code overhead and offers little value if the 
state is not shared among diverse parts of the application.

However, just because we use React and, when necessary, Redux, this doesn't mean
all the code has to be inside React components or the Redux store; "regular" 
JavaScript code launched from main.tsx that for example talks to the Redux 
store is always an option. 

## React Component Guidelines

When coding React components, please keep the following in mind:

* All components should subclass [`React.PureComponent`](https://reactjs.org/docs/react-api.html#reactpurecomponent).
* All component props and redux state variables that are complex objects should be immutable (enforced via TypeScript by declaring them as `ReadOnlyArray<T>`, `ReadOnlySet<T>`, and `ReadOnly<T>`, mutated using [`immutability-helper`](https://github.com/kolodny/immutability-helper) or plain ES6).
* Write sensible tests, including unit tests, [snapshot tests](https://jestjs.io/docs/en/snapshot-testing), and/or end-to-end tests.
    - When reviewing changes to snapshot tests, carefully review the HTML diff to ensure the changes are expected.
    - Test files should be located alongside the component they test (so `Card.tsx` is tested in `Card.spec.tsx`)
    - You can use the `mountWithContext()` helper method to mount any component with [Enzyme](https://airbnb.io/enzyme/) to test how it behaves.
    - Never import jest/test related code in `.ts` files that are part of the application (only in `.spec.tsx` files); this avoids adding several megabytes of test code to the app bundle.
    - When in doubt, end-to-end tests and Enzyme behavior tests are preferred.  Snapshot tests are still useful, but not as important as an end to end test or even a regular React component test like [this](https://gitlab.com/opencraft/client/LabXchange/labxchange-dev/blob/0abe7f296706d0e3bae1b8249062bf03a15fa4cb/frontend/src/search/components/Taxonomy/Taxonomy.spec.tsx#L135-163) which simulate user interaction with the component and then make assertions about the result.
* Prefer to split big components up into smaller components that get composed together.
* Use the [Container Pattern](https://medium.freecodecamp.org/react-superpowers-container-pattern-20d664bdae65)
    - Don't write a `FoobarComponent` that loads `Foobar` data from the REST API then renders it; instead write a `FoobarComponent` that accepts `Foobar` data as a prop (so its props are never `undefined`), and then write a `FoobarContainerComponent` which loads the `Foobar` data from the REST API and then once it's loaded renders a `<FoobarComponent data={foobarData}/>`. This lets us test the presentation/UX separately from the API/backend, provides better separation of concerns, and reduces the need to write code that checks if the prop has data or not when rendering.
* Make sure the component is internationalized (see below) and accessible.
* We use and contribute back to [Paragon](https://github.com/edx/paragon) wherever possible for foundational accessible UI components.