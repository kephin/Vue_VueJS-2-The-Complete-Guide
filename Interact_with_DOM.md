#Using Vue to interact with the DOM

|#|Catalog|
|---|---|
|1.|[Output data](./Interact_with_DOM.md#output-data)|

###Output data
1. **{{ }}** is called string interpolation. We can use this syntax to export the data property to our template.
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
2. Not only the data object though, **methods** properties can also be exported.
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
3. Vue let us to access data in the Vue instance by using **this**.

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
4. We cannot use {{ }} in any html element attribute. Vue let us use **v-bind directive** to bind to the attributes.

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
5. **Disable re-render** with **v-once** and it will kepp the original value.

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
6. To avoid **cross-site scritping(XSS)**, Vue will NOT render html elements by default. It will render text instead. But if you are pretty sure the source is safe or you've sanitized it, you can use **v-html** direcitve.

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
