## Run

```sh
yarn install
yarn dev
```

## Architecture

The project uses `parcel` to bundle the application. The application uses hooks to encapsulate few features.
One of the hook is a `store`.

### Store

The shape of the store looks like this:

```ts
interface IGlobalState {
  questions: IQuestion[];
  answers: TypeAnswers;
  result: IResultOptions[];
  cursor: number;
}
```

The types are kept in `src/types.ts`.

To have a global store, I have used React's context and useReducer methods to build a small store like redux.

```js
export const GlobalStateContext = createContext<
  [IGlobalState, Dispatch<IAction>]
>([initialState, () => {}]);

export function StoreProvider(props: StoreProviderProps) {
  // make globalState and dispatch available to all components
  const value = useReducer(reducer, initialState);
  return (
    <GlobalStateContext.Provider value={value}>
      {props.children}
    </GlobalStateContext.Provider>
  );
}

export const useGlobalStore = (): [IGlobalState, Dispatch<IAction>] => {
  return useContext(GlobalStateContext);
};
```

This can be used just like how we use Redux.

```js
const [store, dispatch] = useGlobalStore();
```

### Offline hook

The offline hook is used to save data in localStorage.

```js
const [offlineData, setOfflineData] = useOfflineHook();
```

There are other components which is responsible for rendering the UI. Most components directly access the store.
Since the components are directly connected to store, any change in store will re-render the components linked with the store. For this
we can use Selectors to slice the store.

```js
Store (context API)
|
|___Question.tsx // render question
|      |
|      |___Answer.tsx // render answer
|           |
|           |____Option.tsx // render options
|
|___Result.tsx // renders result page

```

## ADA

The solution is ADA complaint. Everytime a new question appears, the focus is set to the first element.
The main question block has the attribute `aria-live="polite"` which helps in reading its content during updates.
