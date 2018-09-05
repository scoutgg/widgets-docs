# Introduction

## How the boilerplate works

The boilerplate has a `src` folder where all your code goes.
Inside the `src` folder you will find an `index.js` file, which is also called
the project entry file. This is where we bootstrap(attach) widgets. This is also
where you will need to import your components to make them available in the your
website.

Your components are by default created in the `src/components` folder.

When you run `npm run serve`, the entry file will be bundled into a file
called `application.js`, which is imported in a `<script>`-tag inside `index.html`
at the root of the project.

And that's how you get the script on your page. If you want to create a bundle
on your disk, you can run the command `gulp`, which will create the `application.js`
file inside a folder called `build` in the root of the directory.

## Attributes and bindings

Since all your widgets are fancy HTML elements you can watch the values of their
attributes by using the `@Attribute` decorator. The `@Attribute` decorator takes
two arguments: `@Attribute(<String>name, <mixed>Type)`

For instance a simple greeting app could be:
```js
@Component('demo')
@Attribute('name', String)
@Attribute('age', Number)
@Template(function (html) {
  html `
    <p><b>Hello ${this.name || 'World'}</b>. You are ${this.age || 'billion of'} years old.</p>
  `
})
export default class Hello extends HTMLElement {
}
```

Usage of this widget would look like:
```html
<demo-hello name="John" age="34"></demo-hello>
```

Great work! Keep it up. 👍
## Redering elements
