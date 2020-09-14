# Typescript & React Guidelines

Welcome to my personal **Typescript & React** application guidelines. In this project I keep my references to typescript styleguide, project architecture guide, and a list of useful tools for `React` and `Typescript` development.

### Index

- [Typescript Code-Style Guide](TS_GUIDELINES.md)
- React App Architecture Guide
- [React Redux Guide](REACT_REDUX_GUIDELINES.md)
- [Basic React stack](#basic-react-stack)
- [Useful tools for React and TS](#useful-tools-for-react-app-development-with-typescript)

## Basic `React` stack

- [`react`](https://reactjs.org/docs/create-a-new-react-app.html) - the **React** itself. Start new app with `npx create-react-app my-app --template typescript` only.
- [`node-sass`](https://create-react-app.dev/docs/adding-a-sass-stylesheet/) - a tool used to implement `scss` and `sass` styles in the app, which later compile into plain `css` with this package.
- [`redux`](https://redux.js.org/introduction/installation) - plain Redux package, which is used to handle React objects store for the whole app.
- [`react-redux`](https://react-redux.js.org/introduction/quick-start) - a package used to bind Redux with React UI.
- [`immer`](https://immerjs.github.io/immer/docs/introduction) - a package used to correctly mutate immutable Redux state, as well as simplify the reducer creation. 
- [`redux-axios-middleware`](https://github.com/svrcekmichal/redux-axios-middleware) - redux middleware that handles `axios` **http** requests to the API through reducers. This middleware keeps track of all events over state and is easy to implement via reducers.
- [`redux-logger`](https://github.com/LogRocket/redux-logger) - ultra-useful tool to keep eye-track over the Redux state of the app and actions implemented over it.
- [`react-router`](https://reactrouter.com/web/guides/quick-start) - package which takes care of your pages composition and redirects, links, view history, etc.
- [`react-hook-form`](https://react-hook-form.com/get-started/) - also very great package to easily develop and handle pro-like forms in react.

## Useful tools for `React` app development with `Typescript`

- Typescript [`eslint`](https://github.com/typescript-eslint/typescript-eslint) - ( a new version of `tslint` which was deprecated in 2019), can be used to check your code style on the fly.
- TS Formatter [`tsfmt`](https://github.com/vvakame/typescript-formatter) - used to format typescript files on the fly or from the cmd. A powerful tool to use for automation to keep the code clean during build or deployment.
- Jest [`jest`](https://jestjs.io/) - a testing library for react applications. Jest is a delightful JavaScript Testing Framework with a focus on simplicity and it works charmly with **Typescript**.
