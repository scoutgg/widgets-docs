# Getting started

## Installation
### NPM
```npm install --save @scoutgg/widgets```

Import Widgets as a module in your project:

```require('@scoutgg/widgets')```

### RawGit
If you want to load Widgets directly to your project without installing it, you can add it like this:

```import { bootstrap, define, Component, Attribute, Template } from 'https://rawgit.com/scoutgg/widgets/master/esm/index.js'```

## Online playground

## What is Widgets

### What is Web Components

## Create a Widgets project

## How it works
For a bystander this might seem a bit magical, but fortunately it's just clever code. Let's go briefly through it and see what it does.

#### @Component
```js
import {Component, Attribute} from 'ftw/lib'

@Component('namespace')
class A extends HTMLElement {
  /* code goes here */
}


const widgetsInterface = {/* more on this later */}
// what @Component does

function Component(namespace) {
  return function define(Class) {
    // add namespace to class name so its a valid custom element name
    const tagName = `${namespace}-${Class.name}`

    /* skip lots of polyfill code */

    // add widgets methods
    Object.assign(Class.prototype, widgetsInterface)

    // custom elements v1 has changed how to register elements
    customElements.define(tagName, Class)

    // return helper for creating instances
    return createElement.bind(tagName)
  }
}
```

#### @Attribute
When you set an attribute with `@Attribute` in Widgets you set the name and Type, which is being being added to an array and watched by the browser.

```js
import {Component, Attribute} from 'ftw/lib'

@Component('namespace')
@Attribute('title', String)
class A extends HTMLElement {
  /* code goes here */
}

// what attribute does
function Attribute(name, Type) {
  return function define(Class) {
    // tell the browser to watch attribute
    if(!Class.observedAttributes) Class.observedAttributes = []
    Class.observedAttributes.push(name)

    /* create getter/setter for attribute
    ** uses different getters/setters for different types.
    ** by default it just does this
    */
    Object.defineProperty(Class.prototype, name, {
      get() {
        // type may be any kind of function
        // that does some transform to value like moment
        return Type(this.getAttribute('name'))
      },
      set(value) {
        this.setAttribute(name, value)
      }
    })
  }
}
```

#### @Template

```js
import {Component, Template} from 'ftw/lib'
// https://github.com/Matt-Esch/virtual-dom
import {h} from 'virtual-dom'

// function that returns virtual dom tree
function template() {
  return [
    h('article', [
      h('header', [ h('h1', this.title) ]),
      h('section',  [ h('content') ]),
      h('footer', [ h('i', this.author) ])
    ])
  ]
}

@Component('namespace')
@Template(template)
class A extends HTMLElement {
  /* code goes here */
}

// what template does
function Template(template) {
  return function define(Class) {
    /* pretty much all it does really */
    Class.prototype.template = template
  }
}
```

#### Widgets interface and lifecycles

```js
const lifecycle = {
    constructor() {
      /* setup */
    },
    connectedCallback() {
      /* start */
    },
    attributeChangedCallback() {
      /* react to changes */
    }
    disconnectedCallback() {
      /* cleanup */
    }
}

const widgetsInterface = Object.assign(lifecycle, {
    render() {
      /* update view */
    },
    update() {
      /* update data */
    },
    emit() {
      /* create event */
    }
})
```

##### The lifecycle constructor
```js
const lifecycle = {
   /* in the old web component spec
   ** this used to be called createdCallback
   ** now its just a regular constructor
   */
   constructor() {
        // custom elements v1 changed how to create shadow root
        this.attachShadow({ mode: 'open' })

        // but we preserve the old behaviour so that it still works
        if(this.created) this.created()
    }
}
```

##### Connected and disconected callback
```js
const lifecycle = {
   /*
   ** In the old web component spec
   ** this used to be called attachedCallback
   */
   connectedCallback() {
        /*
        ** First render fails if the template is not properly guarded!!
        */
        this.render(done => {
            // mark element as attached
            this.$$isAttached = true
            // do component specific setup with same callback as before
            if(this.attached) this.attached()
            // render a second time to make sure we are up to date
            this.render()
        })
    }
    /*
    ** In the old web component spec
    ** this used to be called detachedCallback
    */
    disconnectedCallback() {
      // backport
      if(this.detached) this.detached()
    }
 }
```

##### Attribute changed callback
```js
let changed = null
function applyChanges() {
  for(const [element, changes] of changed) {
    for(const [key, [value, old]] of changes) {
      // notify component listening for spesific change
      if(element[`${key}Changed`]) element[`${key}Changed`](value, old)
    }
    // notify component of changes
    if(element.changed) element.changed(changes)
  }
  // make sure to update view afterwards
  element.render()
}

const lifecycle = {
   attributeChangedCallback(attr, oldValue, newValue) {
     if(!changed) {
        changed = new Map()
        // shedule change later
        requestAnimationFrame(applyChanges)
     }
     if(!changed.has(this)) {
        changed.set(this, new Map())
     }
     // register a new change
     changed.get(this).set(attr, [newValue, oldValue])
   }
}
```

##### Render
```js
import {h, diff, patch} from 'virtual-dom'

const cache = new WeakMap()

const widgetsInterface = {
   render(callback) {
     // sync render with next frame render
     requestAnimationFrame(delta => {
         // get the previous template or just an empty shadowroot
         const previous = cache.get(this) || h('#shadowRoot')
         // run the template function for latest vdom
         const current = h('#shadowRoot'. this.template(this))
         // calculate view changes
         const changes = diff(previous, current)
         // apply updates
         patch(this.shadowRoot, changes)
         // update cache
         cache.set(this, current)
         // tell the component you are done rendering
         if(callback) callback()
     })
   }
}
```

##### Update
```js
const widgetsInterface = {
   update(promises) {
     if(!Array.isArray(promises)) promises = [promises]

     /* bookkeeping for ft-loading */

     // await data load completion
     return Promise.all(promises)
        .then(data => {
          // assign response to component
          Object.assign(this, ...data)
          // update view
          this.render()
        })
   }
}
```

##### Emit
```js
const widgetsInterface = {
   emit(name, data, opts={}) {
    // create an event that bubbles and goes through shadow boundaries
    const event = new CustomEvent(name, bubbles: true, composed: true, ...opts})

    // assign all event data to the vent object

    Object.assign(event, data)

    // send the event out into the world
    this.dispatchEvent(event)
   }
}
```
