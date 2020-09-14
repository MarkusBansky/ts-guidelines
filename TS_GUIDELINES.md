# Typescript & React Guidelines
Personal Typescript and React guidelines for new and existing projects.This is an open source **CC0** project and can be used by anyone everywhere. Kindly note that still these are the guidelines developed by myself for myself and they are not taken from any official source.

## Variable and Function

- Use `camelCase` for variable and function names.
- Prefer `inline function` if it is being defined as a variable of a class.
- Prefer `function` if it is a standalone function.
- Do **NOT** use `var` for variable declaration, use either `const` or `let` for mutable variables.

## Class

- Use `PascalCase` for class names.
- Use `camelCase` for class methods and members.

> Reason: This is actually fairly conventional in standard JavaScript.

## Interface 

- Use `PascalCase` for interface names.
- Use `camelCase` for interface methods and members.
- Do NOT prefix interfaces with `I`.

> Same as class.

## Enums

- Use `PascalCase` for enum names.
- Use `PascalCase` for enum members.

> Reason: Convention followed by TypeScript team.

## Namespace

- Use `PascalCase` for namespace names.

> Reason: Convention followed by the TypeScript team. Namespaces are effectively just a class with static members. Class names are PascalCase => Namespace names are PascalCase.

## Null vs. Undefined

- Prefer not to use either for explicit unavailability.
> Reason: these values are commonly used to keep a consistent structure between values. In TypeScript you use types to denote the structure/
#### Bad
    let foo = { x: 123, y: undefined };
#### Good
    let foo: { x: number, y?: number } = { x:123 };
    
- Use `undefined` in general (do consider returning an object like `{valid:boolean, value?:Foo}` instead).
#### Bad
    return null;
#### Good
    return undefined;
    
- Use `null` where it's a part of the **API** or **conventional**.
> Reason: It is conventional in **Node.js** e.g. `error` is `null` for NodeBack style callbacks.
#### Bad
    cb(undefined)
#### Good
    cb(null)
    
- Use **truthy check** for objects being `null` or `undefined`.
#### Bad
    if (error === null)
#### Good
    if (error)
    
- Use `== null` / `!= null` (not `===` / `!==`) to check for **null** / **undefined** on primitives as it works for both **null** / **undefined** but not other falsy values (like ``` '' ```, `0`, `false`).
#### Bad
    if (error !== null) // does not rule out undefined
#### Good
    if (error != null) // rules out both null and undefined

## Types

- Use `PascalCase` for type names.
- Use `camelCase` for type members.
- Always declare strict types for function parmaters, variables and returns.
- Try to avoid type `any` at all costs.
- If described type has internal properties declare a new `interface` or `class`.
- If described type uses an union or intersection declare a new `type`.

#### Bad
    interface Foo {
      foo: string | { bar: string, mar: number };
    }
#### Good
    interface Bar = { 
      bar: string;
      mar: number;
    }
    
    type FooBar = string | Bar;
    
    interface Foo {
      foo: FooBar;
    }
    
## Quotes

- Use `"` (double) quotes everywhere.
- If you cannot use a double quote then use back ticks ``` ` ``` as they represent a complex string.

> Reason: More JavaScript teams do this (e.g. airbnb, standard, npm, node, google/angular, facebook/react). It's easier to type (no shift needed on most keyboards).

> Double quotes are not without merit: Allows easier copy paste of objects into JSON. Allows people to use other languages to work without changing their quote character. Allows you to use apostrophes e.g. `He's not going.`. But I'd rather not deviate from where the JS Community is fairly decided.

## Spaces & Indentation 

- Use `2 Spaces` for indentation.
- Do **NOT** use tabs or 4 spaces as this makes things messy for large files.

## Semicolons

- Use semicolons.

> Reasons: Explicit semicolons helps language formatting tools give consistent results. Missing ASI (automatic semicolon insertion) can trip new devs e.g. `foo() \n (function(){})` will be a single statement (not two). 

## Formatting

The TypeScript compiler ships with a very nice formatting language service. Whatever output it gives by default is good enough to reduce the cognitive overload on the team.

Each IDE has their default formatting tools that comply with `tsconfig.json` in the project and other linter config files.
If such formatting tools are not available by default then I advice to use [`tsfmt`](https://github.com/vvakame/typescript-formatter) for formatting.
It is a command line tool installed via **NPM** and can be run in a project folder or automated through `package.json`.

Formatting is for the correct spaces placement, indentation, bracket placement, screen width, etc.

## Array

- Annotate arrays as foos: `Foo[]` instead of foos: `Array<Foo>`.

> Reasons: It's easier to read. It's used by the TypeScript team. Makes easier to know something is an array as the mind is trained to detect `[]`.

## Filename

- Name files with `camelCase`. E.g. `accordion.tsx`, `myControl.tsx`, `utils.ts`, `map.ts` etc.

> Reason: Conventional across many JS teams.
