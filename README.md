<p>&nbsp;&nbsp; 1. hello world</p>
<p>&nbsp;&nbsp; 1. hello world</p>

<details>
<summary>3. test</summary>
    
some description
</details>

<h3 id="-concretions">Concretions</h3>
<h4 id="-concretion-oogaBooga">
    oogaBooga
</h4>

```ts
/**
 * @type {import("../publicApi").IInitializeMVC}
 */
export const initializeMVC: IInitializeMVC;
```

<details open="">
<summary id="-concretion-oogaBooga-references">
    <a href="#-concretion-oogaBooga-references">#</a>
    references
</summary>

<br>

<blockquote>
<details>
<summary id="-concretion-oogaBooga-references-IInitializeMVC">
    <a href="#-concretion-oogaBooga-references-IInitializeMVC">#</a>
    <b>IInitializeMVC</b>
</summary>
        
```ts
export declare type IInitializeMVC = <IState extends serializableReference, IIds extends nonInferedIds>(parameters: {
    ids: IIds;
    defaultState: IState;
    onPersistedStateParseFailure: IOnPersistedStateParseFailure<IState>;
    onPersistedStateParseSuccess: IOnPersistedStateParseSuccess<IState>;
}) => {
    defineCustomElement: <
        ILocalState extends serializableReference,
        ILocalActions extends {
            [x: string]: (...parameters: never[]) => void;
        }
    >(
        name: string,
        localState: (parameters: { state: IState; ids: IIds }) => ILocalState,
        actions: (parameters: { state: IState; action: IAction; ids: IIds }) => ILocalActions,
        htmlTemplate: (parameters: {
            localState: ILocalState;
            actions: ILocalActions;
            html: typeof html;
            setIds: (ids: IIds) => void;
            ids: IIds;
        }) => ReturnType<typeof html>,
        connectedCallback?: (that: HTMLElement) => void,
        disconnectedCallback?: (that: HTMLElement) => void
    ) => {
        new (): HTMLElement;
    };
    /**
     * @description
     * Clears the local storage and reloads the web page.
     */
    resetState: () => void;
    /**
     * @description
     * Returns a copy of the state.
     */
    getState: () => IState;
    customElementDemo: (parameters: {
        changeDefaultState?: (parameters: { state: IState }) => void;
        template: (parameters: { html: typeof html; setIds: (ids: IIds) => void }) => ReturnType<typeof html>;
    }) => void;
};
```








</details>
<blockquote>
<details>
<summary id="-concretion-oogaBooga-references-IInitializeMVC-serializableReference">
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-serializableReference">#</a>
    <b>serializableReference</b>
</summary>
        
```ts
export declare type serializableReference =
    | serializableValue[]
    | {
          [x: string]: serializableValue;
      };
```



</details>
<blockquote>
<details>
<summary id="-concretion-oogaBooga-references-IInitializeMVC-serializableReference-serializableValue">
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-serializableReference-serializableValue">#</a>
    <b>serializableValue</b>
</summary>
        
```ts
export declare type serializableValue = serializableReference | serializablePrimitive;
```

<p>
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-serializableReference">
        <b>serializableReference</b>
    </a>
</p>


</details>
<blockquote>
<details>
<summary id="-concretion-oogaBooga-references-IInitializeMVC-serializableReference-serializableValue-serializablePrimitive">
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-serializableReference-serializableValue-serializablePrimitive">#</a>
    <b>serializablePrimitive</b>
</summary>
        
```ts
declare type serializablePrimitive = null | boolean | string | number;
```



</details>

</blockquote>
</blockquote><details>
<summary id="-concretion-oogaBooga-references-IInitializeMVC-nonInferedIds">
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-nonInferedIds">#</a>
    <b>nonInferedIds</b>
</summary>
        
```ts
export declare type nonInferedIds = {
    [webComponentName: string]: serializableValue | undefined;
};
```

<p>
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-serializableReference-serializableValue">
        <b>serializableValue</b>
    </a>
</p>

</details>
<blockquote>

</blockquote><details>
<summary id="-concretion-oogaBooga-references-IInitializeMVC-IOnPersistedStateParseFailure">
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-IOnPersistedStateParseFailure">#</a>
    <b>IOnPersistedStateParseFailure</b>
</summary>
        
```ts
export declare type IOnPersistedStateParseFailure<IState> = () => IState;
```



</details>
<details>
<summary id="-concretion-oogaBooga-references-IInitializeMVC-IOnPersistedStateParseSuccess">
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-IOnPersistedStateParseSuccess">#</a>
    <b>IOnPersistedStateParseSuccess</b>
</summary>
        
```ts
export declare type IOnPersistedStateParseSuccess<IState> = (parsedState: serializableValue) => IState;
```

<p>
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-serializableReference-serializableValue">
        <b>serializableValue</b>
    </a>
</p>

</details>
<blockquote>

</blockquote><details>
<summary id="-concretion-oogaBooga-references-IInitializeMVC-IAction">
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-IAction">#</a>
    <b>IAction</b>
</summary>
        
```ts
export declare type IAction = <P, R>(fnToDecorate: (...parameters: P[]) => R) => (...parameters: P[]) => R;
```



</details>
<details>
<summary id="-concretion-oogaBooga-references-IInitializeMVC-html">
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-html">#</a>
    <b>html</b>
</summary>
        
```ts
/**
 * Interprets a template literal as an HTML template that can efficiently
 * render to and update a container.
 */
export declare const html: (strings: TemplateStringsArray, ...values: unknown[]) => TemplateResult;
```



</details>
<blockquote>
<details>
<summary id="-concretion-oogaBooga-references-IInitializeMVC-html-TemplateResult">
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-html-TemplateResult">#</a>
    <b>TemplateResult</b>
</summary>
        
```ts
/**
 * The return type of `html`, which holds a Template and the values from
 * interpolated expressions.
 */
export declare class TemplateResult {
    readonly strings: TemplateStringsArray;
    readonly values: readonly unknown[];
    readonly type: string;
    readonly processor: TemplateProcessor;
    constructor(strings: TemplateStringsArray, values: readonly unknown[], type: string, processor: TemplateProcessor);
    /**
     * Returns a string of HTML used to create a `<template>` element.
     */
    getHTML(): string;
    getTemplateElement(): HTMLTemplateElement;
}
```



</details>
<blockquote>
<details>
<summary id="-concretion-oogaBooga-references-IInitializeMVC-html-TemplateResult-TemplateProcessor">
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-html-TemplateResult-TemplateProcessor">#</a>
    <b>TemplateProcessor</b>
</summary>
        
```ts
export interface TemplateProcessor {
    /**
     * Create parts for an attribute-position binding, given the element,
     * attribute name, and string literals.
     *
     * @param element The element containing the binding
     * @param name  The attribute name, including a possible prefix. The name may
     *   be prefixed by `.` (for a property binding), `@` (for an event binding)
     * or
     *   `?` (for a boolean attribute binding).
     * @param strings The array of literal strings that form the static part of
     *     the
     *   attribute value. There are always at least two strings,
     *   even for fully-controlled bindings with a single expression. For example,
     *   for the binding `attr="${e1}-${e2}"`, the `strings` array includes three
     *   strings (`['', '-', '']`)—the text _before_ the first expression (the
     * empty string), the text between the two expressions (`'-'`), and the text
     * after the last expression (another empty string).
     */
    handleAttributeExpressions(element: Element, name: string, strings: ReadonlyArray<string>, options: RenderOptions): ReadonlyArray<Part>;
    /**
     * Create parts for a text-position binding.
     * @param partOptions
     */
    handleTextExpression(options: RenderOptions): NodePart;
}
```





</details>
<blockquote>
<details>
<summary id="-concretion-oogaBooga-references-IInitializeMVC-html-TemplateResult-TemplateProcessor-RenderOptions">
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-html-TemplateResult-TemplateProcessor-RenderOptions">#</a>
    <b>RenderOptions</b>
</summary>
        
```ts
export interface RenderOptions {
    readonly templateFactory: TemplateFactory;
    readonly eventContext?: EventTarget;
}
```



</details>
<blockquote>
<details>
<summary id="-concretion-oogaBooga-references-IInitializeMVC-html-TemplateResult-TemplateProcessor-RenderOptions-TemplateFactory">
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-html-TemplateResult-TemplateProcessor-RenderOptions-TemplateFactory">#</a>
    <b>TemplateFactory</b>
</summary>
        
```ts
/**
 * A function type that creates a Template from a TemplateResult.
 *
 * This is a hook into the template-creation process for rendering that
 * requires some modification of templates before they're used, like ShadyCSS,
 * which must add classes to elements and remove styles.
 *
 * Templates should be cached as aggressively as possible, so that many
 * TemplateResults produced from the same expression only do the work of
 * creating the Template the first time.
 *
 * Templates are usually cached by TemplateResult.strings and
 * TemplateResult.type, but may be cached by other keys if this function
 * modifies the template.
 *
 * Note that currently TemplateFactories must not add, remove, or reorder
 * expressions, because there is no way to describe such a modification
 * to render() so that values are interpolated to the correct place in the
 * template instances.
 */
export declare type TemplateFactory = (result: TemplateResult) => Template;
```

<p>
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-html-TemplateResult">
        <b>TemplateResult</b>
    </a>
</p>


</details>
<blockquote>
<details>
<summary id="-concretion-oogaBooga-references-IInitializeMVC-html-TemplateResult-TemplateProcessor-RenderOptions-TemplateFactory-Template">
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-html-TemplateResult-TemplateProcessor-RenderOptions-TemplateFactory-Template">#</a>
    <b>Template</b>
</summary>
        
```ts
/**
 * An updatable Template that tracks the location of dynamic parts.
 */
export declare class Template {
    readonly parts: TemplatePart[];
    readonly element: HTMLTemplateElement;
    constructor(result: TemplateResult, element: HTMLTemplateElement);
}
```


<p>
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-html-TemplateResult">
        <b>TemplateResult</b>
    </a>
</p>

</details>
<blockquote>
<details>
<summary id="-concretion-oogaBooga-references-IInitializeMVC-html-TemplateResult-TemplateProcessor-RenderOptions-TemplateFactory-Template-TemplatePart">
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-html-TemplateResult-TemplateProcessor-RenderOptions-TemplateFactory-Template-TemplatePart">#</a>
    <b>TemplatePart</b>
</summary>
        
```ts
/**
 * A placeholder for a dynamic expression in an HTML template.
 *
 * There are two built-in part types: AttributePart and NodePart. NodeParts
 * always represent a single dynamic expression, while AttributeParts may
 * represent as many expressions are contained in the attribute.
 *
 * A Template's parts are mutable, so parts can be replaced or modified
 * (possibly to implement different template semantics). The contract is that
 * parts can only be replaced, not removed, added or reordered, and parts must
 * always consume the correct number of values in their `update()` method.
 *
 * TODO(justinfagnani): That requirement is a little fragile. A
 * TemplateInstance could instead be more careful about which values it gives
 * to Part.update().
 */
export declare type TemplatePart =
    | {
          readonly type: "node";
          index: number;
      }
    | {
          readonly type: "attribute";
          index: number;
          readonly name: string;
          readonly strings: ReadonlyArray<string>;
      };
```



</details>

</blockquote>
</blockquote>
</blockquote><details>
<summary id="-concretion-oogaBooga-references-IInitializeMVC-html-TemplateResult-TemplateProcessor-Part">
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-html-TemplateResult-TemplateProcessor-Part">#</a>
    <b>Part</b>
</summary>
        
```ts
/**
 * The Part interface represents a dynamic part of a template instance rendered
 * by lit-html.
 */
export interface Part {
    readonly value: unknown;
    /**
     * Sets the current part value, but does not write it to the DOM.
     * @param value The value that will be committed.
     */
    setValue(value: unknown): void;
    /**
     * Commits the current part value, causing it to actually be written to the
     * DOM.
     *
     * Directives are run at the start of `commit`, so that if they call
     * `part.setValue(...)` synchronously that value will be used in the current
     * commit, and there's no need to call `part.commit()` within the directive.
     * If directives set a part value asynchronously, then they must call
     * `part.commit()` manually.
     */
    commit(): void;
}
```



</details>
<details>
<summary id="-concretion-oogaBooga-references-IInitializeMVC-html-TemplateResult-TemplateProcessor-NodePart">
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-html-TemplateResult-TemplateProcessor-NodePart">#</a>
    <b>NodePart</b>
</summary>
        
```ts
/**
 * A Part that controls a location within a Node tree. Like a Range, NodePart
 * has start and end locations and can set and update the Nodes between those
 * locations.
 *
 * NodeParts support several value types: primitives, Nodes, TemplateResults,
 * as well as arrays and iterables of those types.
 */
export declare class NodePart implements Part {
    readonly options: RenderOptions;
    startNode: Node;
    endNode: Node;
    value: unknown;
    constructor(options: RenderOptions);
    /**
     * Appends this part into a container.
     *
     * This part must be empty, as its contents are not automatically moved.
     */
    appendInto(container: Node): void;
    /**
     * Inserts this part after the `ref` node (between `ref` and `ref`'s next
     * sibling). Both `ref` and its next sibling must be static, unchanging nodes
     * such as those that appear in a literal section of a template.
     *
     * This part must be empty, as its contents are not automatically moved.
     */
    insertAfterNode(ref: Node): void;
    /**
     * Appends this part into a parent part.
     *
     * This part must be empty, as its contents are not automatically moved.
     */
    appendIntoPart(part: NodePart): void;
    /**
     * Inserts this part after the `ref` part.
     *
     * This part must be empty, as its contents are not automatically moved.
     */
    insertAfterPart(ref: NodePart): void;
    setValue(value: unknown): void;
    commit(): void;
    clear(startNode?: Node): void;
}
```

<p>
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-html-TemplateResult-TemplateProcessor-Part">
        <b>Part</b>
    </a>
</p>
<p>
    <a href="#-concretion-oogaBooga-references-IInitializeMVC-html-TemplateResult-TemplateProcessor-RenderOptions">
        <b>RenderOptions</b>
    </a>
</p>

</details>
<blockquote>

</blockquote>
</blockquote>
</blockquote>
</blockquote>
</blockquote>
</blockquote>
</details>
<hr>
