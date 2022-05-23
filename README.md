# Reducing super sets to complements.

**Champion(s)**: 

* TBD (To Be Determined)

**Author(s)**: 

* @lillallol

**Stage:** -1

## Problem statement

There is widespread adoption of statically typed JavaScript super sets, despite 
being inferior to complements\[[1](#separation-of-intend-and-implementation)\]\[[2](#concluding-to-complement)\].

## Proposal

Standardize a type system agnostic, battle tested\[[3](#a-list-of-projects-that-implement-the-proposal)\],
 minimal syntax reservation, concise comment contract\[[4](#defining-the-comment-contract)\],
that is enough to reduce the super sets to complements.

## The intuition behind the proposal

### Separation of intend and implementation.

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

is not enforced.

#### Advantages.

<details>
<summary>expand/collapse</summary>

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
derive their corresponding types from `index.d.ts` so that they can conform to 
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
    ```
* `./privateApi.ts`
    ```ts
    import {add} from "index";

    export type IAdd = typeof add;
    ```
* `./add.ts`
    ```ts
    import type {IAdd} from "./privateApi";

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
file, or at least a few files. This, combined with the fact of minimum syntax 
reservation makes the migration (automated or manual) from one type system to 
another more easy.
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

#### Misconceptions.

<details>
<summary>expand/collapse</summary>

<br>

<details>
<summary>Frequent context switching because you can not see the type while 
defining the implementation.</summary>

More specifically because the types are in separate files from their 
implementation, that means you have to frequently switch between the 
implementation file and the concretion file. This is not valid since the IDE 
will show the type of an implementation by hovering on its annotation. 

</details>

<details>
<summary>You have to write everything two times.</summary>
    
That is, you have to write the function once for implementation and once more 
for abstraction. This is not valid. I always find myself writing an empty 
implementation, and then copying that to define its abstraction. For example, 
the following empty implementation:
    
```ts
export const add = (a,b) => {}
```
    
can be copied and converted to abstraction with minimal effort:

* replace `const` with ` type`
* replace `{}` with ` number`
* add `: number` next to the parameters `a` and `b`
* replace `add` with `IAdd`
    
Finally, lets not forget that there are many cases in which we accept writing 
extra code due to maintainability, e.g. strict mode of TypeScript.
</details>

<details>
<summary>You will not know where the types are.</summary>
    
If you know where the implementation of the type is, then you can use the go to
type definition feature of your IDE.
</details>

***

</details>

### Concluding to the complement method.

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

***

</details>

<details>
<summary>Flow</summary>

> Disclaimer: I do not know Flow

```js
/*:: import type {IAdd} from "index";*/
export const add /*: IAdd*/ = (a,b) => a+b;
```

***

</details>

or a less verbose alternative:

```js
//:"./index".IAdd
export const add = (a,b) => a+b;
```

#### Advantages

<details>
<summary>expand/collapse</summary>

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
* formatters have a simpler job to do
* code can be pasted in the console and it will execute
* no IoC (Inversion of Control), code executes as it has be written (at least 
during the development stage)
* syntax from TypeScript will never collide with JavaScript syntax
* easier adoption of TypeScript in projects that do no use it
* easier adoption of TypeScript by beginners
* the code gets even more familiar looking to the JavaScript developer
* adherence to KISS (Keep It Super Simple)
* adherence to SRP (Single Responsibility Principle) (e.g. TypeScript is not 
concerned with transpilation anymore)
* less work for TypeScript maintainers
* can be easily standardized by tc39 due to minimal syntax
* can be type system agnostic
* can be adopted by different type systems without breaking changes
* has no effect on the runtime
* no need to change the browser JavaScript parsers
* enforces separation of intend and implementation

***

</details>

#### Common misconceptions.

<details>
<summary>expand/collapse</summary>

<br>

<!-- #region as assertions -->

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

<!-- #endregion -->

<!-- #region You will not be able to use type parameters. -->

<details>
<summary>You will not be able to use type parameters.</summary>

Consider using a library that was written in TypeScript with the following 
public api:

* `./index.d.ts`:
    
    ```ts
    /**
     * @description 
     *`DLL` stands for Double Linked List.
    */
    export declare function DLLFactory<T>() : IDLL<T>;
    
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

Here is how can someone pass a type parameter to `DLLFactory` using the 
complement method:

* `./privateApi.ts`

    ```ts
    import {DLLFactory} from "./index";
    
    // tested with typescript 4.8.0-dev.20220515
    export type IMyDllFactory = typeof DLLFactory<number>;
    ```

* `./myDLL.js`

    ```js
    import {DLLFactory} from "./index.js";
    
    /**
     * @type {import("./privateApi").IMyDllFactory}
     * Hover over `myDLLFactory` to see that the type parameter is `number`.
     */
    const myDLLFactory = DLLFactory;
    ```

Something that should be noted here, is that this is not possible with older 
versions of TypeScript. Given that TypeScript is mostly used as a super set, it 
makes sense for features that mainly benefit the complement method to not have 
been requested, e.g. type parameters extraction\[[6](https://github.com/microsoft/TypeScript/issues/49112)\].
So whenever we are faced in a situation that challenges the complement method,
we should question whether this an intrinsic inability of the complement method 
or a matter of support from the type system.

</details>

<!-- #endregion -->

<!-- #region You will not be able to use enum. -->

<details>
<summary>You will not be able to use <code>enum</code>.</summary>

This is not an intrinsic inability of the complement method, since\[[7](https://www.typescriptlang.org/docs/handbook/enums.html)\]:
    
>Enums are one of the few features TypeScript has which is not a type-level
>extension of JavaScript.

So it is both a matter of support from the type system but also EcmaScript.
</details>

<!-- #endregion -->

<!-- #region It will produce verbose code. -->

<details>
<summary>It will produce verbose code.</summary>

Consider the the following public api:

* `index.d.ts`

    ```ts
    export declare const add : (a:number,b:number) => number;

    export type IAdd = typeof add;
    ```

Lets compare verbosity between the complement method and the super set method:

* `add.ts` (super set method):

    ```ts
    import type {IAdd} from "./index";
    export const add : IAdd = (a,b) => a+b;
    ```

* `add.js` (complement method as enabled by TypeScript):

    ```js
    /**@type {import("./index").IAdd}*/
    export const add = (a,b) => a+b;
    ```

It is clear that the complement method is the most concise without actually 
trying\[[12](https://github.com/microsoft/TypeScript/issues/22160#issuecomment-413029464)\].
    
In the end, the comparison is not fair. The super set method has a fixed syntax
while I can choose whatever syntax I want to standardize for the complement
method. For example you might have noticed that there are cases where the 
super set method is less verbose, e.g.:

* `add.ts` (super set method):

    ```ts
    import type {IAdd} from "./index";
    export const add1 : IAdd = (a,b) => a+b;
    export const add2 : IAdd = (a,b) => a+b;
    export const add3 : IAdd = (a,b) => a+b;
    ```
* `add.js` (complement method as enabled by TypeScript):

    ```js
    /**@type {import("./index").IAdd}*/
    export const add1 = (a,b) => a+b;
    /**@type {import("./index").IAdd}*/
    export const add2 = (a,b) => a+b;
    /**@type {import("./index").IAdd}*/
    export const add3 = (a,b) => a+b;
    ```

However there is nothing stopping me from standardizing the complement method to
be less verbose even for such cases, e.g.:
   
* `add.js` (complement method concise alternative):
    
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

<!-- #endregion -->

<!-- #region Super sets are better because the community has chosen them -->

<details>
<summary>Super sets are better because the community has chosen them.</summary>

This is a logical fallacy called argumentum add populum\[[8](https://en.wikipedia.org/wiki/Argumentum_ad_populum)\].
The fact that something is widely adopted by the majority is not a proof that it
is the best solution to the problem. 

The question remains though:

> Why the majority prefers super sets and not the complement method?

Because super sets hit the "market" first and they were backed by big 
corporations. TypeScript as a super set became open source in 2012\[[9](https://en.wikipedia.org/wiki/TypeScript#History)\].
The complement method for TypeScript was made possible in 2018\[[10](https://github.com/microsoft/TypeScript/issues/22160)\]\[[11](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-9.html#import-types)\]\[[12](https://www.npmjs.com/package/typescript/v/2.9.1)\],
as a side effect. By that time, super sets were the norm. To make matters even 
worse the overwhelming majority of developers, regardless of seniority level, 
**if** they are aware of what I describe as complement method, have the wrong 
assumption that it is inferior to the super sets. 

</details>

<!-- #endregion -->

***

</details>

## Proposal reception.

There is this common misconception that regardless what tc39 manages to
standardize regarding static typing, nobody is going to adhere to it unless it 
is what the majority of the community has chosen, i.e. TypeScript. However:

* It is a TypeScript design goal to\[[13](https://github.com/Microsoft/TypeScript/wiki/TypeScript-Design-Goals)\]:

  > Align with current and future ECMAScript proposals.

* Acceptance of this proposal will prevent future proposal creators to\[[14](https://github.com/tc39/notes/blob/main/meetings/2022-03/mar-29.md#types-as-comments-for-stage-1)\]:

  > steer away from syntax because typescript uses it.

  so inevitably proposals that have nothing to do with static typing, but 
  introduce breaking changes to super sets, will be adopted.

* Acceptance of this proposal will popularize complements and expose the 
drawbacks of super sets.

In the long run super sets will have no other choice than be reduced to 
complements of JavaScript.

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

## Defining the comment contract.

This section is tentative.

<details>
<summary>A comment for type imports that will also define the type for variable 
declarations.</summary>

Example for:

* statement:

    ```js
    //:"./path/to/file".exportedTypeName
    export const add = (a,b) => a+b;
    ```
    
* expression:

    ```js
    ( /*:"path/to/file".exportedTypeName*/ [
        { a : 1 , b : "2" },
        { a : 11, b : "22"}
    ]).forEach(({a,b}) => {
        // do some stuff
    })
    ```

    There is a common misconception that this needs an extra pair of parenthesis\[[15](https://github.com/microsoft/TypeScript/issues/18212#issuecomment-328733868)\]. 

1. The path can be absolute or relative.

1. To be a type system agnostic comment, the path has to be without filename
extension. For example the path: `./src/index.d.ts` has to be written as: 
`./src/index`.

</details>

<details>
<summary>A comment that will inform the type checker that a mistake is expected.
</summary>

Example for:

* statement:

    ```js
    test(add.name,() => {
        //:type-check-expect-error
        expect(() => add("1",1)).toThrow();
    })
    ```

* expression:

    ```js
    test(add.name,() => {
        expect(() => {
            add( /*:type-check-expect-error*/ "1" , 1 )
        }).toThrow();
    })
    ```

</details>

## How this proposal compare to other proposals.

The context proposal does not collide with other tc39 proposals, given that they
do not "reserve syntax" in native comments.

## The scope of tc39 includes static typing.

According to ecma-international the scope of tc39 is\[[16](https://www.ecma-international.org/technical-committees/tc39/)\]:

>Scope:
>
>Standardization of the general purpose, cross platform, vendor-neutral 
>programming language ECMAScript®. This includes the language syntax, semantics,
>and libraries and complementary technologies that support the language. ...

Static typing is a complementary technology that supports the EcmaScript 
language. Hence the context proposal belongs to the scope of tc39.
