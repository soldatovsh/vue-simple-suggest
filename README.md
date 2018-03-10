# vue-simple-suggest

> Simple yet feature-rich autocomplete component for Vue.js


[![npm](https://img.shields.io/npm/v/vue-simple-suggest.svg?style=flat-square)](https://www.npmjs.com/package/vue-simple-suggest)
[![live demo](https://img.shields.io/badge/demo-live-brightgreen.svg?style=flat-square)](https://kazanexpress.github.io/vue-simple-suggest/)
[![](https://img.shields.io/badge/github-repo-lightgray.svg?style=flat-square)](https://github.com/KazanExpress/vue-simple-suggest)
[![](https://img.shields.io/badge/very-awesome-orange.svg?style=flat-square)](https://github.com/vuejs/awesome-vue#autocomplete)

```bash
npm install --save vue-simple-suggest
```

## Table of contents

- [What is it?](#what-is-it)
- [Build](#build-setup)
- [Controls](#default-controls)
- [Component API](#component-api)
  - [TLDR](#tldr)
  - [API Definitions](#api-definitions)
    - [Props](#props)
    - [Emitted Events](#emitted-events)
    - [Ref Methods](#ref-methods)
    - [Ref Event Handlers](#ref-event-handlers)
    - [Ref Data](#ref-data)
  - [Slots](#slots)
    - [Custom input](#custom-input)
    - [Custom suggestion item](#custom-suggestion-item)
    - [Custom miscellanious item slots](#custom-miscellanious-item-slots)


## What is it

This is a simple yet feature-rich suggestion/autocomplete component for Vue.js.

It supports v-model, allows custom styling, custom input and suggestion list templates, API calls and more.

Actually, it's so feature rich, that it's possible to do crazy stuff with it, like
  - Imitating drop-downs and drop-down menus
  - Turn suggestions list into an actual suggestions table
  - Work with ANY type of custom input passed (like type=button, radio and etc.)
  - ... And many more things

And, as a bonus, it is very light (<4kb minified).

All of the props, events and slots are OPTIONAL for this component, so it can be used without any configuration at all.

-----
## Build Setup

``` bash
# clone the repo
git clone https://github.com/KazanExpress/vue-simple-suggest.git
cd ./vue-simple-suggest

# install dependencies
npm install

# serve example with hot reload at localhost
npm run dev

# build example & readme for static serving
npm run docs
```
-----
## Default Controls

|Key | Description|
|-|-|
|`Escape` | If the suggestions list is shown - hide it.|
|`ArrowDown` | If the suggestions list is hidden - show it.|
|`ArrowUp`/`ArrowDown` | Cycle (hover) through suggestions.|
|`Enter` | If the list is shown - chooses the highlighted element, if the list is hidden - refills the suggestions based on current input text.|
|`Ctrl + Space`/`Cmd + Space` | Select the first element in the list.|

-----
## Component API

### TLDR

```html
<!-- Ref to access the API, v-model for efficient query binding -->
<vue-simple-suggest ref="vueSimpleSuggest" v-model="model"
  valueAttribute="id"
  displayAttribute="title"
  :placeholder="placeholder!!!"
  :get-list="getListFunction"
  :max-count="10"
  :min-length="3"
  :debounce="100"
  :destyled="false"
  :removeList="false"
  :filter-by-query="false"
  :value="defaultValue"
  @input="onInputEvent"
  @select="onSuggestSelect"
  @hover="onSuggestHover"
  @focus="onFocus"
  @blur="onBlur"
  @request-start="onRequestStart"
  @request-done="onRequestDone"
  @request-failed="onRequestFailed"
  @show-list="onShowList"
  @hide-list="onHideList"
>
  <!-- v-model on input itself is useless -->
  <input class="optional-custom-input">

  <!-- Appears o top of the list -->
  <template slot="miscItem-above" slot-scope="{ suggestions, query }">
    <div class="misc-item">
      <span>You're searching for {{ query }}.</span>
    </div>
    <div class="misc-item">
      <span>{{ suggestions.length }} suggestions are shown...</span>
    </div>
    <hr>
  </template>

  <div slot="suggestionItem" slot-scope="{ suggestion }" class="custom">{{ suggestion.title }}</div>

  <!-- Appears below the list -->
  <div class="misc-item" slot="miscItem-below" slot-scope="{ suggestions }" v-if="loading">
    <span>Loading...</span>
  </div>
</vue-simple-suggest>
```

-----
### API definitions

#### Props
| Name                           | Type     | Default  | Description         |
|--------------------------------|----------|----------|--------------------------------------------------------------|
| `maxSuggestions`               | Number   | `10`       | The maximum amount of suggestions to display. Set to 0 for infinite suggestions. |
| `displayAttribute`             | String   | `'title'`  | The property in a suggestion object to display in a list. Supports dotted paths. |
| `valueAttribute`               | String   | `'id'`     | The property in a suggestion object to use as a unique key. Supports dotted paths. |
| `getList`                      | Funciton or Array | `() => []` | The array provider function, must accept a query as its only argument. Can return an array or a promise. Can be async. The component behaves as a simple input without this function. |
| `debounce`                     | Number   | `0`        | Determines the getList debounce (a time between the input event and a function execution). |
| `destyled`                     | Boolean  | `false`    | Whether to cancel the default styling of input and suggestions list. |
| `removeList`                   | Boolean  | `false`    | If true - the suggestion list will be always hidden. |
| `filterByQuery`                | Boolean  | `false`    | Whether to filter the resulting suggestions by input's text query (make it a search component). |
| type, value, pattern, etc...   |          |            | All of the HTML5 input attributes with their respected default values. |

-----
#### Emitted Events
| Name            | Arguments                   | Description                                                                                            |
|-----------------|-----------------------------|--------------------------------------------------------------------------------------------------------|
| `input`         | HTML input event            | An outward projection of the current input's event.                                                    |
| `focus`         | HTML focus event            | An outward projection of the current input's event.                                                    |
| `blur`          | HTML focus event            | An outward projection of the current input's event.                                                    |
| `select`        | Selected suggestion         | Fires on suggestion selection (via a mouse click or enter keypress).                                   |
| `hover`         | Hovered suggestion          | Fires each time a new suggestion is highlighted (via a cursor movement or keyboard arrows).            |
| `showList`      | -                           | Fires each time the suggestion list is toggled to be shown.                                            |
| `hideList`      | -                           | Fires each time the suggestion list is being hidden.                                                   |
| `requestStart`  | Current input value (query) | Fires each time a `getList` function starts executing.                                                 |
| `requestDone`   | Resulting suggestions list  | Fires when a `getList` function successfully returns a result and forwards that result as an argument. |
| `requestFailed` | The interrrupting exception | Fires if an exception occurs during the execution of a `getList` funciton.                             |

-----

#### Ref Methods
> accessed via `$refs.*your ref name here*`

| Name | Arguments | Description |
|------|-----------|-------------|
|`showList`| - | Shows the suggestion list. Emits the respected [event](#emitted-events). |
|`hideList`| - | Hides the suggestion list. Emits the respected [event](#emitted-events). |
|`getSuggestions`| `query`: string | Gets and processes suggestions from the `list` prop. Returns a promise. Emits the `requestStart`, `requestDone` and `requestFailed` [events](#emitted-events). |
|`research`| - | Debounced `getSuggestions` on the current input value. |
|`clearSuggestions`| - | Clears the `suggestions` array. |
|`select`| suggestion | Selects the passed suggestion. Emits the respected [event](#emitted-events). |
|`hover`| suggestion | Hovers over the passed suggestion. Emits the respected [event](#emitted-events). |
|`displayProperty`| suggestion | Returns the displayed property of a suggestion. |
|`valueProperty`| suggestion | Returns the value property of a suggestion. |

-----

#### Ref Event Handlers
> accessed via `$refs.*your ref name here*`

You can use these to imitate come of the component's behaviours.

| Name | Arguments | Description |
|------|-----------|-------------|
|`onInputClick`| HTML click event | Fires whenever the input is being clicked. |
|`onInput`| HTML input event | Fires whenever the input text is changed. Emits the [`input`](#emitted-events) event. |
|`onFocus`| HTML focus event | Fires whenever the input comes into focus, emits the [`focus`](#emitted-events) event. |
|`onBlur`| HTML focus event | Antonym to `onFocus`. |
|`onAutocomplete`| - | Fires when the autocomplete [keyboard shortcut](#default-controls) is pressed. Selects the first suggestion. |
|`onListKeyUp`| HTML keyup event | Fires on component keyup. Internally used for hiding the list. |
|`onArrowKeyDown`| HTML keydown event | Fires on component keydown. Internally used for arrow-key-based selections. |

-----

#### Ref Data
> accessed via `$refs.*your ref name here*`

| Name | Default | Description |
|------|-----------|-------------|
|`selected`| `null` | Currently selected element. |
|`hovered`| `null` | Currently hovered element. |
|`suggestions`| `[]` | Current suggestions list. |
|`listShown`| `false` | Is suggestion list shown. |
|`inputElement`| `null` | Currently used HTMLInputElement. |
|`canSend`| `true` | Whether the assigned getListFuncion can be executed. |
|`timeoutInstance`| `null` | The timeout until next getListFunction execution. |
|`text`| `vueSimpleSuggest.$props.value` | Current input text. |
|`slotIsComponent`| - | Whether this current custom input is a vue-component. |
|`listIsRequest`| - | Whether the list prop is a function. |
|`input`| - | A ref to the current input (component or vanilla). |
|`hoveredIndex`| - | The current hovered element index. |

-----

### Slots
all optional

##### Custom input
> default slot

Supports nesting. Input props can be passed to a custom input to avoid their processing by vue-simple-suggest.
Defaults to a simple input with props passed to vue-simple-suggest.

**Warning:** `v-model` on a custom input IS NOT the same as `v-model` on vue-simple-suggest!

```html
<!--  Default HTMLInputElement example:  -->
<vue-simple-suggest v-model="model" placeholder="Text here" type="search" pattern="[a-z]+"/>
```
```html
<!--  Vanilla HTMLInputElement example 1:  -->
<vue-simple-suggest>
  <input pattern="[a-z]+">
</vue-simple-suggest>
```
```html
<!--  Vanilla HTMLInputElement example 2:  -->
<vue-simple-suggest v-model="model" placeholder="Text here" type="search">
</vue-simple-suggest>
```
```html
<!--  Vanilla HTMLInputElement example 3 (fully equivalent to the second example):  -->
<vue-simple-suggest v-model="model">
  <input placeholder="Text here" type="search">
</vue-simple-suggest>
```
```html
<!--  Vanilla HTMLInputElement example 4 (nested):  -->
<vue-simple-suggest>
  <div>
    <section>
      <input type="email">
    </section>
  </div>
</vue-simple-suggest>
```
```html
<!--  Vue component example (also supports nesting):  -->
<vue-simple-suggest>
  <my-custom-input-somponent></my-custom-input-somponent>
</vue-simple-suggest>
```

##### Custom suggestion item
> `suggestionItem` slot

Allows custom html-definitons of the suggestion items in a list.
Defaults to `<span>{{ displayAttribute(suggestion) }}</span>`

Accepts the `suggestion` object and a `query` text as a `slot-scope` attribute values.

```html
<!-- Example: -->
<vue-simple-suggest>
  <div slot="suggestionItem" slot-scope="{ suggestion, query }">
    <div>My {{ suggestion.title }}</div>
  </div>
</vue-simple-suggest>
```

In cooperation with [ref fields](#ref-methods) can be used to drastically transform the suggestion list behaviour.

One of the simplest examples - highlighting the query text in the results:

```html
<div slot="suggestionItem" slot-scope="scope">
  <span v-html="boldenSuggestion(scope)"></span>
</div>
```

```js
boldenSuggestion({ suggestion, query }) {
  let result = this.$refs.vueSimpleSuggest.displayProperty(suggestion);

  if (!query) return result;

  const replace = str => (result = result.replace(str, str.bold()));
  const texts = query.split(/[\s-_/\\|\.]/gm).filter(t => !!t) || [''];
  const processors = [
    s => s[0].toUpperCase() + s.substr(1),
    s => s.toLowerCase(),
    s => s.toUpperCase(),
    s => s
  ];
  texts.forEach(text => processors.forEach(processor => replace(processor(text))));

  return result;
}
```
Result:

![](assets/screenshot.jpg)

##### Custom miscellanious item slots
> `miscItem-above` and `miscItem-below` slots

Allow custom elements to be shown in suggestion list. These elements never dissapear from the list, niether can they be selected nor hovered on.

These can be used for decoration, loaders, error messages and etc.

Do not have defaults, so are not shown until defined.

Accept the `suggestions` array and a `query` text as a `slot-scope` attribute values.

```html
<!-- Examples: -->
<vue-simple-suggest>
  <template slot="miscItem-above" slot-scope="{ suggestions, query }">
    <div class="misc-item">
      <span>You're searching for {{ query }}.</span>
    </div>
    <div class="misc-item">
      <span>{{ suggestions.length }} suggestions are shown...</span>
    </div>
  </template>

  <div slot="miscItem-below" slot-scope="{ suggestions }" v-if="isLoading" class="misc-item">
    <span>Loading...</span>
  </div>
</vue-simple-suggest>
```
