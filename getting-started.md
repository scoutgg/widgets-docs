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

#### @Component
```
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
```
import {Component, Attribute} from 'ftw/lib'

@Component('namespace')
@Attribute('title', String)
class A extends HTMLElement {
  /* code goes here */
}

// what attribute does does
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
```
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

```
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

## Create a Widgets project

