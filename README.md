# Reducing the supersets to complements.

**Champion(s)**: 

* TBD (To Be Determined)

**Author(s)**: 

* @lillallol

**Stage:** -1

## Problem statement

There is no tc39 standardization on anything related with static typing. This 
has resulted in wide adoption of third party tools that enable static type 
checking with no actuall standard on how to do it. Now, supersets like 
TypeScript, that mix implementation with intend and compile to JavaScript are
considered the norm, despite being suboptimal solutions\[[1](#separation-of-intend-and-implementation)\]\[[2](#the-inevitable-result-of-minimal-syntax-reservation-the-no-compile-method)\]. To make things worse, there are proposals
to make them native\[[3](https://github.com/tc39/proposal-type-annotations)\].

## Proposal

A type system agnostic, zero syntax reservation, ergonomic, native comment 
contract, for external tools to enable static type checking in JavaScript files,
is a pragmatic, battle tested\[[4](#a-list-of-projects-that-implement-the-proposal)\],
minimal risk, minimal tc39 work, superior to supersets\[[1](#separation-of-intend-and-implementation)\]\[[2](#the-inevitable-result-of-minimal-syntax-reservation-the-no-compile-method)\], starting step for standardizing enough 
static typing to reduce the supersets to complements.

To make a long story short, I propose standardizing something trivial, along the
lines of this:

```js
//:"./index".IAdd
export const add = (a,b) => a+b;
```
The following sections apply separation of intend and implementation to a 
superset and explain how this inevitably leads to this proposal.

## Separation of intend and implementation.

> TypeScript is used as a starting point, although it is my understanding that 
any statically typed superset of JavaScript can be used without loss of 
generalization.

Unfortunately the very design of TypeScript, promotes the mix of intend with 
implementation, e.g.:

* `./add.ts`

    ```ts
    const add = (a:number,b:number):number => a+b;
    ```

Separation of intend and implementation, e.g.:

* `./publicApi.ts`

    ```ts
    export type IAdd = (a : number,b : number) => number;
    ```
* `./add.ts`

    ```ts
    import type {IAdd} from "./publicApi.ts";

    const add : IAdd = (a,b) => a+b;
    ```

is not enforced and hence many advantages are lost.

<details>
<summary><b>The advantages.</b></summary>

<br>

<details>
<summary>Maintainable public api.</summary>

Since the types are separated from their implementations, it makes sense to 
gather all of the public api types in a single file. This makes it easy to 
maintain the public api since it is not scattered in multiple files.
</details>

<details>
<summary>Less need for <code>.d.ts</code> generators.</summary>

The single file that contains the public api can in fact be a manually 
created `index.d.ts` file, and hence reduce the need for `.d.ts` file 
generation. The files that define the implementations of the public api will
import their corresponding types from `index.d.ts` so that they can conform to 
it.
</details>

<details>
<summary>Less need for documentation generations libraries.</summary>

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

Notice that both the types and the JSDoc descriptions are contained in 
`index.d.ts`, and when you import from `index.ts` you actually see the type and 
the JSDoc description of `index.d.ts`.
</details>
    
<details>
<summary>Flexibility on making the public api readable.</summary>

You can put the most important types in the top of `index.d.ts`, and the least 
important in the bottom. You can define types and IDE collapsible regions for 
the sole purpose of improving public api readability for both the library 
maintainer but also the library consumer. 
</details>

<details>
<summary>Reduced need to bundle declaration files.</summary>

Many times, I find myself trying to bundle a library to an esm `index.js`
file with its associated `index.d.ts` file. From the previous points it can be 
seen that there will be a reduced need for `.d.ts` bundlers. Just make sure that
`index.d.ts` does not depend on the private api.
</details>

<details>
<summary>TypeScript reserves the least possible syntax from JavaScript.
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
<summary>Loose coupling of JavaScript code with the type system.</summary>

Not only the public api, but also the private api can be contained in a single 
file. That means that all the types can be contained in two files. This, 
combined with the fact of minimum syntax reservation makes the migration 
(automated or manual) from one type system to another more easy.
</details>

<details>
<summary>TypeScript maintainers have less work to do.</summary>

A direct result of reserving the least possible syntax. They no longer need to
enable features that mix implementation and indent:

```ts
export const add = (a:number,b:number):number => a+b;
```

</details>

<details>
<summary>The probability for TypeScript to have syntax collisions with 
future JavaScript syntax, gets minimized.</summary>

A direct result of reserving the least possible syntax.
</details>

<details>
<summary>Code that looks familiar to the JavaScript developers.</summary>

A direct result of reserving the least possible syntax.
</details>

***    

</details>

<details>
<summary><b>Common misconeptions.</b></summary>

<br>

@TODO you will have a file for types for each `.js` file
    
<details>
<summary>You can not see the type while writing the implementation.</summary>

You can just hover over the type that annotates the implementation and the IDE will
show its definition. Also the IDE will lint error when the implementation
deviates from its type.

</details>
    
<details>
<summary>Frequent context switching.</summary>

More specifically because the types are in separate files from their implementation, 
that means you have to frequently switch among many files. This is not valid since, 
the IDE will show the type of an implementation by hovering on its annotation. If 
the type references other types, then we have frequent context switching, however 
this does not occur due to separation of intend and implementation, nor it gets 
excageratted by it. In fact when you
separate intend with implementation you can split your private api in two files so 
that at most two files are needed frequent context switchin is minimized.
    
@TODO add example
</details>
    
<details>
<summary>You have to write everything two times.</summary>
    
That is, you have to write the function once for implementation and once more for 
abstraction. This is not valid. I always find myself writing an empty implementation,
and then copying that to define its abstraction. For example, the following empty 
implementation:
    
```ts
export const add = (a,b) => {}
```
    
can be copied and converted to abstraction with minimal effort:

* replace `const` with ` type`
* replace `{}` with ` number`
* add `: number` next to the parameters `a` and `b`
* replace `add` with `IAdd`
    
Finally, lets not forget that there are many cases in which we accept writing extra
code due to maintanability, e.g. strict mode of TypeScript.
</details>

<details>
<summary>You will not know where the types are.</summary>
    
If you know where the implementation of the type is, then you can use the go to
type definition feature of your IDE.
</details>

<details>
<summary>The code will not be readable.</summary>
    
The code will be more readable since it will be closer to JavaScript code due to
separation of intend and implementation leading to minimal syntax reservation. If
you want to see the type of something, then you can just hover over it and the IDE
will shows its type.
</details>

***

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
example something that is already supported by:

<details>
<summary>TypeScript</summary>
 
 ```js
/**@type {import("./index").IAdd}*/
export const add = (a,b) => a+b;
```
</details>

<details>
<summary>Flow</summary>

> Disclaimer: I do not know Flow

```js
/*:: import type {IAdd} from "index";*/
export const add /*: IAdd*/ = (a,b) => a+b;
```
 </details>

Writing type annotations in comments has so many advantages.
<details>
<summary><b>The advantages.</b></summary>

* no `.ts` to `.js` compilation needed
* `.ts` files that contain implementations become redundant
* no need for `tsc` to be a compiler
* no need to wait the compiler
* no need to develop faster compilers
* one less configuration for the build pipeline
* no need to deal with the fragmented ecosystem of compiling `.ts` to `.js`
* no need to depend on extra packages for your code to get executed
* no need to learn new APIs
* less security issues due to depending on less code
* one less source map
* no need to develop source map generators
* code can be pasted in the console and it will execute
* no IoC (Inversion of Control), code executes as it has be written (at least 
during the development stage)
* syntax from TypeScript will never collide with JavaScript syntax
* easier adoption of TypeScript in projects that do no use it
* the code gets even more familiar looking to the JavaScript developer
* adherence to KISS (Keep It Super Simple)
* adherence to SRP (Single Responsibility Principle) (e.g. TypeScript is not 
concerned with transpilation anymore)
* less work for TypeScript maintainers
    
***

</details>

<details>
<summary><b>Common misconceptions.</b></summary>

<br>
    
<details>
<summary>You can not use type parameters when writing types in comments.</summary>
    
Extra syntax reservation inside native comments can enable that:

* `./privateApi.ts`

```js
export type IAdd = <T>(a : T,b : T) => T
```
    
Even without that it still works:
    
</details>
    
<details>
<summary>Types in comments produce verbose code.</summary>

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

It is clear that the no compile method is the most concise.
    
In the end, the comparison is not fair. The compile method has a fixed syntax
while I can choose whatever syntax I want to standardize for the no compile 
method. For example you might have noticed that there are cases where the compile
method is less verbose, e.g.:

* `add.ts` (compile method):

    ```ts
    import type {IAdd} from "./index";
    export const add1 : IAdd = (a,b) => a+b;
    export const add2 : IAdd = (a,b) => a+b;
    export const add3 : IAdd = (a,b) => a+b;
    ```
* `add.js` (no compile method as enabled by TypeScript):

    ```js
    /**@type {import("./index").IAdd}*/
    export const add1 = (a,b) => a+b;
    /**@type {import("./index").IAdd}*/
    export const add2 = (a,b) => a+b;
    /**@type {import("./index").IAdd}*/
    export const add3 = (a,b) => a+b;
    ```

However there is nothing stopping me from standardizing the no compile method to
be less verbose even for such cases, e.g.:
   
* `add.js` (no compile method concise alternative):
    
    ```js
    //::"./index".IAdd
    
    //:IAdd
    export const add1 = (a,b) => a+b;
    //:IAdd
    export const add2 = (a,b) => a+b;
    //:IAdd
    export const add3 = (a,b) => a+b;
    ```

</details>

<details>
<summary>Having to compile is better than using comments, because the majority of the
community has choosen it.</summary>
    
JavaScript is the best programming language because it is the most popular? This is a 
logical fallacy called [argumentum add poppulum](https://en.wikipedia.org/wiki/Argumentum_ad_populum).
The fact that something is widely adopted by the majority is not a proof that it is 
the best solution to a problem. The question remains though:

> Why the majority compiles when comments are objectively better?

If there is a demand for something, the defacto standard becomes the solution that hits the
market first. 
TypeScript became open source in 2012[i](https://en.wikipedia.org/wiki/TypeScript#History).
The no compile method for TypeScript (i.e. improting types from `.ts` files in `.js` files via
JSDoc imports) was possible in 2018\[[ii](https://github.com/microsoft/TypeScript/issues/22160)\]\[[iii](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-9.html#import-types)\]\[[iv](https://www.npmjs.com/package/typescript/v/2.9.1)\].
By that time everyone was compiling `.ts` to `.js`. To make matters even worse the overwhelming
majority of developers, regardless of seniority level, have the wrong assumption that the no 
compile method is inferior to the compile method, while in fact, the opposite is true. All
these have contributed to writing types in comments being a second class cistizen when compared
to the compile method\[[v](https://github.com/microsoft/TypeScript/issues/22160#issuecomment-413029464)\].

> But JSDoc has been a thing since 1999 [v](https://en.wikipedia.org/wiki/JSDoc)
    
JSDoc (without TypeScript support) is a tool for documentation and and not for static typing.
It is way much more verbose and has less features than TypeScript, regarding static typing.
</details>

@TODO code in comments in not readable/ugly
    
***

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
 * TODO dependency injection

## A list of projects that implement the proposal.

This list can be expanded with your project. Just post them in the #1 issue 
of this repository.
 
1. [es-test](TODO) - testing library (work in progress)
1. [@lillallol/outline-pdf](TODO) - add outline pdf (work in progress)
1. [@lillallol/dic](TODO) - dependency injection library (work in progress)
1. [declarative-tree](TODO) - converts trees to string and vice versa, useful for writing declarative tree tests (work in progress)
1. [fn-to-cli](TODO) - creates cli based on typescript types (work in progress)
1. [scrap-it](TODO) - scraps dom element to outlined pdf (work in progress)
1. [m-mutable](TODO) - client side model library (work in progress)
 
 * TODO discontinue ts-doc-gen-md and explain the reason on its README.md by linking to the proposal
 * TODO discontinue md-in-place and explain the reason
 * TODO discontinue @lillallol/outline-pdf-cjs
 * TODO discontinue @lillallol/outline-pdf-data-structure

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
<summary>Proposal for type annotations[<a href="https://github.com/tc39/proposal-type-annotations">4</a>].</summary>

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

According to ecma-international the scope of tc39 is\[[5](https://www.ecma-international.org/technical-committees/tc39/)\]:

>Scope:
>
>Standardization of the general purpose, cross platform, vendor-neutral 
>programming language ECMAScript®. This includes the language syntax, semantics,
>and libraries and complementary technologies that support the language. ...

Static typing is a complementary technology that supports the EcmaScript 
language. Hence the context proposal belongs to the scope of tc39.
