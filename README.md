# svelte-pincode

[![NPM][npm]][npm-url]

> Declarative pin code component for Svelte.

<!-- REPO_URL -->

A pin code is a short sequence of characters (usually numeric) used for verification. It differs from a password in that it is typically ephemeral and is not predetermined by the user.

Try it in the [Svelte REPL](https://svelte.dev/repl/2841eef46bfb49c4a848a2a063602e5d).

---

<!-- TOC -->

## Installation

**Yarn**

```bash
yarn add -D svelte-pincode
```

**NPM**

```bash
npm i -D svelte-pincode
```

**pnpm**

```bash
pnpm i -D svelte-pincode
```

## Usage

### Basic

Bind to either the `code` or `value` prop.

- **code** (`string[]`): Array of input values. An empty string represents an undefined value.
- **value** (`string`): `code` joined as a string.

<!-- prettier-ignore-start -->
```svelte
<script>
  import { Pincode, PincodeInput } from "svelte-pincode";

  let code = [];
  let value = '';
</script>

<Pincode bind:code bind:value>
  <PincodeInput />
  <PincodeInput />
  <PincodeInput />
  <PincodeInput />
</Pincode>

<div>code: <code>{JSON.stringify(code)}</code></div>
<div>value: <code>{JSON.stringify(value)}</code></div>
```
<!-- prettier-ignore-end -->

### Select text on focus

Set `selectTextOnFocus` to `true` for the input value text to be selected when focused.

<!-- prettier-ignore-start -->
```svelte
<script>
  import { Pincode, PincodeInput } from "svelte-pincode";

  let input;
</script>

<Pincode selectTextOnFocus>
  <PincodeInput bind:ref={input} value="0" />
  <PincodeInput />
  <PincodeInput />
  <PincodeInput />
</Pincode>

<button on:click={() => input.focus()}>
  Focus input
</button>
```
<!-- prettier-ignore-end -->

### Numeric variant

By default, this component accepts alphanumeric values.

Set `type` to `"numeric"` to only allow numbers.

<!-- prettier-ignore-start -->
```svelte
<Pincode type="numeric">
  <PincodeInput />
  <PincodeInput />
  <PincodeInput />
  <PincodeInput />
</Pincode>
```
<!-- prettier-ignore-end -->

### Initial values

Define intitial input values by using the `code` prop or `value` prop on `PincodeInput`.

<!-- prettier-ignore-start -->
```svelte
<script>
  import { Pincode, PincodeInput } from "svelte-pincode";

  let pincode = ["1", "", "3"];
</script>

<Pincode bind:code={pincode}>
  <PincodeInput />
  <PincodeInput value="2" />
  <PincodeInput />
  <PincodeInput />
</Pincode>

<div>code: <code>{JSON.stringify(pincode)}</code></div>
```
<!-- prettier-ignore-end -->

### Validating upon completion

Actual validation is left to the consumer.

This example illustrates how you can validate the code once all inputs have a value by binding to the `complete` prop.

<!-- prettier-ignore-start -->
```svelte
<script>
  import { Pincode, PincodeInput } from "svelte-pincode";

  const correctCode = "1234";

  let inputValue = '';
  let complete = false;

  $: success = complete && inputValue === correctCode;
  $: error = complete && !success;
</script>

<Pincode bind:complete bind:value={inputValue}>
  <PincodeInput />
  <PincodeInput />
  <PincodeInput />
  <PincodeInput />
</Pincode>

<div class:complete class:success class:error>
  {#if !complete}
    Enter {correctCode.length - inputValue.length} more digit(s)...
  {/if}
  {#if success}Correct code{/if}
  {#if error}Incorrect code{/if}
</div>
```
<!-- prettier-ignore-end -->

Use the dispatched "complete" event as an alternative to binding the `complete` prop.

```svelte no-eval
<Pincode
  on:complete="{(e) => {
    console.log(e.detail); // { code: string[]; value: string; }
  }}"
/>
```

### Programmatic usage

`code` can be set programmatically.

In the following example, type some initial values and try setting or clearing the code.

<!-- prettier-ignore-start -->
```svelte
<script>
  import { Pincode, PincodeInput } from "svelte-pincode";

  let passcode = [];
</script>

<Pincode bind:code={passcode}>
  <PincodeInput />
  <PincodeInput />
  <PincodeInput />
  <PincodeInput />
</Pincode>

<div>code: <code>{JSON.stringify(passcode)}</code></div>

<button on:click={() => passcode = ['1', '2', '3', '4']}>
  Set code
</button>

<button on:click={() => passcode = ['', '', '', '']}>
  Clear code
</button>
```
<!-- prettier-ignore-end -->

### Focusing the first input

To programmatically focus the first input, invoke the `focusFirstInput` method in a component reference.

<!-- prettier-ignore-start -->
```svelte
<script>
  import { Pincode, PincodeInput } from "svelte-pincode";

  let ref;
</script>

<Pincode bind:this={ref}>
  <PincodeInput />
  <PincodeInput />
  <PincodeInput />
  <PincodeInput />
</Pincode>

<button on:click={ref.focusFirstInput}>
  Focus first input
</button>
```
<!-- prettier-ignore-end -->

### Focusing the next empty input

To focus the next input with no value, invoke the `focusNextEmptyInput` method.

<!-- prettier-ignore-start -->
```svelte
<script>
  import { Pincode, PincodeInput } from "svelte-pincode";

  let pincodeRef;
</script>

<Pincode code={["9", "9"]} bind:this={pincodeRef}>
  <PincodeInput />
  <PincodeInput />
  <PincodeInput />
  <PincodeInput />
</Pincode>

<button on:click={pincodeRef.focusNextEmptyInput}>
  Focus next empty input
</button>
```
<!-- prettier-ignore-end -->

### Focusing the last input

To focus the last input, invoke the `focusLastInput` method.

<!-- prettier-ignore-start -->
```svelte
<script>
  import { Pincode, PincodeInput } from "svelte-pincode";

  let passcodeRef;
</script>

<Pincode bind:this={passcodeRef}>
  <PincodeInput />
  <PincodeInput />
  <PincodeInput />
  <PincodeInput />
</Pincode>

<button on:click={passcodeRef.focusLastInput}>
  Focus last input
</button>
```
<!-- prettier-ignore-end -->

## Styling

### Customization

This component is minimally styled; override the default styles by targeting the `data-pincode` selector:

```css
/** Pincode **/
:global([data-pincode]) {
  display: inline-flex;
  border: 1px solid #e0e0e0;
}

/** PincodeInput */
:global([data-pincode] input) {
  width: 3rem;
  padding: 0.5rem 1rem;
  margin: 0;
  border: 0;
  border-radius: 0;
  text-align: center;
}

:global([data-pincode] input:focus) {
  z-index: 1;
}

:global([data-pincode] input:not(:last-of-type)) {
  border-right: 1px solid #e0e0e0;
}
```

### Unstyled components

Use the unstyled components located in the `svelte/src/unstyled` folder if you prefer to style the components from scratch.

```html
<script>
  import Pincode from "svelte-pincode/src/unstyled/Pincode.svelte";
  import PincodeInput from "svelte-pincode/src/unstyled/PincodeInput.svelte";
</script>
```

## API

### Pincode

#### Props

| Name              | Type                            | Default value    |
| :---------------- | :------------------------------ | :--------------- |
| code              | `string[]`                      | `[]`             |
| value             | `string`                        | `""`             |
| type              | `"alphanumeric"` or `"numeric"` | `"alphanumeric"` |
| selectTextOnFocus | `boolean`                       | `false`          |

#### Accessors

- `focusFirstInput`
- `focusNextEmptyInput`
- `focusLastInput`

#### Dispatched Events

- **on:complete**: fired when all inputs have a value

```svelte no-eval
<Pincode
  on:complete="{(e) => {
    console.log(e.detail); // { code: string[]; value: string; }
  }}"
/>
```

### PincodeInput

#### Props

| Name | Type               | Default value                          |
| :--- | :----------------- | :------------------------------------- |
| id   | `string`           | `"input" + Math.random().toString(36)` |
| ref  | `HTMLInputElement` | `null`                                 |

#### Forwarded Events

- on:focus
- on:blur
- on:keydown

## TypeScript

Svelte version 3.31 or greater is required to use this component with TypeScript.

## Changelog

[CHANGELOG.md](CHANGELOG.md)

## License

[MIT](LICENSE)

[npm]: https://img.shields.io/npm/v/svelte-pincode.svg?style=for-the-badge&color=%23ff3e00
[npm-url]: https://npmjs.com/package/svelte-pincode
