# Reducing super sets to complements.

**Champion(s)**: 

* TBD (To Be Determined)

**Author(s)**: 

* @lillallol

**Stage**: -1

## The gist

Statically typed super sets of EcmaScript are inferior to complements\[[link](#advantages)\]\[[link](#advantages-1)\].
The ecosystem is dominated by super sets because it has never been presented
equally with a complement. Therefore, tc39 must pave the way for reducing super 
sets to complements. For this to be done, add in the EcmaScript specification, 
that nothing more than the following comments \[[link](#defining-the-comments-to-be-used-by-complements)\]:

```js
/**@type {import("./path/to/types/file.js").IMyType}*/
//@ts-expect-error
//@ts-nocheck
```

are to be used in `.js` files, by complements. Let the rest of the type system 
(only abstractions, not concretions) be defined by third party type checkers 
outside of `.js` files, that will play no role in execution of EcmaScript.(defining a type system is out of scope for this proposal).

## The intuition behind the proposal.

While trying to create documentation for the public API of my TypeScript 
projects, I understood that this can be done without the need of documentation 
generation libraries, by simply separating intend with implementation. That 
separation lead naturally to the usage of TypeScript as a complement.

### Separation of intent and implementation.

Unfortunately the very design of TypeScript as a super set, promotes the mix of 
intent with implementation, e.g.:

* `./add.ts`

    ```ts
    const add = (a:number,b:number):number => a+b;
    ```

Separation of intent and implementation, e.g.:

* `./publicApi.ts`

    ```ts
    export type IAdd = (a : number,b : number) => number;
    ```
* `./add.ts`

    ```ts
    import type {IAdd} from "./publicApi";

    const add : IAdd = (a,b) => a+b;
    ```

is not enforced.

#### Advantages.

<details>
<summary>expand/collapse</summary>

<br>

<details>
<summary>Maintainable public API.</summary>

Since the types are separated from their implementations, it makes sense to 
gather all of the public API types in a single file. This makes it easy to 
maintain the public API since it is not scattered in multiple files.
</details>

<details>
<summary>Less need for <code>.d.ts</code> generators.</summary>

The single file that contains the public API can in fact be a manually 
created `index.d.ts` file, and hence reduce the need for `.d.ts` files 
generation. The files that define the implementations of the public API will
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
`index.d.ts`, that means that imports from `index` show (or can be made to show) 
both the type and the JSDoc description of `index.d.ts` in the IDE documentation
popup window, when you hover over the imported variable.
</details>

<details>
<summary>Flexibility on making the public API readable.</summary>

You can put the most important types in the top of `index.d.ts`, and the least 
important in the bottom. You can manually edit the format, define types and IDE
collapsible regions for the sole purpose of improving public API readability for
both the library maintainer but also the library consumer. Finally `index.d.ts` 
opens in your IDE, with the font, syntax highlighting, theme and keyboard 
shortcuts your are familiar with. It is not trivial to do these with 
documentation generation libraries (e.g. typedoc).
</details>

<details>
<summary>Reduced need to bundle declaration files.</summary>

Many times, I find myself trying to bundle a library to an esm `index.js` file 
with its associated `index.d.ts` file. From the previous points it can be seen 
that there will be a reduced need for `.d.ts` bundlers. Just make sure that 
`index.d.ts` is indeed acting like a public API, i.e. it does not depend on the
private API and hence imports nothing from it.
</details>

<details>
<summary>TypeScript reserves the least possible syntax from EcmaScript.
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
<summary>Loose coupling of EcmaScript code with the type system.</summary>

Not only the public API, but also the private API can be contained in a single 
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
future EcmaScript syntax, gets minimized.</summary>

A direct result of reserving the least possible syntax.

</details>

<details>
<summary>Code that looks familiar to the EcmaScript developers.</summary>

A direct result of reserving the least possible syntax.

</details>

<details>
<summary>Formatters, syntax highlighters, etc, have a simpler job to do.
</summary>

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
highlight the parts of the implementation that do not conform to the type.

</details>

<details>
<summary>You will not know where the types are.</summary>

More specifically because the files for types can grow large, that will make it 
hard to find the types. This is not valid since if you know where the 
implementation of the type is, then you can use the go to type definition 
feature of your IDE to find it.

</details>

<details> 
<summary>This will lead to clutter of the common space.</summary>

Multiple `.ts` files can be used to segregate the common space. Here is a 
possible example:

* `index.d.ts` is used to define the public API
* `privateApi.ts` is used to define the private API
* `types.ts` is used to define types shared in `privateApi.ts`
* `trivialApi.ts` is used to define less complex types (e.g. `string[]`,`number`
etc.)
* `testApi.ts` is used to define types that are used only in test files
* `typeFunctions.ts` is used to define type functions
* `dicApi.ts` is used to define the types of the dependency graph of the DIC

</details>

<details>
<summary>There will be no reduced need for bundling declaration files since 
<code>index.d.ts</code> will depend on types from other files.</summary>

If the public API depends on te private API, then reverse the dependency, and
make the private API depend on the public API.

</details>

***

</details>

### Concluding to complements.

If separation of intent and implementation, inevitably leads TypeScript, without 
loss in static typing, to do the minimal possible syntax reservation from 
EcmaScript, which is:

* type imports:
    
    ```ts
    import type {IAdd} from "index";
    ```

* type annotating a variable declaration
    
    ```ts
    export const add : IAdd = (a,b) => a+b;
    ```

then why not use a comment syntax in EcmaScript that enables them both? For 
example something that is already supported by:

<details>
<summary>TypeScript</summary>
 
 ```js
/**@type {import("./index.js").IAdd}*/
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
//:"./index.js".IAdd
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
* syntax from TypeScript will never collide with EcmaScript syntax
* easier adoption of TypeScript in projects that do no use it
* easier adoption of TypeScript by beginners
* the code gets even more familiar looking to the EcmaScript developer
* adherence to KISS (Keep It Super Simple)
* adherence to SRP (Single Responsibility Principle) (e.g. TypeScript is not 
concerned with transpilation anymore)
* it is type system agnostic
* can be easily adopted by different static type checkers
* standardization will have no effect on the runtime
* standardization will create no need to change existing EcmaScript parsers
* standardization will not collide with other tc39 proposals
* standardization will be trivial
* enforces separation of intent and implementation
* enables simultaneous type checking source code with more than one type
system

***

</details>

#### Addressing possible criticism.

<details>
<summary>expand/collapse</summary>

<br>

<!-- #region You will not be able to use `as` and `!.`. -->

<details>
<summary>You will not be able to use <code>as</code> and <code>!.</code>.</summary>

Using `as`\[[link](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#type-assertions)\]
and `!.`\[[link](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#non-null-assertion-operator-postfix-)\]
is a bad practice since they override the type checker. It is true that the 
override is safer than `//@ts-ignore`, but that does not change the fact that it
is an override. These:

```ts
const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement;
function liveDangerously(x?: number | null) {
    // No error
    console.log(x!.toFixed());
}
```

can be replaced with these

```ts
const myCanvas = document.getElementById("main_canvas");
if (!(myCanvas instanceof HTMLCanvasElement)) throw Error();
function liveDangerously(x?: number | null) {
    if (x === null) throw Error();
    console.log(x!.toFixed());
}
```

leading to safer static type checking. It is the ability to use `as` and `!.` 
that leads to loss in static typing, not the inability to use them.
</details>

<!-- #endregion -->

<!-- #region You will not be able to use `as const` -->

<details>
<summary>You will not be able to use <code>as const</code>.</summary>

Since TypeScript `5.0`, instead of using const type assertion you can do 
something like:

* `./privateApi`

    ```ts
    export type IAsConstArray = <const T extends readonly unknown[]>(array : T) => T;
    ```

* `./index.js`

    ```js
    /**@type {import("./privateApi.js").IAsConstArray}*/
    const asConstArray = (array) => array;
    
    const myData = asConstArray([1,2]);
    ```

</details>

<!-- #endregion -->

<!-- #region You will not be able to provide type parameters to generic function calls. -->

<details>
<summary>You will not be able to provide type parameters to generic function 
calls.</summary>

Consider using a library with the following public API:

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
     * @type {import("./privateApi.js").IMyDllFactory}
     * Hover over `myDLLFactory` to see that the type parameter is `number`.
     */
    const myDLLFactory = DLLFactory;
    ```

</details>

<!-- #endregion -->

<!-- #region You will not be able to use type parameters inside the definition of generic functions. -->

<details>
<summary>You will not be able to use type parameters inside the definition of 
generic functions.</summary>

Here is the implementation of the `chunk` function of the lodash library, using
TypeScript as a complement:

* `./index.d.ts`:

  ```ts
  export declare const chunk : <T>(array:T[],length:number) => T[][];
  ```

* `./privateApi.ts`:

  ```ts
  import {chunk} from "./index";
  
  export type IChunk = typeof chunk;
  ```

* `./index.js`

  ```ts
  /**@type {import("./privateApi.js").IChunk}*/
  export const chunk = (array,length) => {
      const toReturn = [];
      for (let i = 0; i < array.length ; i += length) {
          toReturn.push(
              array.slice(
                  i,
                  i + length
              )
          )
      }
      return toReturn;
  }
  ```

As of writing this, TypeScript is at version 5.1, and if it used as a complement
it is impossible to type `toReturn` of the last code snippet. Given that 
TypeScript is mostly used as a super set, it makes sense for features that 
mainly benefit the complement to not have been requested, e.g. type parameters 
extraction. This is what is needed to statically type `toReturn` in the last 
code snippet. What we are dealing here, is not a design limitation of 
complements, its a matter of support from the type system\[[link](https://github.com/microsoft/TypeScript/issues/49112#issuecomment-1130379058)\].
For the time being, here are some hacks to get full static type checking:

* `./index.d.ts`:

  ```ts
  export declare const chunk : <T>(
      array     : T[],
      length    : number,
      toReturn ?: T[][],
  ) => T[][];
  ```

* `./privateApi.ts`:

  ```ts
  import {chunk} from "./index";
  
  export type IChunk = typeof chunk;
  ```

* `./index.js`

  ```ts
  /**@type {import("./privateApi.js").IChunk}*/
  export const chunk = (array,length,toReturn) => {
      for (let i = 0; i < array.length ; i += length) {
          toReturn.push(
              array.slice(
                  i,
                  i + length
              )
          )
      }
      return toReturn;
  }
  ```

If you do not want to change the public api, here is another hack:

* `./index.d.ts`:

  ```ts
  export declare const chunk : <T>(
      array     : T[],
      length    : number,
  ) => T[][];
  ```

* `./privateApi.ts`:

  ```ts
  import {chunk} from "./index";
  
  export type IChunk = typeof chunk;
  export type _IChunk = <T>(
      array     : T[],
      length    : number,
      
  ) => T[][];
  ```

* `./index.js`

  ```ts
  /**@type {import("./privateApi.js")._IChunk}*/
  const _chunk = (array,length,toReturn) => {
      for (let i = 0; i < array.length ; i += length) {
          toReturn.push(
              array.slice(
                  i,
                  i + length
              )
          )
      }
      return toReturn;
  }
  
  /**@type {import("./privateApi.js").IChunk}*/
  export const chunk = (array,length) => _chunk(array,length)
  ```

</details>

<!-- #endregion -->

<!-- #region You will not be able to use enum. -->

<details>
<summary>You will not be able to use <code>enum</code>.</summary>

This is not an intrinsic inability of complements, since\[[link](https://www.typescriptlang.org/docs/handbook/enums.html)\]:

>Enums are one of the few features TypeScript has which is not a type-level
>extension of EcmaScript.

So it is both a matter of support from the type system but also EcmaScript.

</details>

<!-- #endregion -->

<!-- #region It will produce unreadable and verbose code, that will reduce developer experience. -->

<details>
<summary>It will produce unreadable and verbose code, that will reduce developer
experience.</summary>

Objectively speaking, knowing the type of a concretion makes it easier to read
the code. How can someone know the type of a concretions when using complements?
Hovering over any implementation, will make the IDE to show its type. Do super 
sets need that help from the IDE? Yes they do, because of type aliases.

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
is less verbose, because in some cases, the complement is less verbose while in
other cases the super set is less verbose.

</details>

<!-- #endregion -->

<!-- #region Super sets are better because the community has chosen them -->

<details>
<summary>Super sets are better because the community has chosen them.</summary>

This is a logical fallacy called argumentum ad populum\[[link](https://en.wikipedia.org/wiki/Argumentum_ad_populum)\].
The fact, that something has be chosen by the majority, does not prove it is the
best choice.

If we want to know which method is preferred by the community, then we have to 
ask people, that have tried both complement and super set methods, and have no 
misconceptions about them. Any statistical inference based on something 
different from that, is intrinsically biased.

To understand why TypeScript as a superset is the defacto standard for static
type checking EcmaScript, we have to look at the topic from a historical 
perspective:

* 1999 JSDoc\[[link](https://en.wikipedia.org/wiki/JSDoc)\]
* 2009 Closure Compiler\[[link](https://en.wikipedia.org/wiki/Google_Closure_Tools)\]
* 2012 TypeScript\[[link](https://en.wikipedia.org/wiki/TypeScript#History)\]
* 2014 Flow\[[link](https://github.com/facebook/flow/tree/49820636495b6e36752079117b9e7c34e5c4fc7b)\]
* 2018 TypeScript supports `/**@type {import("./some/path.js").IMyType}*/`\[[link](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-9.html#import-types)\]
* 2022 TypeScript supports `type IMyFn = typeof fn<IMyType>`\[[link](https://devblogs.microsoft.com/typescript/announcing-typescript-4-7/#instantiation-expressions)\]
* 2022 TypeScript feature request for extracting type parameters\[[link](https://github.com/microsoft/TypeScript/issues/49112)\]
* 2023 TypeScript supports `const` generics\[[link](https://devblogs.microsoft.com/typescript/announcing-typescript-5-0/#const-type-parameters)\]

> Why people use TypeScript instead of plain JSDoc?

Regarding static typing, JSDoc syntax is verbose when compared to TypeScript. 
This should not surprise us since JSDoc syntax was made to favor documentation, 
while TypeScript syntax was made to favor static typing. Finally JSDoc lacks in 
static typing support when compared to TypeScript.

> Why people use TypeScript instead of Google's closure compiler?

Same arguments that were used about JSDoc apply here as well.

> Why people use TypeScript instead of Flow?

Typescript "hit the market" first when compared to Flow.

> Why people use TypeScript as a super set and not as a complement?

That is because TypeScript as a complement has been possible only very recently.
To add to that, statically typed complements have never been promoted (a look
at the TypeScript handbook will convince you).

> Why the TypeScript creators decide to go for super set instead of complement?

I do not know. The TypeScript creators have to answer that.

</details>

<!-- #endregion -->

***

</details>

## Super set vs complement proposal.

Here are the drawbacks of standardizing super sets into tc39:

* There will be breaking changes to existing super sets.
* There will be irreversible syntax reservation outside of native comments.
* There might be collisions with existing tc39 proposals.
* Every EcmaScript parser, will have to be updated, which means that:
    * minifiers
    * bundlers
    * source maps
    * browsers
    
  will also have to be updated.
* It is highly likely that the updated EcmaScript parsers will be slower than 
the older parsers.
* They will take many years to be standardized because they reserve too 
much syntax.
* They might require an opt in like `"use types"`\[[link](https://github.com/tc39/notes/blob/main/meetings/2022-03/mar-31.md#types-as-comments-continuation)\], hence further fragmenting EcmaScript.

Take into account that all these drawbacks apply, regardless of whether the 
proposed super set will have types that have runtime semantics.

These drawbacks do not exist with a proposal about complements.

## Addressing possible proposal criticism.

<!-- #region Nobody is going to adhere to complements. -->

<details>
<summary>Nobody is going to adhere to complements.</summary>

People might claim that, regardless what tc39 manages to standardize regarding 
static typing, nobody is going to adhere to it unless it is what the majority of
the community has chosen, i.e. TypeScript. However:

* It is TypeScript's design goal to\[[link](https://github.com/Microsoft/TypeScript/wiki/TypeScript-Design-Goals)\]:

  > Align with current and future ECMAScript proposals.

* This proposal proves that there is no need for future proposal creators to:

  > steer away from syntax because typescript uses it\[[link](https://github.com/tc39/notes/blob/main/meetings/2022-03/mar-29.md#types-as-comments-for-stage-1)\].

  Inevitably proposals that have nothing to do with static typing, but introduce
  breaking changes to super sets, will be adopted. This will cause people to 
  move to complements.

* Acceptance/discussion of this proposal, will aid in popularizing complements 
and expose the drawbacks of super sets.

* People can still use TypeScript with this proposal.

* People that have created influential projects start to gradually conclude to 
a looser form of complements \[[link](https://github.com/sveltejs/svelte/pull/8569)\].

In the long run super sets will have no other choice than be reduced to 
complements of EcmaScript.
</details>

<!-- #endregion -->

<!-- #region I see defining a syntax for types without semantics to be actively harmful. -->

<details>
<summary>I see defining a syntax for types without semantics to be actively
harmful.</summary>

This statement is a direct quote from the tc39 meeting notes about the types as 
comments proposal\[[link](https://github.com/tc39/notes/blob/main/meetings/2022-03/mar-29.md#types-as-comments-for-stage-1)\]. This argument applies to my proposal, so I 
have to address it.

Standardizing a semantically typed EcmaScript super set will be a very 
controversial topic that will likely never reach adoption, and for the case it 
does, it will be after many years (possibly decades). What are we going to do up 
until then? Continue using non standard super sets despite all their drawbacks
when compared to complements?

Also it is highly likely that such a semantically typed EcmaScript super set
will be slower than untyped EcmaScript. This means that even then, there will 
still be people that use untyped EcmaScript.

In addition, the only practical path for standardizing semantically typed 
EcmaScript is to start with the standardization of the bare minimum, which is:

* type annotations for variable declarations
* type imports
* type alias declarations

Notice that:

* syntax for generics functions
* typing function parameters in concretion function
* typing function return type in concretion function
* typing classes via `implements`
* syntax associated with types at runtime

are not actually required.

That bare minimum is actually enforcing separation of intend and implementation.
This makes me understand that, an ecosystem that has embraced complements, i.e. 
embraced separation of intend and implementation, will have a smooth transition 
from complements to semantically typed EcmaScript.
</details>

<!-- #endregion -->

<!-- #region The scope of tc39 does not include static typing. -->

<details>
<summary>The scope of tc39 does not include static typing.</summary>

According to ecma-international the scope of tc39 is\[[link](https://www.ecma-international.org/technical-committees/tc39/)\]:

>Scope:
>
>Standardization of the general purpose, cross platform, vendor-neutral 
>programming language ECMAScript®. This includes the language syntax, semantics,
>and libraries and complementary technologies that support the language.

Static typing is a complementary technology that supports the EcmaScript 
language. Hence the context proposal belongs to the scope of tc39.

</details>

<!-- #endregion -->

<!-- #region So you've already decided what the solution should be. -->

<details>
<summary>So you've already decided what the solution should be.</summary>

This statement is a direct quote from the tc39 meeting notes about the types as 
comments proposal\[[link](https://github.com/tc39/notes/blob/main/meetings/2022-03/mar-29.md#types-as-comments-for-stage-1)\].
It has to do with the fact that proposals at stage 1 are supposed to be about 
problem exploration, and not about coming up already with a solution. This 
argument applies to my proposal, so I have to address it.

The problem statement is that super sets are a suboptimal solution to the 
problem of adding static type checking in EcmaScript. How can someone prove that
a solution to a problem is suboptimal? This is only done by showing that there 
is a better solution.

If there is a better solution than the one I have proposed then it should be 
the proposed solution.

</details>

<!-- #endregion -->

## A list of projects that implement the proposal.

1. [complement-lint](https://github.com/lillallol/complement-lint) - a linter 
that ensures you are using TypeScript as a complement
1. [es-test](https://github.com/lillallol/es-test) - testing library
 
<details>
<summary>Things to know before using TypeScript as a complement.</summary>

* Make sure that you take a look at the list of the projects that already 
implement the proposal.
* Read and understand the context proposal.
* Currently, TypeScript will not static type check `.js` files that have a 
corresponding `.d.ts` file. However there are feature requests for that
\[[link](https://github.com/microsoft/TypeScript/issues/48911#issuecomment-1115905062)\].
* There is no `noExplicitAny` option in the `tsconfig.json`. Even if you 
have `noImplicitAny` enabled, things like, `const myArray = []`,
`const myMap = new Map()`, will be treated by TypeScript as being explicitly
typed with `any`.
* You will find that for some edge cases, functions are not properly typed. That
is not the case for arrow functions. There is an issue open for that \[[link](https://github.com/microsoft/TypeScript/issues/49039)\].
* There are some minor differences on how TypeScript applies static typing 
to concretion in `.js` versus `.ts` \[[link](https://github.com/microsoft/TypeScript/issues/30009#issuecomment-469385244)\].
* There is currently no support for extracting type parameters from generic
functions. There is a feature request for that \[[link](https://github.com/microsoft/TypeScript/issues/49112)\].

</details>

## Defining the comments to be used by complements.

This section is tentative.

<details>
<summary>A comment for type alias import, that will also type annotate.</summary>

* statement:

    ```js
    //:"./path/to/file.js".exportedTypeName
    export const add = (a,b) => a+b;
    ```
    
* expression:

    ```js
    ( /*:"./path/to/file.js".exportedTypeName*/ [
        { a : 1 , b : "2" },
        { a : 11, b : "22"}
    ]).forEach(({a,b}) => {
        // do some stuff
    })
    ```

    There is a common misconception that there is an intrinsic need for an extra
    pair of parenthesis\[[link](https://github.com/microsoft/TypeScript/issues/18212#issuecomment-328733868)\]. 

The module specifier has to be type system agnostic, i.e. changing third party
static type checkers, does not require refactoring the module specifier. This 
can be achieved by enforcing the module specifier to end with `.js`. It is up to
the type checker how to resolve this path to its corresponding type file. For
example, with TypeScript, `.js` does is that , they will just replace the ending `.js` with their own extension.
</details>

<details>
<summary>A comment that will inform the type checker that a mistake is expected.
</summary>

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

<details>
<summary>A comment at the top of a file that informs the static type checker to 
ignore it.</summary>

```js
//:type-check-ignore-file
```
</details>
