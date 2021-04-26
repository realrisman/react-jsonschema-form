react-jsonschema-form
=====================

[![Build Status](https://travis-ci.org/mozilla-services/react-jsonschema-form.svg)](https://travis-ci.org/mozilla-services/react-jsonschema-form)

A simple [React](http://facebook.github.io/react/) component capable of building HTML forms out of a [JSON schema](http://json-schema.org/) and using [Bootstrap](http://getbootstrap.com/) semantics by default.

A [live playground](https://rjsf-team.github.io/react-jsonschema-form/) is hosted on gh-pages.

![Image](https://i.imgur.com/M8ZCES5.gif)

Testing powered by BrowserStack<br>
<a target=_blank href="https://www.browserstack.com/"><img height=200 src="https://user-images.githubusercontent.com/1689183/51487090-4ea04f80-1d57-11e9-9a91-79b7ef8d2013.png"></a>

## Philosophy

react-jsonschema-form is meant to automatically generate a React form based on a [JSON Schema](http://json-schema.org/). It is a major component in the [kinto-admin](https://github.com/Kinto/kinto-admin/) project. If you want to generate a form for any data, sight unseen, simply given a JSON schema, react-jsonschema-form may be for you. If you have _a priori_ knowledge of your data and want a toolkit for generating forms for it, you might look elsewhere.

react-jsonschema-form validates that the data conforms to the given schema, but doesn't prevent the user from inputing data that doesn't fit (for example, stripping non-numbers from a number field, or adding values to an array that is already "full").

## Installation

Requires React 16.0.0+.

> Note: The `master` branch of the repository reflects ongoing development. Releases are published as [tags](https://github.com/mozilla-services/react-jsonschema-form/releases). You should never blindly install from `master`, but rather check what the available stable releases are.


### As a npm-based project dependency

```bash
$ npm install @rjsf/core --save
```

> Note: While the library renders [Bootstrap](http://getbootstrap.com/) HTML semantics, you have to build and load the Bootstrap styles on your own.

### As a script served from a CDN

```html
  <script src="https://unpkg.com/react-jsonschema-form/dist/react-jsonschema-form.js"></script>
```

Source maps are available at [this url](https://unpkg.com/react-jsonschema-form/dist/react-jsonschema-form.js.map).

> Note: The CDN version **does not** embed `react` or `react-dom`.

You'll also need to alias the default export property to use the Form component:

```jsx
const Form = JSONSchemaForm.default;
// or
const {default: Form} = JSONSchemaForm;
```

## Usage

```jsx
import React, { Component } from "react";
import { render } from "react-dom";

import Form from "@rjsf/core";

const schema = {
  title: "Todo",
  type: "object",
  required: ["title"],
  properties: {
    title: {type: "string", title: "Title", default: "A new task"},
    done: {type: "boolean", title: "Done?", default: false}
  }
};

const log = (type) => console.log.bind(console, type);

render((
  <Form schema={schema}
        onChange={log("changed")}
        onSubmit={log("submitted")}
        onError={log("errors")} />
), document.getElementById("app"));
```

This will generate a form like this (assuming you loaded the standard [Bootstrap](http://getbootstrap.com/) stylesheet):

![](https://i.imgur.com/DZQYPyu.png)

### Form initialization

Often you'll want to prefill a form with existing data; this is done by passing a `formData` prop object matching the schema:

```jsx
const formData = {
  title: "First task",
  done: true
};

render((
  <Form schema={schema}
        formData={formData} />
), document.getElementById("app"));
```

> Note: If your form has a single field, pass a single value to `formData`. ex: `formData='Charlie'`

> WARNING: If you have situations where your parent component can re-render, make sure you listen to the `onChange` event and update the data you pass to the `formData` attribute.

### Form event handlers

#### Form submission

You can pass a function as the `onSubmit` prop of your `Form` component to listen to when the form is submitted and its data are valid. It will be passed a result object having a `formData` attribute, which is the valid form data you're usually after. The original event will also be passed as a second parameter:

```js
const onSubmit = ({formData}, e) => console.log("Data submitted: ",  formData);

render((
  <Form schema={schema}
        onSubmit={onSubmit} />
), document.getElementById("app"));
```

> Note: If there are fields in the `formData` that are not represented in the schema, they will be retained by default. If you would like to remove those extra values on form submission, then set the `omitExtraData` prop to `true`. Set the `liveOmit` prop to true in order to remove extra data upon form data change.

#### Form error event handler

To react when submitted form data are invalid, pass an `onError` handler. It will be passed the list of encountered errors:

```js
const onError = (errors) => console.log("I have", errors.length, "errors to fix");

render((
  <Form schema={schema}
        onError={onError} />
), document.getElementById("app"));
```

#### Form data changes

If you plan on being notified every time the form data are updated, you can pass an `onChange` handler, which will receive the same args as `onSubmit` any time a value is updated in the form.

> Note: If `omitExtraData` and `liveOmit` are both set to true, then extra form data values that are not in any form field will be removed whenever `onChange` is called.

#### Form field blur events

Sometimes you may want to trigger events or modify external state when a field has been touched, so you can pass an `onBlur` handler, which will receive the id of the input that was blurred and the field value.

#### Form field focus events

Sometimes you may want to trigger events or modify external state when a field has been focused, so you can pass an `onFocus` handler, which will receive the id of the input that is focused and the field value.

### Submit form programmatically
You can use the reference to get your `Form` component and call the `submit` method to submit the form programmatically without a submit button.
This method will dispatch the `submit` event of the form, and the function, that is passed to `onSubmit` props, will be called.

```js
const onSubmit = ({formData}) => console.log("Data submitted: ",  formData);
let yourForm;

render((
  <Form schema={schema}
        onSubmit={onSubmit} ref={(form) => {yourForm = form;}}/>
), document.getElementById("app"));

yourForm.submit();
```

## Styling your forms

This library renders form fields and widgets leveraging the [Bootstrap](http://getbootstrap.com/) semantics. That means your forms will be beautiful by default if you're loading its stylesheet in your page.

You're not necessarily forced to use Bootstrap; while it uses its semantics, it also provides a bunch of other class names so you can bring new styles or override default ones quite easily in your own personalized stylesheet. That's just HTML after all :)

If you're okay with using styles from the Bootstrap ecosystem though, then the good news is that you have access to many themes for it, which are compatible with our generated forms!

Here are some examples from the [playground](https://rjsf-team.github.io/react-jsonschema-form/), using some of the [Bootswatch](http://bootswatch.com/) free themes:

![](https://i.imgur.com/1Z5oUK3.png)
![](https://i.imgur.com/IMFqMwK.png)
![](https://i.imgur.com/HOACwt5.png)

Last, if you really really want to override the semantics generated by the lib, you can always create and use your own custom [widget](advanced-customization.md#custom-widget-components), [field](advanced-customization.md#custom-field-components) and/or [schema field](advanced-customization.md#custom-schemafield) components.


## JSON Schema supporting status

This component follows [JSON Schema](http://json-schema.org/documentation.html) specs. We currently support JSON Schema-07 by default, but we also support other JSON schema versions through the [custom schema validation](https://react-jsonschema-form.readthedocs.io/en/latest/validation/#custom-schema-validation) feature. Due to the limitation of form widgets, there are some exceptions as follows:

* `additionalItems` keyword for arrays

    This keyword works when `items` is an array. `additionalItems: true` is not supported because there's no widget to represent an item of any type. In this case it will be treated as no additional items allowed. `additionalItems` being a valid schema is supported.

* `anyOf`, `allOf`, and `oneOf`, or multiple `types` (i.e. `"type": ["string", "array"]`)

    The `anyOf` and `oneOf` keywords are supported; however, properties declared inside the `anyOf/oneOf` should not overlap with properties "outside" of the `anyOf/oneOf`.

    You can also use `oneOf` with [schema dependencies](dependencies.md#schema-dependencies) to dynamically add schema properties based on input data.

    The `allOf` keyword is supported; it uses [json-schema-merge-allof](https://github.com/mokkabonna/json-schema-merge-allof) to merge subschemas to render the final combined schema in the form. When these subschemas are incompatible, though (or if the library has an error merging it), the `allOf` keyword is dropped from the schema. 

* `"additionalProperties":false` produces incorrect schemas when used with [schema dependencies](#schema-dependencies). This library does not remove extra properties, which causes validation to fail. It is recommended to avoid setting `"additionalProperties":false` when you use schema dependencies. See [#848](https://github.com/mozilla-services/react-jsonschema-form/issues/848) [#902](https://github.com/mozilla-services/react-jsonschema-form/issues/902) [#992](https://github.com/mozilla-services/react-jsonschema-form/issues/992)

## Handling of schema defaults

This library automatically fills default values defined the [JSON Schema](http://json-schema.org/documentation.html) as initial values in your form. This also works for complex structures in the schema. If a field has a default defined, it should always appear as default value in form. This also works when using [schema dependencies](#schema-dependencies).

Since there is a complex interaction between any supplied original form data and any injected defaults, this library tries to do the injection in a way which keeps the original intention of the original form data.

Check out the defaults example on the [live playground](https://mozilla-services.github.io/react-jsonschema-form/) to see this in action.

### Merging of defaults into the form data

There are three different cases which need to be considered for the merging. Objects, arrays and scalar values. This library always deeply merges any defaults with the existing form data for objects.

This are the rules which are used when injecting the defaults: 

- When the is a scalar in the form data, nothing is changed. 
- When the value is `undefined` in the form data, the default is created in the form data.
- When the value is an object in the form data, the defaults are deeply merged into the form data, using the rules defined here for the deep merge.
- Then the value is an array in the form data, defaults are only injected in existing array items. No new array items will be created, even if the schema has minItems or additional items defined.

### Merging of defaults within the schema

In the schema itself, defaults of parent elements are propagated into children. So when you have a schema which defines a deeply nested object as default, these defaults will be applied to children of the current node. This also merges objects defined at different levels together with the "deeper" not having precedence. If the parent node defines properties, which are not defined in the child, they will be merged so that the default for the child will be the merged defaults of parent and child.

For arrays this is not the case. Defining an array, when a parent also defines an array, will be overwritten. This is only true when arrays are used in the same level, for objects within these arrays, they will be deeply merged again.

## Tips and tricks

 - Custom field template: <https://jsfiddle.net/hdp1kgn6/1/>
 - Multi-step wizard: <https://jsfiddle.net/sn4bnw9h/1/>
 - Using classNames with uiSchema: <https://jsfiddle.net/gfwp25we/1/>
 - Conditional fields: <https://jsfiddle.net/69z2wepo/88541/>
 - Advanced conditional fields: <https://jsfiddle.net/cowbellerina/zbfh96b1/>
 - Use radio list for enums: <https://jsfiddle.net/f2y3fq7L/2/>
 - Reading file input data: <https://jsfiddle.net/f9vcb6pL/1/>
 - Custom errors messages with transformErrors: <https://jsfiddle.net/revolunet/5r3swnr4/>
 - 2 columns form with CSS and FieldTemplate: <https://jsfiddle.net/n1k0/bw0ffnz4/1/>
 - Validate and submit form from external control: <https://jsfiddle.net/spacebaboon/g5a1re63/>
 - Custom component for Help text with `ui:help`: <https://codesandbox.io/s/14pqx97xl7/>
 - Collapsing / Showing and Hiding individual fields: <https://codesandbox.io/s/examplereactjsonschemaformcollapsefieldtemplate-t41dn>

## FAQ

### Q: Does rjsf support `oneOf`, `anyOf`, multiple types in an array, etc.?

A: The `anyOf` and `oneOf` keywords are supported; however, properties declared inside the `anyOf/oneOf` should not overlap with properties "outside" of the `anyOf/oneOf`. We currently support a limited version of `allOf`.

In addition, "nullable" types are supported in a narrow sense: If a property declares a type of `["<some-type>", "null"]`, then `"some-type"` will be passed through as the type used to determine which widget to use for rendering the field. However, the actual rendering and handling of the field is unchanged; you are free to handle this using an approach (`'ui:emptyValue': null`, for example) best-suited to your use case.

### Q: Will react-jsonschema-form support Material, Ant-Design, Foundation, or [some other specific widget library or frontend style]?

A: Yes. We currently support Bootstrap 3 and material-ui, and we are working on adding antd and Semantic UI. We'd welcome more contributions! See more at https://react-jsonschema-form.readthedocs.io/en/latest/theme-customization/.

### Q: Is there a way to "collapse" fields, for instance to show/hide individual fields?

A: There's no specific built-in way to do this, but you can write your own FieldTemplate that supports hiding/showing fields according to user input. See the "tips and tricks" section above for one example implementation. See also: [#268](https://github.com/mozilla-services/react-jsonschema-form/issues/268), [#304](https://github.com/mozilla-services/react-jsonschema-form/pull/304), [#598](https://github.com/mozilla-services/react-jsonschema-form/issues/598), [#920](https://github.com/mozilla-services/react-jsonschema-form/issues/920).

## License

Apache 2
