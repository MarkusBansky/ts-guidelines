# React Application Architecture Guide

This file defines basic architectural consepts that are adviced to be used to in the applications that use React + Typescript + Redux + Axios.

To give you a bit of a glance on the app structure we are aiming at:

```
project/
|- src/
|  |- actions/
|  |  |- authActions.ts
|  |- components/
|  |  |- carbonShell.tsx
|  |  |- carbonHeader.tsx
|  |  |- carbonFooter.tsx
|  |- models/
|  |  |- requestError.ts
|  |  |- userToken.ts
|  |  |- userCredentialsDto.ts
|  |- pages/
|  |  |- auth/
|  |  |  |- login.tsx
|  |  |  |- logout.tsx
|  |  |  |- components/
|  |  |  |  |- loginForm.tsx
|  |  |- home/
|  |  |  |- home.tsx
|  |- reducers/
|  |  |- authReducer.ts
|  |  |- index.ts
|  |- store/
|  |  |- axiosStore.ts
|  |  |- historyStore.ts
|  |  |- store.ts
|  |- styles/
|  |  |- index.scss
|  |- utils/
|  |  |- paths.ts
|  |  |- constants.ts
|  |  |- actionUtils.ts
|  |- index.tsx
|  |- react-app-env.d.ts
|- .env
|- package.json
|- tsconfig.json
```

## Defining Redux

Redux has several parts to be defined by the developer in the application source code, such as:

- `actions`
- `reducers`
- `store`

Each of these folders should contain it's relavant components.

### `actions`

Folder named `actions` should contain definishions of **dispatch actions** that are used in UI components to modify the redux store. 

These actions can be plain redux actions containing `type: string` and `payload: any`, or they can be more complex dispatch actions like those used to trigger `axios` middleware. 

It is better to define **action builders** that can be reused in the `actions` folder to create dispatch functions or middleware functions. By simplifiying the creation of each type of action the code can look like this afterwards:

```typescript
export function getCurrentUser() {
    return createReducerAxiosAction<any>(getCurrentUserActionTypes, GET, pathToCurrentUserAPI);
}
```

Each file in the `actions` folder should represent a group of actions triggered over a certain API *tag*, for example each file may represent a Java controller with all cations in it.

Each API path must also be set in a separate constant parameter in the same file containing actions which it is used in. For example an url `pathToCurrentUserAPI` must be defined in the same file where the function `getCurrentUser` is defined.

Each function in actions files must return a plain JavaScript object which corresponds with the requirements of `dispatch()` parameters for `redux`.

Later, each action can be directly used as a parameter in the `mapDispatch` function in the UI. React Component would then recieve a binded property of type `function` which dispatches selected action, the name of such property would be the same as the name of property in the `mapDispatch` object.

So by passing `getCurrentUser` as this: `const mapDispatch = { getCurrentUser };` the component would have a function binded as `getCurrentUser`, other example if you pass the action as `const mapDispatch = { callAxiosActionTogetCurrentUser: getCurrentUser };` your component would have a function binded as `callAxiosActionTogetCurrentUser`, using this trick you can change the name of the function in the component to be different from the name of the action, but this is not advised.

Your average file in `actions` should look something like this:

```typescript
// imports......

const pathToCurrentUserAPI = "/auth/current";

/**
 * Dispatch axion to axios middleware to make a GET HTTP request to API url to get current user by token.
 * The *true* parameter here stands for *useAuth* to state that we need to pass auth token with this request.
 */
export function getCurrentUser() {
    return createReducerAxiosAction<any>(getCurrentUserActionTypes, GET, pathToCurrentUserAPI, true);
}
```

Each file must have an extention `.ts` which supports typescript code only. Read more about dispatch in [React Redux Guidelines](REACT_REDUX_GUIDELINES.md#dispatch).

### `reducers`

Folder named `reducers` should contain all reducers used in the application with a separate file for each reducer.

Each file must contain definitions for reducer function types, for example:

```typescript
export const getCurrentUserActionTypes = createReducerActionTypes("auth/getCurrentUser");
```

Depending which type of action you want to dispatch, if this is a plain redux action then the value of the type should be `string`, if you want to dispatch an `axios` action then the value must be an array `string[]` **always** consisting of 3 strings, the first one corresponds for `_REQUEST` action which is dispatched before the axios request, second is `_SUCCESSS` which is dispatched when axios request was successfull and will return a value, and `_FAIL` which is request on any error, either on server or client side, depending which side it is on, it would contain different error structures.

To sum up, the `actionTypes` of reducer action can be: `string | string[]`.


To create a reducer it is preffered to use the `immer` function `produce` which allows correct and easy mutation of the immutable redux state. 
Reducer file should also contain an interface definition of all properties and their types which this reducer handles.

For example your `reducer` file should look like this:

```typescript
// imports.......

// action types
export const getCurrentUserActionTypes = createReducerActionTypes("auth/getCurrentUser");

// reducer interface
export interface AuthReducer {
    token?: string;
    loading: boolean;
}

// every non-nullable property in the reducer interface must be set in initial state value:
const initialState: AuthReducer = {
    loading: true
}

// the reducer function itself
export const authReducer = (state = defaultState, action: any) => produce(state, (draft: AuthReducer) => {
    switch(action.type) {
        case ...
            // ...
            break;
            
    }
});
```

making so that your `authReducer` would be a function which takes two parameters: `state` (with a default value), and `action`, and returns a value which is received after calling `produce` function with the `state` parameter. `immer` then takes care of updating properties, mutating state, and flaging which object has changed.

The `reducers` folder also must contain a file called `index.ts` which would combine all reducers.
This file must look like this:

```typescript
import {combineReducers} from "redux";
// import your reducers here.....

const combinedReducers = combineReducers({
    // put your reducer function objects here
});

export default combinedReducers;
```

Each file must have an extention `.ts` which supports typescript code only.

### `store`

The `store` folder must contain a `history` file that defines browser history used for router, and `store` file used to create an app store.

This folder should also contain any additional files that would define the middleware used in the redux store. For example it should contain `redux-axios-middleware` definitions.

Example of the `store.ts`:

```typescript
import {createStore, applyMiddleware} from "redux";
import reducers from "../reducers";
import logger from "redux-logger";

// other imports........

const store = createStore(
    reducers,
    applyMiddleware(
        axiosStoreMiddleware,
        logger
    )
);

export default store;
```

Example of the `historyStore.ts`:

```typescript
import {createBrowserHistory} from "history";

export default createBrowserHistory();
```

In order to know how to set up the axios middleware please refer to their documentation.

Each file must have an extention `.ts` which supports typescript code only.

## Component Usage

Folder:
- `components`

Folder `components` must contain any external reusable components over your application. 

If your component can only be used in a single place you shgould not put it here, instead put it it the corresponding `pages` loder along with the page component. 

But if your code can be reusable and generic over whole application, for example a customized form field or a customized `Row` and `Column` component, you should put it in the `components` folder.

Each component must have an extension `.tsx` which stansa for a typescript file with support for XML syntax (HTMl tags).

## Pages

Folder:
- `pages`

Folder `pages` should contain files or folders for React components which are rendered as pages in the React Router config.

It is better to group pages by their category (ex: `home`, `auth`, `users` etc.) and place partial components into the `components` folder next to the page source file.

- `pages`
    - `auth`
        - `login.tsx`
        - `logout.tsx`
        - `components`
            - `loginForm.tsx`
            
Each page React component which has to use redux state must be exported using the `connect` function. You can read more about the correct usage of the `connect` fuinction and all the mappings in [React Redux Guidelines](REACT_REDUX_GUIDELINES.md#store).

A page component must contain an `interface` which defines the properties and members of the components, these include dispatch actions, state properties, and internal members used dynamically in the component.

Example: 
```typescript
interface LoginPageProps {
    error?: RequestError;
    warning?: RequestWarning;
    
    authenticate: (data: CredentialsDto) => void;
}
```

If your component has an initial state then you can set it inside the component to any object you want to:
```typescript
const initialState = { /* ... */ };
```

Then in the component:
```typescript
state = initialState;
```

For the naming conventions inside the React component use:
- `render<opName>(params...) {...}` to define function members that are used to render parts of the page.
- `handle<what?> = (params) => {...}` to define function members that are used to handle user actions, aka: field change, button press, form submit, etc.
- Use internal function like `componentsDidUpdate` to track the state of the component and dispatch actions which rely on that change. 

## Utils

Folder:
- `utils`

Any part of the code, aka function, that you can reuse more than once, is adviced to move to a separate utils file, (you can group multiple functions in the same file if they share some common purpose). 

Use extention `.ts` if your file does not contain html code, otherwise use `.tsx`. To speed things up, it the utils file is very large but there is only a few html functions there, it is better to move them into a separate file with `.tsx` extention and leave the original to be `.ts`, this would speed up compiler slightly.
