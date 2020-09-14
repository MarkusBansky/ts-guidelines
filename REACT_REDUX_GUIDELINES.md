# React Redux Guidelines

This page is dedicated to explain some specific features of `redux`, it's usage and the basic implementation of it in `react` projects.

## What is Redux

Redux is a pattern and a library used for managing and updating application state, using events called "actions". It serves as a centralized store for application state that needs to be used acc=ross the entire application, with rules ensuring that the store can only be updated in a predictable fashion. Redux helps to manage the **global state** - state that is needed across many parts of the application.

The patterns and tools provided by Redux make it easier to understand when, where, why and how the state in the application is being updated, and how the application logic will behave when those changes occur.

Redux guides towards writing code that is predictable and testable, which helps get confidence that the application will work as expected, and it helps to strictly define all possible outcomes of state manipulation.

## When to use Redux

Redux is most useful when:

- Large amounts of application state are needed in many components across the app.
- The app state is updated frequently over time.
- The logic to update that state may be complex (axios, middleware).
- The app has a medium- or large-sized codebase and is worked on by many people.

## Terminology

Redux consists of a small amount of components but they are crusial to understand.

### Actions

An **action** is a plain JavaScript object that **must** have a `type` field. It can also have any additional fields. **An action is an event that describes what has happened to the application**.

The `type` field should be a string that gives this action a descriptive **unique** name, like `"todos/todoAdded"`. Per recommendation from the redux team is is better to write the name if the action like this `<category>/<eventName>` where category defines the feature, action category or a group of methods, and the second part is a specific thing that happened.

If we want to put other information into the action, we must put it in the `payload` object like this:

    const addTodosAction = {
        type: "todos/todoAdded".
        payload: "Buy milk"
    }

### Action Creators

An **action creator** is a function that builds the action for us from parameters, so we do not have to write the action object by hand every time. This can be reused by `axios` middleware for example, to create requests.

    const addTodo = (text: string) => {
        return {
            type: "todos/todoAdded",
            payload: text
        }
    }

### Reducers

A **reducer** is a function that receives the **current** `state` and `action` object, it then decides how to update the state if necessary, and returns the new state. Basic type is: `(state, action) => newState`.

**Think of a reducer as an event listener which handles events based on the received action (event) type**.

> Reducer functions get their name because they are similar to the kind of callback function you pass to the `Array.reduce()` method.

Reducers **must always** follow some specific rules:
- They should only calculate the new state value based on the `state` and `action` arguments.
- They are not allowed to modify the existing `state`. Instead, they must make *immutable updates* by copying the existing `state` and making changes to the copied values. This is where `immer` packages comes in handy.
- They must not do any asynchronous logic, calcualte random values, or cause other *side effects*.

The logic inside the reducer usually follows the same series of steps:
- Check to see if the reducer cares about this action.
- If **yes**, make a copy of the state, update the copy with new values, and return it.
- Otherwise, return the existing state unchanged.

### Store

The current Redux application state lives in an object called the **store**.

The store is created by passing in a reducer, and has a method called `getState` that returns the current state value. By using `react-redux` user can connect the store to their UI components and map values from it directly to the component properties.

As the first argument passed in to `connect`, `mapStateToProps` is used for selecting the part of the data from the store that the connected component needs. It’s frequently referred to as just `mapState` for short.

- It is called every time the store state changes.
- It receives the entire store state, and should return an object of data this component needs.

Now, `mapStateToProps` should be defined as a function:

    const mapState = (state: any, ownProps?: any) => { }
    
It should take a first argument called `state`, optionally a second argument called `ownProps`, and return a plain object containing the data that the connected component needs.

This function should be passed as the first argument to `connect`, and will be called every time when the Redux `store` state changes. If you do not wish to subscribe to the `store`, pass `null` or `undefined` to `connect` in place of `mapStateToProps`.

Your `mapStateToProps` function should return a **plain object** that contains the **data the component needs**:

- Each field in the object will become a **prop** for your actual component.
- The values in the fields will be used to determine if your component needs to re-render.

Example:

    const mapStateToProps = (state: any) => {
      return {
        a: 42,
        todos: state.todos,
        filter: state.visibilityFilter
      }
    }

> Note!!! If application uses `combineReducers` this means that the `state` parameter in the `mapStateToProps` would be an object containing reducers states.

[Read more on React-Redux `mapState`](https://react-redux.js.org/using-react-redux/connect-mapstate)

### Dispatch

The Redux store has a method called `dispatch`, **which is the only way to update the state is to call `store.dispatch()` and pas in an action object**. The store will run its reducer function and save the new state value inside, and we can call `getState()` to retrieve the updated value.

Using `react-redux` we can map our UI component properties of type `function` to dispatches, and by calling each of these functions later we would then trigger the same `dispatch` event over the app store. **Dispatching** a function is like **triggering an event over app store** which then would be passed as action to the reducer and the latter would decide how to modify the state.

With React Redux, your components never access the store directly - `connect` does it for you. React Redux gives you two ways to let components dispatch actions:
- By default, a connected component receives `props.dispatch` and can dispatch actions itself.
- `connect` can accept an argument called `mapDispatchToProps`, which lets you create functions that dispatch when called, and pass those functions as `props` to your component.

Because this is so common, connect supports an "object shorthand" form for the `mapDispatchToProps` argument: if you pass an object full of action creators instead of a function, connect will automatically call `bindActionCreators` for you internally.

As stated by *react-redux team*: **We recommend always using the "object shorthand" form of `mapDispatchToProps`, unless you have a specific reason to customize the dispatching behavior.**

Thus, the way to map UI properties is to pass a plain object consisting of functions to `connect` like this:

    const mapDispatchToProps = {
      increment,
      decrement,
      reset
    }
    
    export default connect(mapState, mapDispatchToProps)(Counter);
    
Later on `connect` takes a `React.Component` as another parameter and **binds** these functions to the defined properties in your component as a **despatch** functio with an action parameter of the function you defined. This is possible because in JavaScript everything is an `object` event a function.

[Read more on React-Redux `mapDispatch`](https://react-redux.js.org/using-react-redux/connect-mapdispatch)

## Application flow

For Redux we can break the app flow steps in some detail like this:

- Initial Setup:
    - A Redux store is created using root reducer function.
    - The store calls the root reducer oncem and saves the return value as it initial `state`.
    - When the UI is first rendered, UI components access the current state of the Redux store, and use that data to decide what to render. They also subscribe to any future updates so they can know if the state has changed.
- Updates:
    - Something happens in the app, such as user clicking on a button.
    - The app code dispatches an action to the Redux store, like `dispatch({type: 'bla'})`.
    - The store runs the reducer function again wuith the previous `state` and the current `action` and saves the return value as the new `state`.
    - The store notifies all parts of the UI that are subscribed that the store has been updated.
    - Each UI component that needs data from the store checks to see if the parts of the state they need have changed.
    - Each component that sees its data has changewd forces a -re-render with new data, so it can update what is shown on the screen.
    
Here is what the data flow looks like visually:

![](https://redux.js.org/assets/images/ReduxDataFlowDiagram-49fa8c3968371d9ef6f2a1486bd40a26.gif)
