---
title: "How to build a To-do list app with JavaSript"
seoTitle: "How to build a To-do list app with JavaSript"
datePublished: Sun May 14 2023 07:05:18 GMT+0000 (Coordinated Universal Time)
cuid: clhn2neax000009ms237sh5su
slug: how-to-build-a-to-do-list-app-with-javasript
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/x_OKGbxB3fA/upload/41c49b0e53b47a7d6fe53827f99f495a.jpeg
tags: css, javascript, html5, todoapp

---

Hola, thanks for reading!

In the end, this app might look like it was designed in the Stone Age. But don't worry, we're focusing more on functionality here than aesthetics. You can always improve the UI later on.

A screenshot of the to-do-app 🔽

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1684046463336/16d9870c-d434-47b5-8da2-8b737b27cacf.jpeg align="center")

Here, I'll be going through the step-by-step process of building a To-do list app with JavaScript. This is the flow:

* Start with creating the HTML structure
    
* Style the app with CSS
    
* Add JavaScript functionality to make the app interactive
    

## Prerequisites

To follow along with this tutorial, you should have a basic understanding of HTML, CSS, and JavaScript. If you're unfamiliar with these technologies, you may want to check out some introductory resources first. Here are a few free online resources that may be helpful:

* HTML: [W3Schools HTML Tutorial](https://www.w3schools.com/html/)
    
* CSS: [W3Schools CSS Tutorial](https://www.w3schools.com/css/)
    
* JavaScript: [JavaScript.info](https://javascript.info/)
    

You'll also need a text editor to write your code. There are many free text editors available, including [Visual Studio Code](https://code.visualstudio.com/), [Sublime Text,](https://www.sublimetext.com/) and [Notepad++](https://notepad-plus-plus.org/downloads/). Choose the one that works best for you. Also, you should have a web browser to test your app in. Any modern browser such as Chrome, Firefox, or Safari will work.

## Creating the HTML structure

The first step in building our app is to create the HTML structure. It will have:

* A container to hold all of our app's content.
    
* A form for adding new tasks.
    
* A list to display all tasks.
    

Here's the code:

```xml
<!DOCTYPE html>
<html>
  <head>
    <title>To-Do List App</title>
    <link rel="stylesheet" href="style.css">
  </head>
  <body>
    <div class="container">
      <h1>To-Do List</h1>
      <div class="add-task">
        <input type="text" id="input" placeholder="Add task...">
        <button id="add-btn">Add</button>
      </div>
      <ul id="task-list"></ul>
    </div>
    <script src="script.js"></script>
  </body>
</html>
```

An explanation of the code above:

* The `<div>` with a class of `"container"` is to hold all of the app's content.
    
* Inside the container, there's an `<h1>` element with the text "To-Do List App" to give the app a title.
    
* Next, there's a `form` with an `input` field and a `button`. The `input` field is where you will add new tasks and the `button` is used to submit the form and add the task to the list.
    
* Also, there's an empty `unordered` list with an `id` "task-list". This is where tasks will be displayed.
    

## Styling the app with CSS

Now that we have our HTML structure in place, it's time to style our app with CSS. We want our app to look nice and be easy to use, so we'll be using some simple styles to achieve that.

Here's the CSS code:

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Roboto', sans-serif;
}

.container {
  max-width: 500px;
  margin: 50px auto;
  padding: 20px;
  background-color: #f5f5f5;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
}

h1 {
  text-align: center;
  margin-bottom: 20px;
}

form {
  display: flex;
  margin-bottom: 20px;
}

input[type="text"] {
  flex: 1;
  padding: 10px;
  border: none;
  border-radius: 5px 0 0 5px;
  background-color: #fff;
  font-size: 16px;
  font-weight: bold;
}

button[type="submit"] {
  padding: 10px;
  border: none;
  border-radius: 0 5px 5px 0;
  background-color: #5cb85c;
  color: #fff;
  cursor: pointer;
  font-size: 16px;
  font-weight: bold;
}

button[type="submit"]:hover {
  background-color: #4cae4c;
}

ul {
  list-style-type: none;
  margin: 0;
  padding: 0;
}

li {
  padding: 10px;
  margin-bottom: 5px;
  background-color: #fff;
  border-radius: 5px;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.1);
  display: flex;
  justify-content: space-between;
  align-items: center;
}

li span {
  flex: 1;
  font-size: 16px;
  font-weight: bold;
}

li .delete {
  color: #ff4136;
  font-weight: bold;
  cursor: pointer;
  font-size: 20px;
  margin-left: 10px;
}

li .complete {
  color: #2ecc40;
  font-weight: bold;
  cursor: pointer;
  font-size: 20px;
  margin-right: 10px;
}

li.complete span {
  text-decoration: line-through;
  color: #808080;
}

.completed {
  opacity: 0.5;
}
```

Breaking down the CSS code:

* The `*` selector applies these styles to every element on the page.
    
* We set the margin and padding of every element to 0 and use the `border-box` value for the `box-sizing` property, which includes the padding and border in the element's total width and height.
    
* We set the font family to Arial and sans-serif as a fallback in case Arial is not available on the user's device.
    
* The `.container` class styles our main container element. We set a maximum width of 500px, centre it horizontally with `margin: 0 auto`, and give it some padding and background colour. We also add a border radius and box shadow to give it a nice look.
    
* The `h1` selector centres our heading text and gives it a margin-bottom of 20px.
    
* The `form` selector styles our form element. We use Flexbox to display the input and button elements side-by-side and add a margin-bottom of 20px.
    
* The `input[type="text"]` selector styles our text input element. We use Flexbox to make it take up as much space as possible, give it some padding, remove the border, and give it a border radius on the left side.
    
* The `button[type="submit"]` selector styles our submit button element. We give it some padding, remove the border, and give it a border radius on the right side. We also give it a green background colour and white text colour and change the background colour on hover.
    
* The `ul` selector removes the default list style from our unordered list element and sets the margin and padding to 0.
    
* The `li` selector styles our list item elements. We give them some padding, a margin bottom, and a white background colour. We also give them a border radius and box shadow to make them stand out. We use Flexbox to align the text and the delete button horizontally and centre them vertically.
    

## Adding JavaScript Functionality

Now, let's dive into the JavaScript functionality of our to-do list app. We'll be using JavaScript to make the app interactive and dynamic.

First, we define some variables to access elements in our HTML. We use the `getElementById` method to select the input field, the "add" button, and the task list.

```javascript
const input = document.getElementById('input');
const addButton = document.getElementById('add-btn');
const taskList = document.getElementById('task-list');
```

We then add an event listener to the "add" button to listen for a click event. When the button is clicked, the `addTask` function is called.

```javascript
addButton.addEventListener('click', addTask);
```

The `addTask` function checks if the input field is empty. If it is, an alert message is displayed to the user to enter a task. If not, a new task item is created using `createElement`, with the value of the input field as the task description.

```javascript
function addTask() {
  const task = input.value;
  if (task === '') {
    alert('Please enter a task!');
    return;
  }
  
  const taskItem = document.createElement('li');
  taskItem.innerText = task;
  // ...
}
```

We then create a checkbox input element to allow users to mark tasks as completed. We add an event listener to the checkbox to listen for a change event. When the checkbox is checked, the `completeTask` function is called.

```javascript
const checkbox = document.createElement('input');
checkbox.type = 'checkbox';
checkbox.addEventListener('change', completeTask);
taskItem.appendChild(checkbox);
```

The `completeTask` function accesses the parent element of the checkbox, which is the task item. If the checkbox is checked, the task item is marked as completed by adding the CSS class `.completed`. If it is unchecked, the `.completed` class is removed.

```javascript
function completeTask() {
  const taskItem = this.parentElement;
  if (this.checked) {
    taskItem.classList.add('completed');
  } else {
    taskItem.classList.remove('completed');
  }
}
```

Finally, we create a delete button element to allow users to delete tasks. We add an event listener to the delete button to listen for a click event. When the button is clicked, the `deleteTask` function is called.

```javascript
const deleteButton = document.createElement('span');
deleteButton.innerHTML = '&times;';
deleteButton.classList.add('delete');
deleteButton.addEventListener('click', deleteTask);
taskItem.appendChild(deleteButton);
```

The `deleteTask` function accesses the parent element of the delete button, which is the task item. The task item is then removed from the task list using the `removeChild` method.

```javascript
function deleteTask() {
  const taskItem = this.parentElement;
  taskList.removeChild(taskItem);
}
```

Here is the complete JavaScript code:

```javascript
const input = document.getElementById('input');
const addButton = document.getElementById('add-btn');
const taskList = document.getElementById('task-list');

addButton.addEventListener('click', addTask);

function addTask() {
  const task = input.value;
  if (task === '') {
    alert('Please enter a task!');
    return;
  }
  
  const taskItem = document.createElement('li');
  taskItem.innerText = task;
  
  const checkbox = document.createElement('input');
  checkbox.type = 'checkbox';
  checkbox.addEventListener('change', completeTask);
  taskItem.appendChild(checkbox);
  
  const deleteButton = document.createElement('span');
  deleteButton.innerHTML = '&times;';
  deleteButton.classList.add('delete');
  deleteButton.addEventListener('click', deleteTask);
  taskItem.appendChild(deleteButton);
  
  taskList.appendChild(taskItem);
  
  input.value = '';
}

function completeTask() {
  const taskItem = this.parentElement;
  if (this.checked) {
    taskItem.classList.add('completed');
  } else {
    taskItem.classList.remove('completed');
  }
}

function deleteTask() {
  const taskItem = this.parentElement;
  taskList.removeChild(taskItem);
}
```

With these JavaScript functions, our to-do list app is now interactive.

The complete code for this To-do app is available on my [GitHub repo](https://github.com/Tabintel/to-do-app.js).

## Conclusion

In this article, we learned how to create a simple to-do list app using HTML, CSS, and JavaScript. Focusing on the JavaScript functionality, which includes: adding, deleting, and marking tasks as complete.

I hope this article has helped introduce JavaScript features such as `getElementById`, `createElement`, and event listeners. If you want to learn more about JavaScript, here are some additional resources:

* [**MDN Web Docs**](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
    
* [**W3Schools**](https://www.w3schools.com/js/)
    
* [**JavaScript.info**](http://JavaScript.info)
    

Practice, build, and keep on learning - that is the way to go. 👨🏾‍💻🚀

Thanks a latte for reading this tutorial! ☕️ I hope you found it egg-ci-tingly helpful! 🥚 If you have any questions or comments, donut hesitate to connect with me on [LinkedIn](https://www.linkedin.com/in/ekemini-samuel-45062a1b4/) or [Twitter](https://twitter.com/realEkemini).

And if you really enjoyed this tutorial, you can [buy me a coffee](https://www.buymeacoffee.com/sekemini) and I'll be over the moon! 🌙