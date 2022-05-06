# Agnostic static typing.

**Champion(s)**: 

* TBD (To Be Determined)

**Author(s)**: 

* @lillallol

**Stage:** -1

## The gist.

A type system agnostic, minimal native comment contract, for 
external tools to enable static type checking in JavaScript files, is a pragmatic, 
battle tested, minimal risk, minimal work, starting step for standardizing 
static type checking in EcmaScript, in a way that adheres to the best coding 
practices. In the long run, this will divert the ecosystem from bad practices. 
To make a long story short, something as simple as this:

```js
//:"./index".IAdd
export const add = (a,b) => a+b;
```

achieves that. The following sections explain the intuition and the reasons 
behind the proposal.

> TypeScript is used as a starting point, although it is my understanding that 
any statically typed superset of JavaScript can be used without loss of 
generalization.

## Separation of intend and implementation.

Unfortunately the very design of TypeScript, promotes the violation of this good
 practice. For example you can mix intend with implementation like this:

* `./add.ts`

    ```ts
    const add = (a:number,b:number):number => a+b;
    ```

but you can separate intend and implementation like this:

* `./publicApi.ts`

    ```ts
    export type IAdd = (a : number,b : number) => number;
    ```
* `./add.ts`

    ```ts
    import type {IAdd} from "./publicApi.ts";

    const add : IAdd = (a,b) => a+b;
    ```

<details>
<summary>The advantages you get by sticking to the best practice are a lot.
</summary>

<details>
<summary>1. Maintainable public api.</summary>

Since the types are separated from their implementations, it makes sense to 
gather all of the public api types in a single file. This makes it easy to 
maintain the public api since it is not scattered in multiple files.
</details>

<details>
<summary>2. Less need for <code>.d.ts</code> generators.</summary>

The single file that contains the public api can in fact be a manually 
created `index.d.ts` file, and hence reduce the need for `.d.ts` file 
generation. The files that define the implementations of the public api will
import their corresponding types from `index.d.ts` so that they can conform to 
it.
</details>

<details>
<summary>3. Less need for documentation generations libraries.</summary>

`index.d.ts` can act as documentation. The documentation section of the 
`README.md` of a project can just link to `index.d.ts`. This makes documentation
 generation libraries for the most cases redundant.

Here is an example:

* `./index.d.ts`
    
    ```ts
    /**
     * @description
     * My super function.
    */
    export declare const add : (a : number,b : number) => number;
    
    export type IAdd = typeof add;
    ```
* `./add.ts`
    ```ts
    import type {IAdd} from "./index";

    export const add : IAdd = (a,b) => a+b;
    ```
* `./index.ts`
    ```ts
    export {add} from "./add";
    ```

Notice that both the types and the JSDoc descriptions are both contained in 
`index.d.ts`, and when you import from `index.ts` you actually see the type and 
the JSDoc description of `index.d.ts`.
</details>

<details>
<summary>4. Flexibility on making the public api readable.</summary>

You can put the most important types in the top of `index.d.ts`, and the least 
important in the bottom. You can define types and IDE collapsible regions for 
the sole purpose of improving public api readability for both the library 
maintainer but also the library consumer. 
</details>

<details>
<summary>5. Reduced need to bundle declaration files.</summary>

Many times, I find myself trying to bundle a library to an esm `index.js`
file with its associated `index.d.ts` file. From the previous points it can be 
seen that there will be a reduced need for `.d.ts` bundlers. Just make sure that
`index.d.ts` does not depend on the private api.
</details>

<details>
<summary>6. TypeScript reserves the least possible syntax from JavaScript.
</summary>

You just need these two things:

* type imports

    ```ts
    import type {IAdd} from "./index";
    ```

* type annotations for variable declarations

    ```ts
    export const add : IAdd = (a,b) => a+b;
    ```

Although this point might initially seem not that much of a big deal, it is 
actually the gateway to the next section.
</details>

<details>
<summary>7. Loose coupling of JavaScript code with the type system.</summary>

Not only the public api, but also the private api can be contained in a single 
file. That means that all the types can be contained in two files. This, 
combined with the fact of minimum syntax reservation makes the migration 
(automated or manual) from one type system to another more easy.
</details>

<details>
<summary>8. TypeScript maintainers have less work to do.</summary>

A direct result of reserving the least possible syntax. They no longer need to
enable features that mix implementation and indent:

```ts
export const add = (a:number,b:number):number => a+b;
```

</details>

<details>
<summary>9. The probability for TypeScript to have syntax collisions with 
future JavaScript syntax, gets minimized.</summary>

A direct result of reserving the least possible syntax.
</details>

<details>
<summary>10. Code that looks familiar to the JavaScript developers.</summary>

A direct result of reserving the least possible syntax.
</details>

There is a common misconception that adding the type in a separate file from its 
implementation, will lead to frequent context switching. This is not valid. You 
can just hover over the type and the IDE will show its definition.
</details>

## The inevitable result of minimal syntax reservation: the no compile method.

If separation of intend and implementation inevitably leads TypeScript, without 
loss in static type functionality, to do the minimal possible syntax reservation
from JavaScript, which is:

* type imports:
    
    ```ts
    import type {IAdd} from "index";
    ```

* typing a variable declaration
    
    ```ts
    export const add : IAdd = (a,b) => a+b;
    ```

then why are we not using a comment syntax in JavaScript that enables that? For 
example (something that is already supported by TypeScript):

```js
/**@type {import("./index").IAdd}*/
export const add = (a,b) => a+b;
```

or (something that is already supported by Flow):

> Flow people you will surelly need to correct me since I have never written Flow

```js
/*:: import type {IAdd} from "index";*/
export const add /*: IAdd*/ = (a,b) => a+b;
```

<details>
<summary>Having static type checking without the need to compile is more ergonomic 
than having to compile.</summary>

1. `.ts` files that contain implementations become redundant
1. less work for TypeScript maintainers
1. no `.ts` to `.js` compilation needed
1. no need to wait the compiler
1. no need to develop faster compilers
1. no need for `tsc` to be a compiler
1. one less configuration for the build pipeline
1. no need to deal with the fragmented ecosystem of compiling `.ts` to `.js`
1. no need to depend on extra packages for your code to get executed
1. no need to learn new APIs
1. less security issues due to depending on less code
1. one less source map
1. no need to develop source map generators
1. code can be pasted in the console and it will execute
1. no IoC (Inversion of Control), code executes as it has be written (at least 
during the development stage)
1. syntax from TypeScript will never collide with JavaScript syntax
1. easier adoption of TypeScript in projects that do no use it
1. the code gets even more familiar looking to the JavaScript developer
1. adherence to KISS (Keep It Super Simple)
1. adherence to SRP (Single Responsibility Principle) (e.g. TypeScript is not 
concerned with transpilation anymore)
1. adherence to [TypeScript design goal](https://github.com/Microsoft/TypeScript/wiki/TypeScript-Design-Goals) 
of avoiding expression level syntax

<details>
<summary>22. less verbose code</summary>

Consider the the following public api:

* `index.d.ts`

    ```ts
    export declare const add : (a:number,b:number) => number;

    export type IAdd = typeof add;
    ```

Lets compare verbosity between the no compile method and the compile method:

* `add.ts` (compile method):

    ```ts
    import type {IAdd} from "./index";
    export const add : IAdd = (a,b) => a+b;
    ```

* `add.js` (no compile method as enabled by TypeScript):

    ```js
    /**@type {import("./index").IAdd}*/
    export const add = (a,b) => a+b;
    ```

* `add.js` (no compile method, a possible concise alternative):

    ```js
    //:"./index".IAdd
    export const add = (a,b) => a+b;
    ```

It is clear that the no compile method is the most concise.

</details>

</details>

## The final form of the no compile method.

If the no compile method just needs this:

```js
/**@type {import("./index").IAdd}*/
export const add = (a,b) => a+b;
```

to enable static type checking, then why not create a tc39 approved standard, 
that is type system agnostic, and standardizes the comment contract? This will 
pressure statically typed super sets of JavaScript to conform, and in the long
 run, be reduced to complements of JavaScript.

## Why comments?

If static typing has no effect on runtime, then it makes no sense to have place
on the JavaScript parser. But for something to have no place on the parser, it 
means it has no syntax. But how to enable static typing without syntax? With 
"reserving syntax" in existing comments.

## An intrinsic inability of the no compile method, or a matter of support from the type system?

<details>
<summary>Inferred types.</summary>

Consider the following example:

* `./index.d.ts`
    
    ```ts
    export declare const chunk : <T>(array : T[], length : number) => T[][];

    export type IChunk = typeof chunk;
    ```

* `./chunk.js`
    
    ```ts
    /**@type {import("./index").IChunk}*/
    export function chunk(array,length) {
        /**@type {array[]}*/
        // the previous comment although it works for TypeScript, is a
        // violation of the proposal since it is not type system agnostic
        const toReturn = [];
        /* some code that calculates `toReturn` */
        return toReturn;
    }
    ```

Is something like this:

* `index.d.ts`

    ```ts
    // rest of the file omitted for brevity
    export type IChunkReturnType = InferredType<IChunk>[0][][];
    ```

* `./chunk.js`

    ```js
    /**@type {import("./index").IChunk}*/
    export function chunk(array,length) {
        /**@type {import("./index").IChunkReturnType}*/
        const toReturn = [];
        /* some code that calculates `toReturn` */
        return toReturn;
    }
    ```

possible? Maintainers from type systems can help by giving their suggestions.

</details>

<details>
<summary>Non inferred types.</summary>

Consider using a library that was written in TypeScript:

* `./node_modules/library/index.d.ts`:
    
    ```ts
    /**
     * @description 
     *`DLL` stands for Double Linked List.
    */
    export declare function DLL<T>() : IDLL<T>;

    type IDLLNode<T> = {
        value    : T;
        next     : IDLLNode<T> | null;
        previous : IDLLNode<T> | null;
    };
    type IDLL<T> = {
        tail   : IDLLNode<T> | null;
        head   : IDLLNode<T> | null;
        length : number;
    };
    ```

If a JavaScript developer wants to use `DLL` in their project with the no 
compile method, they will be forced to do hacks:

* `./privateApi.ts`

    ```ts
    import {DLL} from "./node_modules/library/index";

    //The following works with TypeScript 4.7.0-dev.20220428
    export type IJSFriendlyDLL = <T>(
        //parameter only used for type inference
        DLLNodeType : T
    ) => ReturnType<typeof DLL<T>>;

    export type IMyDLLNodeValueType = {
        myNumber : number
    };
    ```

* `./jsFriendlyDLL.js`

    ```js
    import {DLL} from "./node_modules/library/index.js";

    /**@type {import("./privateApi").IJSFriendlyDLL}*/
    export function jsFriendlyDLL() {
        /**
         * Hover over `toReturn` to see that `T` is `any`. Overriding the type 
         * checker is a bad practice. I will address this point in the end.
        */
        const toReturn = DLL();
        return toReturn;
    }
    ```

* `./index.js`

    ```js
    import {jsFriendlyDLL} from "./jsFriendlyDLL.js";

    /**@type {import("./privateApi").IMyDLLNodeValueType}*/
    let _myDLLNodeType;

    const myDLL = jsFriendlyDLL(
        /**
         * We are using `_myDLLNodeType` before initialization, so TypeScript 
         * will lint an error. Maybe TypeScript maintainers can come up with a
         * built-in type function, e.g. `toBeInferred<IMyDLLNodeValueType>` that
         * will make our intentions understood by the linter, so we avoid using
         * `//@ts-expect-error`.
        */
        //@ts-expect-error
        _myDLLNodeType
    );
    ```

Hover over `myDLL`. TypeScript does not work. Inline the type like this:

* `./jsFriendlyDLL.js`

    ```js
    import {DLL} from "./node_modules/library/index.js";

    /**@type {<T>(_type : T) => ReturnType<typeof DLL<T>>}*/
    //It still does not work.
    export function jsFriendlyDLL() {
        // ...
    }
    ```

and still TypeScript does not work. Make sure you add the parameter even if it 
is not actually used:

* `./jsFriendlyDLL.js`

    ```js
    import {DLL} from "./node_modules/library/index.js";

    /**@type {<T>(_type : T) => ReturnType<typeof DLL<T>>}*/
    export function jsFriendlyDLL(_type) {
        // ...
    }
    ```

and now TypeScript works. However the solution is hacky and goes against the 
proposal since it is not type system agnostic. It is a matter of support from
TypeScript to fix that. 

This will not stop `toReturn` from having type `any` though. According to [TypeScript design goals](https://github.com/Microsoft/TypeScript/wiki/TypeScript-Design-Goals)

> ## Non-goals
> Exactly mimic the design of existing languages. Instead, use the behavior of JavaScript and the intentions of program authors as a guide for what makes the most sense in the language.

Non inferred type parameters go against the behavior of JavaScript. There is no
such thing as non inferred type parameter in the language. They should be 
considered a bad practice and not be used.

</details>

<details>
<summary>As assertions.</summary>

There is no need for as assertions. They are a bad practice since they override
the type checker. It is true that the override is safer than `//@ts-ignore`, 
but that does not change the fact that it is an override. This:

```ts
const myVariable = myValues as number;
```

can be replaced with this:

```ts
if (typeof myValue !== "number") throw Error();
const myVariable = myValues;
```

</details>

<details>
<summary><code>enum</code>.</summary>

Typescript maintainers can help on clarifying whether this is a matter of 
support.
</details>
 
 * TODO Function overloading
 * TODO Adding properties to functions
 * TODO typing classes
 * TODO typing classes with extend

## A list of projects that implement the proposal.

1. [es-test](TODO) - testing library (work in progress)
1. [@lillallol/outline-pdf](TODO) - add outline pdf (work in progress)
1. [@lillallol/dic](TODO) - dependency injection library (work in progress)
1. [declarative-tree](TODO) - converts trees to string and vice versa, useful for writing declarative tree tests (work in progress)
1. [scrap-it](TODO) - scraps dom element to outlined pdf (work in progress)
1. [fn-to-cli](TODO) - creates cli based on typescript types (work in progress)
1. [m-mutable](TODO) - client side model library (work in progress)
 
 * TODO discontinue ts-doc-gen-md and explain the reason on its README.md by linking to the proposal
 * TODO discontinue md-in-place and explain the reason
 * TODO discontinue @lillallol/outline-pdf-cjs
 * TODO discontinue @lillallol/outline-pdf-data-structure

I would gladly add in the list, your projects that implement the proposal. Just 
post them in the #1 issue of this repository.

<details>
<summary>You can implement the proposal with native TypeScript.</summary>

1. As far as I understand TypeScript is not working in `.js` files that have 
`.d.ts` files with the same name. So avoid defining implementations in 
`index.js`. This file should just re-export the implementation of the public API
that has been defined somewhere else from `index.js`.
1. Make sure that you take a look at the list of the projects that already 
implement the proposal.
1. Read and understand the context proposal.
1. Via your IDE,search all the files, using the following regular expression:
`@(type|param|returns|return)\s*\{\s*(?!i)`, to find all those places where your 
projects does not satisfy the proposal.

</details>

## Defining the comment contract.

<details>
<summary>A comment for type imports that will also define the type for variable 
declarations.</summary>

Example:

```js
/**@type {import("./path/to/file").nameOfTheExportedType}*/
export const add = (a,b) => a+b;
```

A possible less verbose alternative:

```js
//:"./path/to/file".nameOfTheExportedType
export const add = (a,b) => a+b;
```

1. The path can be absolute or relative.

1. To be a type system agnostic comment, the path has to be without filename
extension. For example the path: `./src/index.d.ts` has to be written as: 
`./src/index`.

</details>

<details>
<summary>A comment that will inform the type checker that a mistake is expected.
</summary>

* Example for statement:

    ```js
    test(add.name,() => {
        //type-check-expect-error
        expect(() => add("1",1)).toThrow();
    })
    ```

* Example for expression:

    ```js
    test(add.name,() => {
        expect(() => {
            add( /* type-check-expect-error */ "1" , 1 )
        }).toThrow();
    })
    ```

</details>

## How this proposal compares to other proposals.

The context proposal does not collide with other tc39 proposals, given that they
do not "reserve syntax" in native comments.

<details>
<summary><a href="https://github.com/tc39/proposal-type-annotations">Proposal 
for type annotations.</a></summary>

The very first two paragraphs of the proposal for type annotations describe its 
aim:

>This proposal aims to enable developers to add type annotations to their JavaScript code, allowing those annotations to be checked by a type checker that is external to JavaScript. At runtime, a JavaScript engine ignores them, treating the types as comments.

>The aim of this proposal is to enable developers to run programs written in TypeScript, Flow, and other static typing supersets of JavaScript without any need for transpilation, if they stick within a certain reasonably large subset of the language.

This is already achieved with TypeScript and Flow.

<details>
<summary>And this is done without the drawbacks of the proposal for type 
annotations.</summary>

1. no need to reserve JavaScript syntax
1. no work for tc39
1. no need for JS parsers to change
1. no need for JS parsers to become slower
1. no pressure for breaking changes for type systems
1. no need to be reduced to a subset of the type system of your choice

</details>

Hence the proposal for type annotations is redundant.
</details>

## The scope of tc39 includes static typing.

According to [ecma-international](https://www.ecma-international.org/technical-committees/tc39/)
the scope of tc39 is:

> ## Scope:
>Standardization of the general purpose, cross platform, vendor-neutral 
>programming language ECMAScript®. This includes the language syntax, semantics,
>and libraries and complementary technologies that support the language. ...

Static typing is a complementary technology that supports the EcmaScript 
language. Hence the context proposal belongs to the scope of tc39.

## Concluding remarks

Super sets are a mistake. The mistake will be fixed only when the super sets get 
reduced to complements. This can only happen by community demand. The demand can 
only be created by educating people. In my experience, when a nobody like me, is
telling to people that we have been doing it wrong all along, even when this is 
done with objective criteria, is mostly not going to work. Hence, without 
influential people/centers taking action, nothing will change.
