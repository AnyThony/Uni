# About
Uni is a lightweight Javascript framework for building UI

- State management
- Components
- Parent-based In-line scripting

#### Refer to the documentation [here](https://anythony.github.io/uni-docs/)

# Setup

Uni is available to install as an npm package:
```npm install -g uni-cmd```
        
A new project can then be created with the init command:
```uni init Project_Name```

Inside a project directory, a dev environment with live reload can be started:
```npm run dev```

# In-line Scripting

Inline-scripts that run under a DOM element as context:

```js
<div>
  {
    this.children[0].innerText += "Polo!";
    this.children[1].innerText += "bar";
    this.querySelector("#x").innerText = "y";
  }
  <span>Marco</span>
  <span>foo</span>
  <span id="x"></span>
</div>
```

# Initialization Events

Scripts run in the order of an execution tree

Every DOM element runs itself first then recurses on its children.

The following are events that can be assigned upon initialization:

### onFullLoad
```js
<div>
  {
    this.onFullLoad = () => {
      // called after every descendant has loaded
    }
  }
  ...
</div>
``` 
Assigning a callback to onFullLoad will call it once every descendent's script is ran.

### onChildLoad
```js
<div>
  {
    this.onChildLoad = (child) => {
      console.log(child + " has loaded");
      // called when a descendant loads
    }
  }
  ...
</div>
```
Assigning a callback to onChildLoad will call it everytime a child's script is ran.

# Components

You can split views into components

Component files are automatically registered in the directory src/components.
The file name must be the desired component name. (In this case it's navbar.uni)
```js        
<template>
  <div class="navbar">
    {
      // blah
    }
    ...
  </div>
</template>
```
To use a component, this.imports must be declared with its name inside the parent element.

You can then declare a regular HTML element with its tagname:
```js
<body>
  {
    this.imports = ["navbar"];
  }
  <navbar></navbar>
</body>
```
      
Components can be added dynamically with this.addComponent(name):

### addComponent(name)
```js       
<body>
  {
    this.imports = ["navbar"];
    this.addComponent("navbar");
  }
</body>
```

addComponent appends a new component as a child into the script context.

# Props
Data known as props can be passed to reusable components upon initialization.

For components initialized statically, props can be given through regular html attribute:

        
```js
<body>
  {
    this.imports = ["navbar"];
  }
  <navbar title="foo"></navbar>
</body>
```

      
For components initialized dynamically, props can be given as an object through addComponent's second optional parameter.

        
```js
<body>
  {
    this.imports = ["navbar"];
    this.addComponent("navbar", {title: "foo"});
  }
</body>
```

      
And finally, accessing props from the component end is done with this.props:

        
```js
<template>
  <div class="navbar">
    {
      console.log("My title:", this.props.title);
    }
  </div>
</template>
```

# State Management
Uni respects the DOM tree's hierarchy when processing state management

Declaring an initial state must be done on this.state:
```js        
<div>
  {
    this.state = {foo: 2}
  }
</div>
```
      
### bindState(callback)
```js
<div>
  {
    this.state = {foo: 2}
    this.bindState(newState => {
      console.log("foo is " + newState.foo);
    });
  }
  ...
</div>
```
      
The callback passed to bindState will be called once initially then upon any state changes from setState thereafter.

### setState(object)
```js        
<div>
  {
    this.state = {foo: 2}
    this.bindState(newState => {
      console.log("foo is " + newState.foo);
    });
    this.setState({foo: 3});
  }
  ...
</div>
```
      
The callback passed to bindState will be called once initially then upon any state changes from setState.

## State methods within descendants
Descendants that have ancestor(s) with a defined initial state can also use these methods:

```js    
<div>
  {
    this.state = {foo: 2}
    this.bindState(newState => {
      console.log("foo is " + newState.foo);
    });
  }
  <div id="child">
    {
      this.setState({foo: 3})
    }
  </div>
</div>
```
      
Descendants can setState to multiple ancestors with differing state attributes:

```js
<div id="ancestor">
  {
    this.state = {foo: 2}
    this.bindState(newState => {
      console.log("foo is " + newState.foo);
    });
  }
  <div id="child">
    {
      this.state = {bar: 3}
      this.bindState(newState => {
        console.log("bar is " + newState.bar);
      }); 
    }
    <div id="descendant">
      {
        this.setState({foo: 3, bar: 4});
      }
    </div>
  </div>
</div>
```
