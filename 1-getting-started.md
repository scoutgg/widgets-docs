# Getting started

## Create a Widgets project
First clone the boiler plate:
```bash
$ git clone https://github.com/scoutgg/widgets-boilerplate.git
```
Then install the dependencies, either through npm or yarn.

**NPM:**

```
$ cd widgets-boilerplate
$ npm install
```

**Yarn:**
```
$ cd widgets-boilerplate
$ yarn
```

Now you can run the project with [budo](https://www.npmjs.com/package/budo), which will open an instance of the boilerplate in your browser with watching for code changes:
```
$ npm run serve
```

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
sub-component. Which basically means it will control the todo-list. It could
look something along the lines of:

```js
import { Component, Template } from '@scoutgg/widgets'
import { wire } from 'hyperhtml'

@Component('demo')
@Template(function (html) {
  html `
    <h1>To do list</h1>
    <ul>
      ${this.todo.map(item => wire()`
        <li>
          ${item}
            <button onclick=${this.removeTask.bind(this, item)}>Done! 👌</button>
        </li>
      `)}
    </ul>
  `
})
export default class ToDoApp extends HTMLElement {
  connectedCallback() {
    this.todo = ['Remove this task', '...and this task']
  }
  addTask({ task }) {
    this.todo.push(task)
    this.render()
  }
  removeTask(task) {
    this.todo.splice(this.todo.indexOf(task), 1)
    this.render()
  }
}
```

Let's create a way of creating new tasks in our to do list, and have that done
in a sub-component of this parent.

```bash
$ gulp create-component --add-task
```

We want this widget to have an input field, a descriptive label and a button
that puts our brand new task in the list, in the parent component who controls
the state. Lets make the basic component code:

```js
import { Component, Template } from '@scoutgg/widgets'

@Component('demo')
@Template(function (html) {
  html `
    <form onsubmit=${this.save.bind(this)}>
      <label style="font-size: bold;">New task:</label>
      <input
        type="text"
        value=${this.description}
        oninput=${this.setDescription.bind(this)}
      />
      <button onclick=${this.save.bind(this)}>Add➕</button>
    </form>
  `
})
export default class AddTask extends HTMLElement {
  setTitle({ target }) {
    this.title = target.value
    this.render()
  }
  save(event) {
    // TODO: Send to parent
  }
}
```

In this component we listen to some native events, for instance `oninput` and
`onclick`. These will run functions defined in our component code.

Lets dispatch an event to the parent component, so it can be aware that we
want to make changes to the state. We do this by updating the `save`-method:

```js
save(event) {
  event.preventDefault() // Avoid real submission
  this.dispatchEvent(new CustomEvent('addTask', {
    detail: this.description,
  }))
  this.description = ''
  this.render()
}
```

In this example we use native javascript with no syntactic sugar so we see
clearly what is happening.

Notice how we run `this.render()` in the `setDescription()` and `save` methods.
By doing this we're telling widgets that it should re-render the template where
there are changes.

Now let's go back to the parent component, `toDoApp`, and import our new component
by adding

```js
import '../add-task/add-task'
```

and use `<demo-add-task></demo-add-task>` in the template. Also see that we need
to listen to the event `onaddTask`:

```html
<demo-add-task onaddTask=${this.addTask.bind(this)}></demo-add-task>
```

 A finished component could look like this:

```js
import { Component, Template } from '@scoutgg/widgets'
import { wire } from 'hyperhtml'
import '../add-task/add-task'

@Component('demo')
@Template(function (html) {
  html `
    <h1>To do list</h1>
    <demo-add-task onaddTask=${this.addTask.bind(this)}></demo-add-task>
    <ul>
      ${this.todo.map(item => wire()`
        <li>
          ${item}
            <button onclick=${this.removeTask.bind(this, item)}>Done! 👌</button>
        </li>
      `)}
    </ul>
  `
})
export default class ToDoApp extends HTMLElement {
  connectedCallback() {
    this.todo = ['Remove this task', '...and this task']
  }
  addTask({ detail }) {
    this.todo.push(detail)
    this.render()
  }
  removeTask(task) {
    this.todo.splice(this.todo.indexOf(task), 1)
    this.render()
  }
}
```

Awesome! You made a simple todo-app in widgets. 👏
