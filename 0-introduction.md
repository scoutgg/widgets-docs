# Introduction

## What is Widgets
Widgets by Scout Gaming Group is a small WebComponent library web apps. Widgets was developed in-house by Scout Gaming Group and is used in our products. By using the widgets library you will be able to efficiently create custom HTML elements with [shadowDOM](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_shadow_DOM). This library is an extracted version so you can make brilliant apps faster and more reliable. Have fun!

### What is Web Components
Web components are a set of web platform APIs that allow you to create new custom, reusable, encapsulated HTML tags to use in web pages and web apps. Custom components and widgets build on the Web Component standards, will work across modern browsers, and can be used with any JavaScript library or framework that works with HTML.

You can find more information about WebComponents visit [webcomponents.org](https://www.webcomponents.org/introduction).

### This is a Widget
The basic boiler plate for a Widgets is:

```js
import { Component, Template } from '@scoutgg/widgets'

@Component('demo')
@Template(function (html) {
  html `
    <h1>☕Fresh new component «hello-world»</h1>
  `
})
export default class HelloWorld extends HTMLElement {
}
```

1. Import uses widgets decorators
`@Component` is the fundamental decorator. This takes a String for the namespace of the component. You can have what ever namespace you like, however a namespace is required by the custom elements standard.
2. `@Template` is a decorator that compiles your template. In this example we used [HyperHTML](https://github.com/WebReflection/hyperHTML) which is a fast & light virtual DOM alternative. It provides some extra utilities like javascript directly in the template.
3. The Class Name will be the name of  your component, and the code in the class will be accessible inside the template.

The namespace and your class name will together form the complete tag-name of your brand new widget: `<demo-hello-world></demo-hello-world>`

**Note:** In the JavaScript file we use camelCase for the class-name, which will be kebab-cased in the template.

## Installation
### NPM
`npm install --save @scoutgg/widgets`

### RawGit
If you want to load Widgets directly to your project without installing it, you can add it like this:

```js
import { bootstrap, define, Component, Attribute, Template } from 'https://rawgit.com/scoutgg/widgets/master/esm/index.js'
```

Good job!☕ &nbsp;Let's take a look at starting from a boilerplate!
