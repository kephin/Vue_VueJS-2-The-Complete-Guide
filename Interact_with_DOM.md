# Using Vue to interact with the DOM

|#|Catalog|
|---|---|
|1.|[Output data](./Interact_with_DOM.md#output-data)|
|2.|[Listen to events](./Interact_with_DOM.md#listen-to-events)|
|3.|[Dynamic styling](./Interact_with_DOM.md#dynamic-styling)|

### Output data

**{{ }}** is called string interpolation. We can use this syntax to export the data property to our template.

```html
<!-- html -->
<div id="app">
  <p>{{ title }}</p>
</div>
```

```javascript
//js
const app = new Vue({
  el: '#app',
  data: {
    title: 'hello world',
  },
});
```

Not only the data object though, **methods** properties can also be exported.

```html
<!-- html -->
<div id="app">
  <p>{{ greeting() }}</p>
</div>
```

```javascript
//js
const app = new Vue({
  el: '#app',
  data: {},
  methods: {
    greeting(){
      return 'hello world';
    },
  },
});
```

Vue let us to access data in the Vue instance by using **this**.

```html
<!-- html -->
<div id="app">
  <p>{{ greeting() }}</p>
</div>
```

```javascript
//js
const app = new Vue({
  el: '#app',
  data: {
    title: 'hello world',
    },
  methods: {
    greeting(){
      return this.title;
    },
  },
});
```

We cannot use {{ }} in any html element attribute. Vue let us use **v-bind directive** to bind to the attributes.

```html
<!-- html -->
<div id="app">
  <a v-bind:href="link">My github</a>
  <!-- or just use semicolon(:) as abbreviation -->
  <a :href="link">My github</a>
</div>
```

```javascript
//js
const app = new Vue({
  el: '#app',
  data: {
    link: 'http://www.github.com/kephin/',
  },
});
```

**Disable re-render** with **v-once** and it will kepp the original value.

```html
<!-- html -->
<div id="app">
  <!-- will keep 'hello world' initially, not change to 'hello kephin'-->
  <h1 v-once>{{ title }}</h1>
  <!-- will display 'hello kephin' -->
  <p> {{ greeting() }}</p>
</div>
```

```javascript
//js
const app = new Vue({
  el: '#app',
  data: {
    title: 'hello world',
  },
  methods: {
    greeting() {
      this.title = 'hello kephin'
      return this.title;
    },
  },
});
```

To avoid **cross-site scritping(XSS)**, Vue will NOT render html elements by default. It will render text instead. But if you are pretty sure the source is safe or you've sanitized it, you can use **v-html** direcitve.

```html
<!-- html -->
<div id="app">
  <!-- will show the link of text, not the html element-->
  {{link}}
  <!-- will show the html element -->
  <p v-html='htmlLink'></p>
</div>
```

```javascript
//js
const app = new Vue({
  el: '#app',
  data: {
    link: 'http://google.com',
    htmlLink: '<a href="http://google.com">Google</a>',
  },
});
```

### Listen to events

Listening to events from the templates: **v-on:DOM-event**

```html
<!-- html -->
<div id="app">
  <button v-on:click='increase'>Click me!</button>
  <!-- or just use at(@) as abbreviation -->
  <button @click='increase'>Click me!</button>
  <p>{{ counter }}</p>
</div>
```

```javascript
//js
const app = new Vue({
  el: '#app',
  data: {
    counter: 0,
  },
  methods: {
    increase() {
      this.counter++;
    },
  },
});
```

Getting event data from the **[event object](http://www.w3schools.com/jsref/dom_obj_event.asp)**

```html
<!-- html -->
<div id="app">
  <p v-on:mousemove='updateCoordinates'>Coordinates: ({{ x }}, {{ y }})</p>
</div>
```

```javascript
//js
const app = new Vue({
  el: '#app',
  data: {
    x: 0,
    y: 0,
  },
  methods: {
    updateCoordinates(evt) {
      this.x = evt.clientX;
      this.y = evt.clientY;
      },
  },
});
```

Passing **both** your **own arguments** and the **event object** by *$event*

```html
<!-- html -->
<div id="app">
  <!-- $event is the preserved name -->
  <button v-on:click='increase(2, $event)'>Click me!</button>
  <p>{{ counter }}</p>
</div>
```

```javascript
//js
const app = new Vue({
  el: '#app',
  data: {
    counter: 0,
  },
  methods: {
    increase(step, evt) {
      this.counter = step * evt.clientX;
    },
  },
});
```

Modifying events - with [event modifiers](https://vuejs.org/v2/guide/events.html#Event-Modifiers). For example, if we want to stop propagae some events:

```html
<!-- html -->
<div id="app">
  <p v-on:mousemove='updateCoordinates'>Coordinates: ({{ x }}, {{ y }})
    <span v-on:mousemove.stop=''>DEAD SPOT</span>
  </p>
</div>
```

Listening to keyboard events - with [key modifiers](https://vuejs.org/v2/guide/events.html#Key-Modifiers)

```html
<!-- html -->
<div id="app">
  <input type="text" v-on:keyup.enter='alertMe'>
</div>
```

Using **v-model** for two-way-binding

```html
<!-- html -->
<div id="app">
  <div id="app">
  <input type="text" v-model='name'>
  <p> {{ name }}</p>
</div>
</div>
```

```javascript
//js
const app = new Vue({
  el: '#app',
  data: {
    name: '',
  },
});
```

You can put any valid JavaScript code in all the places where you can access Vue instance as long as it is only one expression and doesn't contain if-else statements or for-loops.

```html
<!-- html -->
<div id="app">
  <p>{{ x > 0 ? x : 0 }}</p>
  <button v-on:click='x++'>plus one</button>
</div>
```

[**Computed properties**](https://vuejs.org/v2/guide/computed.html#Computed-Caching-vs-Methods): the difference between computed properties and methods is that computed properties are **cached based on their dependencies**.

A computed property will only re-evaluate when some of its dependencies have changed. As long as its dependencies has not changed, it will immediately return the previously computed result without having to run the function again.

In comparison, a method invocation will always run the function whenever a re-render happens.

```html
<div id="app">
  <button v-on:click='counter++'>increase</button>
  <button v-on:click='counter--'>decrease</button>
  <button v-on:click='value++'>haha</button>
</div>
```

```javascript
const app = new Vue({
  el: '#app',
  data: {
    counter: 0,
    value: 0,
  },
  computed: {
    // this will NOT run when push haha button
    output(){
      return this.counter > 5 ? 'Greater than 5' : 'Not Greater than 5';
    },
  },
  methods: {
    // this will run when push haha button
    result(){
      return this.counter > 5 ? 'Greater than 5' : 'Not Greater than 5';
    },
  },
});
```

[**Computed setter**](https://vuejs.org/v2/guide/computed.html#Computed-Setter): computed properties are by default getter-only, but you can also use setter:

```javascript
computed: {
  fullName: {
    // getter
    get() {
      return `this.firstName this.lastName`;
    },
    // setter
    set(newValue) {
      const names = newValue.split(' ');
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    },
  },
},
```

[**Watchers**](https://vuejs.org/v2/guide/computed.html#Watchers): useful when you want to perform asynchronous or expensive operations in response to changing data.

```javascript
new Vue({
  el: '#exercise',
  data: {
    value: 0,
  },
  computed: {
    result() {
      return this.value >= 37 ? 'done' : 'not there yet';
    },
  },
  watch: {
    result(){
      setTimeout(() => {
        this.value = 0;
      }, 5000)
    }
  },
});
```

### Dynamic styling

**With CSS classes**

[**Object syntax**](https://vuejs.org/v2/guide/class-and-style.html#Object-Syntax)

```html
<!-- html -->
<div id="app">
  <div
    class="demo"
    @click="attachRed = !attachRed"
    :class="{red: attachRed}">
  </div>
</div>
```

```CSS
/*css*/
.demo {
  display: inline-block;
  background-color: #aaa;
  height: 100px;
  width: 100px;
  margin: 10px;
}

.red {
  background-color: red;
}

.blue {
  background-color: blue;
}
```

```javascript
//javascript
new Vue({
  el: '#app',
  data: {
    attachRed: false,
  },
});
```

As the CSS class object grows, we can create a computed property

> Object syntax is often used in conjunction with computed properties that return objects

```html
<!-- html -->
<div id="app">
  <div
    class="demo"
    @click="attachRed = !attachRed"
    :class="divClasses">
  </div>
</div>
```

``` javascript
//javascript
new Vue({
  el: '#app',
  data: {
    attachRed: false,
  },
  computed: {
    divClasses() {
      return {
        red: this.attachRed,
        blue: !this.attachRed,
      };
    },
  },
});
```

Or we can use [**Array syntax**](https://vuejs.org/v2/guide/class-and-style.html#Array-Syntax)

```html
<!-- html -->
<div class="demo" :class="[color, shape]"></div>
```

```javascript
//javascript
data: {
  color: 'red',
  shape: 'normal',
}
```

Or use the object syntax inside array syntax

```html
<!-- html -->
<div :class="[{ active: isActive }, errorClass]">
```

**With inline styles**

Object Syntax: We can bind inline styles, too

```html
<!-- html -->
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
<div :style="styleObject"></div>
```

```javascript
//javascript
data: {
  activeColor: 'red',
  fontSize: 30,
},
computed: {
  styleObject: {
    color: 'red',
    fontSize: '13px',
  },
},
```

Array Syntax: put objects inside

```html
<!-- html -->
<div :style="[baseStyles, errorStyles]">
```

```javascript
//javascript
data: {
  baseStyles: {
    width: '10px',
    height: '30px',
  },
  errorStyles: {
    backgroundColor: 'red',
    fontSize: '13px',
  },
},
```
