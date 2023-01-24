- View of MVC: JSX, commponets, Virtual DOM, one-way data-binding
- events: camelCase
```html
<Button onPress={lightItUp} />
```
- Synthetic events: one API across diff browsers
- List
  - key: UID
  - map()

- render(): return HTML for each component
- this.state: built-in obj, whenever change, render component
  - update: setState() 
- props: pass data between component
  - immutable

- React.Componet
  - higher-order cmpt
  - embed using tag
  - stateless function: can't be reused
  - stateful class
  - lifecycle
    - getInitialState(): excuted before creation
    - componentDidMount(): rendered and placed on the DOM.
    - shouldComponentUpdate(): changes to the DOM and returns bool
    - componentDidUpdate(): invoked immediately after rendering
    - componentWillUnmount(): invoked immediately before destroyed and unmounted permanently.
  - [PureComponent does a shallow comparison on state change.](https://stackoverflow.com/questions/41340697/react-component-vs-react-purecomponent)
  - convert to ReactElement in virtual DOM
  - [The useMemo is used to memoize values, React.memo is used to wrap React Function components to prevent re-renderings. The useCallback is used to memoize functions.](https://medium.com/geekculture/great-confusion-about-react-memoization-methods-react-memo-usememo-usecallback-a10ebdd3a316)
- Redux
  - state container for app
  - store, action, reducer
- Flux
  - arch 
  - dispatcher

- Router: point to views in SPA
-  .module.css: only for the component imported it
