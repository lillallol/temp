# TODO

* make sure the links in this proposal are numbered correctly

# Reducing super sets to complements.

**Champion(s)**: 

* TBD (To Be Determined)

**Author(s)**: 

* @lillallol

**Stage:** -1

## Problem statement.

There is widespread adoption of TypeScript as a JavaScript super set, despite 
being a suboptimal solution to the problem of enabling static type checking in 
JavaScript\[[1](#separation-of-intend-and-implementation)\]\[[2](#concluding-to-the-complement-method)\],
and this unnecessarily complicates the ecosystem.

## Proposal.

Standardize a type system agnostic, battle tested\[[3](#a-list-of-projects-that-implement-the-proposal)\],
minimal syntax reservation, concise comment contract\[[4](#defining-the-comment-contract)\],
that is enough to eventually reduce the super sets to complements. Let the rest 
of the type system to be dealt by third party tools.

## The intuition behind the proposal.

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
reservation, makes the migration (automated or manual) from one type system to 
another, easier.
</details>

<details>
<summary>TypeScript maintainers have less work to do.</summary>

A direct result of reserving the least possible syntax. They no longer need to
enable mix of implementation and indent.

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

#### Addressing possible criticism.

<details>
<summary>expand/collapse</summary>

<br>

<details>
<summary>Frequent context switching.</summary>

More specifically, when you write the implementation of a type you will have to 
frequently switch between the implementation file and the abstraction file, 
because you want to see the type. This is not valid since the IDE will show you 
the type of an implementation by hovering on its annotation. Also the IDE will
highlight the parts of the implementation where they do not conform to the type.

</details>

<details>
<summary>You will not know where the types are.</summary>

More specifically because the files for types can grow large, that will make it 
hard to find the types. This is not valid since if you know where the 
implementation of the type is, then you can use the go to type definition 
feature of your IDE to find it.

</details>

<!-- @TODO this will lead to clutter of the common space -->

***

</details>

### Concluding to the complement method.

If separation of intend and implementation, inevitably leads TypeScript, without 
loss in static typing, to do the minimal possible syntax reservation from 
JavaScript, which is:

* type imports:
    
    ```ts
    import type {IAdd} from "index";
    ```

* type annotating a variable declaration
    
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
* no need to worry whether the compiler will be fast as your project scales
* no need to develop faster compilers
* one less configuration for the build pipeline
* no need to deal with the fragmented ecosystem of compiling `.ts` to `.js`
* no need to depend on extra packages for your code to get executed
* no need to learn new APIs
* less configuration needed for `tsconfig.json`
* less security issues due to depending on less code
* one less source map
* no need for TypeScript to have a source map generator
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
* can be trivially standardized by tc39 due to minimal syntax
* it is type system agnostic
* can be adopted by different type systems without breaking changes
* standardization will have no effect on the runtime
* standardization will create no need to change existing JavaScript parsers
* standardization will not collide with other tc39 proposals
* enforces separation of intend and implementation
* enables simultaneously type checking source code with more than one type
system

***

</details>

#### Addressing possible criticism.

<details>
<summary>expand/collapse</summary>

<br>

<!-- #region There is loss in static typing since you will not be able to use type assertions. -->

<details>
<summary>There is loss in static typing since you will not be able to use type 
assertions.</summary>

Type assertions\[[5](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#type-assertions)\])
are a bad practice since they override the type checker.  It is true that the 
override is safer than `//@ts-ignore`, but that does not change the fact that it
is an override. This:

```ts
const myVariable = myValues as number;
```

can be replaced with this:

```ts
if (typeof myValue !== "number") throw Error();
const myVariable = myValues;
```

leading to safer static type checking. It it the ability to use type assertions
that leads to loss in static typing, not the inability to use them.
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

Here is how to pass a type parameter to `DLLFactory` using the complement 
method:

* `./privateApi.ts`

    ```ts
    import {DLLFactory} from "./index";
    
    // works with typescript 4.7
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
we should question whether this is an intrinsic inability of the complement 
method or a matter of support from the type system.

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

<!-- #region It will produce unreadable and verbose code, that will reduce developer experience. -->

<details>
<summary>It will produce unreadable and verbose code, that will reduce developer
experience.</summary>

Objectively speaking, knowing the type of a concretion makes it easier to read
the code. How can someone know the type of a concretions when writing code in 
JavaScript, i.e. the complement method? Hovering over any implementation, will 
make the IDE to show its type. Do super sets need that help from the IDE? Yes 
they do, because of type aliases.

Given that help from the IDE, it becomes subjective which code base is more 
readable. From my personal experience the more I get exposed to a certain 
code style, the more readable I find it. So if you think that the complement 
method is not readable, I suggest you, to ask yourself the same question after 
some months you have been exposed to it. Eventually you will get used to it and 
readability will not be an issue.

Regarding verbosity, strictly speaking, a super set can always be made less
verbose when compared to a complement, since a complement is forced to use
the already existing comment syntax, while a super set reserves syntax outside 
of existing comment syntax. However, from my experience with the complement and 
super set methods of TypeScript, there is no practical impact in the developer 
experience. In fact when comparing them, it is not actually clear which method 
is less verbose, because in some cases, the complement method is less verbose
while in other cases the super set method is less verbose.

</details>

<!-- #endregion -->

<!-- #region Super sets are better because the community has chosen them -->

<details>
<summary>Super sets are better because the community has chosen them.</summary>

This is a logical fallacy called argumentum add populum\[[8](https://en.wikipedia.org/wiki/Argumentum_ad_populum)\].
The fact that something is widely adopted by the majority is not a proof that it
is the best solution to the problem.

To add to that, if we are really interested which method is preferred by the 
community, then we have to ask those people that have tried both the complement
and the super set method, and not have misconceptions about them. Any 
statistical inference based on something different from that is garbage in, 
garbage out\[[9](https://en.wikipedia.org/wiki/Garbage_in,_garbage_out)\].

The question remains though:

> Why the majority is using super sets and not the complement method?

To attempt an answer to such a question we have to look at the whole topic from 
a historical perspective:

* 1999 JSDoc\[[10](https://en.wikipedia.org/wiki/JSDoc)\]
* 2009 Closure Compiler\[[11](https://en.wikipedia.org/wiki/Google_Closure_Tools)\]
* 2012 TypeScript\[[12](https://en.wikipedia.org/wiki/TypeScript#History)\]
* 2014 Flow\[[13](https://github.com/facebook/flow/tree/49820636495b6e36752079117b9e7c34e5c4fc7b)\]
* 2018 TypeScript supports `/**@type {import("./some/path").IMyType}*/`\[[14](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-9.html#import-types)\]
* 2022 TypeScript supports `type IMyFn = typeof fn<IMyType>`\[[15](https://devblogs.microsoft.com/typescript/announcing-typescript-4-7/#instantiation-expressions)\]
* 2022 TypeScript feature request for extracting type parameters\[[6](https://github.com/microsoft/TypeScript/issues/49112)\]

From the timeline we can see that JSDoc-like static typing (JSDoc, Closure 
Compiler) hit the market before super sets (TypeScript, Flow). Usually whoever 
hits the market first is the one who becomes the de facto standard. However this
is not what happened here. It is my understanding that the following factors
contributed to that:

* JSDoc-like syntax was made to be documentation friendly, rather than static 
typing friendly.
* JSDoc-like static type checkers, lack considerably in support for static 
typing features, when compared to the super sets\[[16](https://github.com/google/closure-compiler/issues/890)\]\[[17](https://github.com/google/closure-compiler/issues/490)\].

Now we have to ask why TypeScript became way much more popular than Flow. I 
think that is solely due to TypeScript hitting the market first.

Now we have to ask why the overwhelming majority is using TypeScript as a super
set and not as a complement. As far as I understand it is because of the 
following reasons:

* From the timeline we can see that TypeScript as a superset hit the market 
first.
* Looking at the TypeScript handbook, the complement method does not exist, 
hence nobody knows about it.
* From the timeline we can see that features that make the complement method
less hacky have been recently adopted, while others are still missing.

> Why TypeScript creators chose super set instead of complement?

That is a question that has to be answered by the creators of TypeScript.

</details>

<!-- #endregion -->

***

</details>

## Why not native super sets?

Here are the drawbacks of standardizing super sets into tc39, that do no exist
with the complement method:

* There will be breaking changes to existing super sets (e.g. generics).
* There will be irreversible syntax reservation outside of native comments.
* There is high chance that they collide with existing tc39 proposals.
* Every JavaScript parser will have to be updated.
* It is highly likely that the updated JavaScript parsers will be slower than 
the older parsers.
* They will take many years for them to be standardized because they reserve too 
much syntax.
* They might require an opt in like `"use types"`.

Take into account that all these drawbacks apply, regardless of whether the 
native super sets will have types that have runtime semantics.

## Addressing possible proposal criticism.

<!-- #region Nobody is going to adhere to the complement method. -->

<details>
<summary>Nobody is going to adhere to the complement method.</summary>

People might claim that regardless what tc39 manages to standardize regarding 
static typing, nobody is going to adhere to it unless it is what the majority of
the community has chosen, i.e. TypeScript. However:

* It is a TypeScript design goal to\[[18](https://github.com/Microsoft/TypeScript/wiki/TypeScript-Design-Goals)\]:

  > Align with current and future ECMAScript proposals.

* This proposal proves that there is no need for future proposal creators to\[[19](https://github.com/tc39/notes/blob/main/meetings/2022-03/mar-29.md#types-as-comments-for-stage-1)\]:

  > steer away from syntax because typescript uses it.

  Inevitably proposals that have nothing to do with static typing, but introduce
  breaking changes to super sets, will be adopted. This will cause people to 
  move to the complement method.

* Acceptance of this proposal will popularize the complement method and expose 
the drawbacks of super sets.

* People can still use TypeScript with this proposal.

In the long run super sets will have no other choice than be reduced to 
complements of JavaScript.

</details>

<!-- #endregion -->

<!-- #region I see defining a syntax for types without semantics to be actively harmful. -->

<details>
<summary>I see defining a syntax for types without semantics to be actively
harmful.</summary>

This is an exact quote from tc39 meeting notes for the proposal: types as 
comments\[[19](https://github.com/tc39/notes/blob/main/meetings/2022-03/mar-29.md#types-as-comments-for-stage-1)\]\[[20](https://github.com/tc39/notes/blob/main/meetings/2022-03/mar-31.md#types-as-comments-continuation)\]. It is my 
understanding that by types with semantics it is meant types that exist in the
runtime. 

I think that this argument applies to my proposal as well, so I have to address 
it.

Are types without semantics via the TypeScript complement method, more harmful 
than the current ecosystem (i.e. TypeScript as a super set)? I hope that anyone 
who have read the context proposal is convinced that the answer is no.

It is my understanding that standardizing a semantically typed JavaScript super
set will be a very controversial topic that will likely never reach adoption, 
and for the case it does, it will be after many years. What are we going to do 
up until then?

Finally it is highly likely that such a semantically typed JavaScript super set
will be slower than untyped JavaScript. This means that even then, there will 
still be people that use untyped JavaScript.

So my conclusion is that it would be harmful to the ecosystem not to accept the 
current proposal.

@TODO mention that minimal syntax reservation is the only pragmatic way to 
enable static typing and if needed pave the way for strong typing.

</details>

<!-- #endregion -->

<!-- #region The scope of tc39 does not include static typing. -->

<details>
<summary>The scope of tc39 does not include static typing.</summary>

According to ecma-international the scope of tc39 is\[[21](https://www.ecma-international.org/technical-committees/tc39/)\]:

>Scope:
>
>Standardization of the general purpose, cross platform, vendor-neutral 
>programming language ECMAScript®. This includes the language syntax, semantics,
>and libraries and complementary technologies that support the language.

Static typing is a complementary technology that supports the EcmaScript 
language. Hence the context proposal belongs to the scope of tc39.

</details>

<!-- #endregion -->

<!-- #region Proposals at stage 1 are supposed to be about problem exploration, however this proposal has come up already with a solution. -->

<details>
<summary>Proposals at stage 1 are supposed to be about problem exploration,
however this proposal has come up already with a solution.</summary>

The problem statement is that super sets are a suboptimal solution to the 
problem of adding static type checking in JavaScript. How can someone prove that
a solution to a problem is suboptimal? This is only done by showing that there 
is a better solution.

If there is a better solution than the one I have proposed then it should be 
the proposed solution.

</details>

<!-- #endregion -->

## A list of projects that implement the proposal.
 
1. [es-test](TODO) - testing library (work in progress)
1. [complement-lint](TODO) - a linter that ensures you are using TypeScript as a complement (work in progress)
1. [@lillallol/outline-pdf](TODO) - add outline pdf (work in progress)
1. [@lillallol/dic](TODO) - dependency injection library (work in progress)
1. [declarative-tree](TODO) - converts trees to string and vice versa, useful for writing declarative tree tests (work in progress)
1. [md-in-place](TODO) - 
1. [scrap-it](TODO) - scraps dom element to outlined pdf (work in progress)
1. [m-mutable](TODO) - client side model library (work in progress)

TODO discontinue:

* fn-to-cli
* ts-doc-gen-md 

and explain the reason on its README.md
 
* TODO mention know issues with typescript in the list of projects that implement the
proposal
    * There are feature request to enable type checking `.js` based on their 
    corresponding `.d.ts` files [link](https://github.com/microsoft/TypeScript/issues/48911#issuecomment-1115905062).
    That feature is only needed for the entry point of your node module.
    * Find the issue for no explicit any in JS:
        * this : `let toReturn = []` does not error because TS treats as an explicit 
        any.
        * this `const cache = new Map();` does not lint error
    * there this issue with contextual typing in declarative tree  with `tag<string[] | string>`
    https://www.typescriptlang.org/docs/handbook/type-inference.html#contextual-typing
    * function bug compared to arrow when implementation has less parameters
    * the type inference for `observable({prop : true})` works differently for `.ts` and `.js`, at the latter the type is `{prop : true}`, while at the former it is `{prop : boolean}`.
    * https://github.com/microsoft/TypeScript/issues/49039 Irregular behavior of generic non arrow function that is typed via JSDoc import. (both wrong type parameter restriction and also the optional parameter)
    * https://github.com/microsoft/TypeScript/issues/49115 JSDoc tag and import autocompletion for concretions

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

    There is a common misconception that there is an intrinsic need for an extra
    pair of parenthesis\[[22](https://github.com/microsoft/TypeScript/issues/18212#issuecomment-328733868)\]. 

1. The path is relative.

1. The path has to be without filename extension. For example the path: 
`./src/index.d.ts` has to be written as: `./src/index`. This is done so the
 comment is type system agnostic.

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

