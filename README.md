# Typescript & React Guidelines
Personal Typescript and React guidelines for new and existing projects.
This is an open source **CC0** project and can be used by anyone everywhere.

Kindly note that still these are the guidelines developed by myself for myself and they are not taken from any official source.

## Variable and Function

- Use `camelCase` for variable and function names.
- Prefer `inline function` if it is being defined as a variable of a class.
- Prefer `function` if it is a standalone function.
- Do **NOT** use `var` for variable declaration, use either `const` or `let` for mutable variables.

## Types

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

> A lot of modern websites, teams and tools are using single `'` quotes for their projects,
> but I see `"` double quotes a better solution if the backend is written in `Java` and it makes
> it easier to read and understand.

## Spaces & Indentation 

- Use `2 Spaces` for indentation.
- Do **NOT** use tabs or 4 spaces as this makes things messy for large files.

## Semicolons

- Use semicolons.

