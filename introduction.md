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
three arguments, where the last one is optional: `@Attribute(<String>name, <mixed>Type, [<Object>Options])`

For instance a simple greeting app could be:
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
}
```

Usage of this widget would look like:
```html
<demo-hello name="John" age="34"></demo-hello>
```

Great work! Keep it up. 👍

## Create Component Helper
To quickly add a new component boilerplate, we have nifty helper for you. You
can run the following command in your terminal, and you should get a brand new
component in your `src/components` folder.
```bash
$ gulp create-component --my-component-name
```

**Note**: You will need to add an import line in the entry file(`index.js`)
in order for it to be accessible.
```js
import './components/my-component-name/my-component-name'
```

## The To Do app (You guessed it!)

Lets create a very small To Do app. If you start up a fresh boilerplate, and
create a new component called `toDoApp`:

```bash
$ gulp create-component --to-do-app
```

This will be a parent component, meaning it will control the state of its
sub-component. Which basically means it will control the todo-list.




## Redering elements
