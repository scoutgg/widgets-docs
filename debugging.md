# Debugging Widgets
Here you can find some debugging tricks for widgets.

## Read the current state
To check the current state of a component you can inspect the component using
either Google Chrome, Firefox or Safari.

Simply select your custom element(widget) in the element inspector, and you
will have `this` available in `$0`. Example, if you have the following component:

```js
@Component('demo')
@Attribute('name', String, {
  default: 'World',
})
@Attribute('age', Number)
@Template(function (html) {
  html `
    <p><b>Hello ${this.name}</b>. You are ${this.age || 'billion of'} years old.</p>
  `
})
export default class Hello extends HTMLElement {
  connectedCallback() {
    this.loadedAt = Date.now()
  }
}
```
First you want to find the `<demo-hello>` element and make sure it is the selected
element:
<br>
![Selecting element](https://github.com/scoutgg/widgets-docs/blob/master/assets/selected-element.png?raw=true)
<br>
Now you can start accessing values available in its `this`-context:
<br>
![Testing values](https://github.com/scoutgg/widgets-docs/blob/master/assets/testing-values.png?raw=true)

**Note**: Here you can easily see why `'billion of'` will become the default when
`this.age` is `0` (because 0 is a falsy value).

## Check if a variable is watched
All variables defined with the `@Attribute` decorator will be watched. Attributes
saved on `this`, but not defined with the `@Attribute`-decorator will not be.
In these cases you will have to invoke widgets render function to tell your
rendering engine to re-render. This is done by running `this.render()`.

Example of watched attribute:
<br>
![Watched attributes](https://github.com/scoutgg/widgets-docs/blob/master/assets/watched-attributes.gif?raw=true)
<br>
Example of triggering render:
<br>
![Render](https://github.com/scoutgg/widgets-docs/blob/master/assets/render.gif?raw=true)

Example of code where you will need to trigger render:

```js
@Component('demo')
@Template(function (html) {
  html `
    <p>What swims and starts with a T? <b>${this.punchLine}</b></p>
  `
})
export default class Joke extends HTMLElement {
  connectedCallback() {
    setTimeout(()=> {
      this.punchLine = 'Two ducks 😂'
      this.render()
    }, 3000)
  }
}
```

## Use `debugger`
Sometimes your app will grow complex and you will need to find the application's
state in some time, perhaps during loading. We recommend using the `debugger`
keyword for this. The browser will then pause the Javascript execution at that
specific point. One use case could be to check if a function ever ran.
