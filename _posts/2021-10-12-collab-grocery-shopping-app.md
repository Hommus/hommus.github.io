---
layout:      post
title:       Shared Grocery Shopping App
date:        2021-10-12 11:58:00 +1100 
description: A collaborative grocery shopping app.
summary:     A collaborative grocery shopping app.
categories:  project
---

I recently quit my job as a machine learning engineer to spend some time developing my own ideas and projects. The majority of these ideas require web development skills so I started with the easiest web-based project I could think of, a todo list. The collaborative grocery shopping app is a simple todo list tailored for groceries. 

<figure>
    <video poster="/assets/img/2021-10-12/v1.png" preload="auto" width="75%" title="Grocery App v1" loop autoplay playsinline muted="true" class="note-video">
        <source src="/assets/img/2021-10-12/day5.mp4" type="video/mp4">
    </video>
    <figcaption>Progress after one week of development.</figcaption>
</figure>

I had limited experience using web languages such as HTML, CSS, and Javascript so I spent the first week refreshing my knowledge on these. I also thought it would be a good idea to abstract away complex functionality such as user authentication and database management, which I did by using an existing library called [Userbase](https://userbase.com/). 

Although I did not know it at the time, setting up Userbase actually led to the implementation of the collaboration feature. Straight out of the box I had a functional app where I could edit the grocery list on one device and the data would sync instantaneously with all other devices connected. <mark>Very satisfying.</mark>

I wanted the collaborative feature so I could differentiate the app from a standard todo list. The feature allows users to simultaneously edit and complete a shared grocery list. For example, my partner and I use the app when we're shopping at different stores and I will regularly update her section of the grocery list with an item if it's out of stock in my current store.

<figure>
    <video poster="/assets/img/2021-10-12/v3.png" preload="auto" width="50%" title="Main Menu V3" loop autoplay playsinline muted="true" class="note-video">
        <source src="/assets/img/2021-10-12/day12.mp4" type="video/mp4">
    </video>
    <figcaption>Progress after two weeks of development.</figcaption>
</figure>


After one week of full-time development, I had finished the app and wanted to try recreating it using a front-end framework. A front-end framework is a scaffold for building a website. It isn't necessary, but it will help if I want to work on larger projects. As for my framework of choice, I opted for [Svelte](https://svelte.dev/), a framework that has been identified as having a shallow learning curve.

Learning a framework and recreating the project only added one additional week to the development time and most of it was spent trying to create a 'drag and drop' feature, which you can see in the video above. After cleaning up the user interface and making it mobile-friendly, I have now been using the app for several weeks and it has been surprisingly helpful! 

To finish up, I've provided code below for future reference. The code is from the first week of development and does not include any usage of Svelte. I'm hoping it will be easier to read without the addition of a framework.

<h2>Code</h2>
<h3>HTML</h3>
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="user-scalable=no, width=device-width, initial-scale=1.0, maximum-scale=1.0">
    <meta name="apple-mobile-web-app-capable" content="yes" />
    <meta name="apple-mobile-web-app-status-bar-style" content="black" />
    <meta name="apple-mobile-web-app-title" content="Grocery List">
    <link rel="apple-touch-icon" href="apple-touch-icon.png">
    <title>Grocery List</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Source+Sans+Pro:wght@600&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="index.css">
    <script type="text/javascript" src="https://sdk.userbase.com/2/userbase.js"></script>
</head>

<body>
    <!-- Loading View -->
    <div id="loading-view">Loading...</div>

    <!-- Auth View -->
    <div id="auth-view">
        <h1>Grocery List</h1>
        <h2>Login</h2>
        <form id="login-form">
            <input class="upgradedInput" id="login-username" type="text" required placeholder="Username">
            <input class="upgradedInput" id="login-password" type="password" required placeholder="Password"><br>
            <input class="upgradedInput sendIt" type="submit" value="Sign in">
        </form>
        <div id="login-error"></div>

        <h2>Create an account</h2>
        <form id="signup-form">
            <input class="upgradedInput" id="signup-username" type="text" required placeholder="Username">
            <input class="upgradedInput" id="signup-password" type="password" required placeholder="Password"><br>
            <input class="upgradedInput sendIt" type="submit" value="Create an account">
        </form>
        <div id="signup-error"></div>
    </div>

    <!-- To-dos View -->
    <div id="todo-view">
        <h1>Grocery List</h1>
        <div id="todos"></div>
        <div id="db-loading">Loading grocery list...</div>
        <div id="db-error"></div>

        <form id="add-todo-form">
            <input class="upgradedInput groceries" id="add-todo" type="text" required placeholder="Groceries">
            <input class="add" type="submit" value="+">
        </form>
        <div id="add-todo-error"></div>

        <hr>
        <input class="upgradedInput adminInput sendIt" type="button" value="Clear grocery list" id="clear-grocery-button">
        <input class="upgradedInput adminInput sendIt" type="button" value="Logout" id="logout-button">
        <div id="grocery-error"></div>
        <div id="logout-error"></div>
    </div>

    <!-- application code -->
    <script type="text/javascript" src="index.js"></script>
</body>

</html>
```

<h3>JS</h3>
```javascript
userbase.init({ appId: 'INSERT-USERBASE-APPID'})
    .then((session) => session.user ? showTodos(session.user.username) : showAuth())
    .catch(() => showAuth())
    .finally(() => document.getElementById('loading-view').style.display = 'none')

let isAdmin = false
let databaseId = ''

function handleLogin(e) {
    e.preventDefault()

    const username = document.getElementById('login-username').value
    const password = document.getElementById('login-password').value

    userbase.signIn({ username, password, rememberMe: 'local' })
        .then((user) => showTodos(user.username))
        .catch((e) => document.getElementById('login-error').innerHTML = e)
}

function handleSignUp(e) {
    e.preventDefault()

    const username = document.getElementById('signup-username').value
    const password = document.getElementById('signup-password').value

    userbase.signUp({ username, password, rememberMe: 'local' })
        .then((user) => showTodos(user.username))
        .catch((e) => document.getElementById('signup-error').innerHTML = e)
}

function handleLogout() {
    userbase.signOut()
        .then(() => showAuth())
        .catch((e) => document.getElementById('logout-error').innerText = e)
}

function showTodos(username) {
    document.getElementById('auth-view').style.display = 'none'
    document.getElementById('todo-view').style.display = 'block'

    // reset the todos view
    document.getElementById('todos').innerText = ''
    document.getElementById('db-loading').style.display = 'block'
    document.getElementById('db-error').innerText = ''

    // check if user is admin or not, and get database ID
    userbase.getDatabases().then((res) => {
        if (res.databases.length == 1) {
            isAdmin = true
            userbase.openDatabase({databaseName: 'todos', changeHandler })
            .catch((e) => document.getElementById('db-error').innerText = e)
        } else {
            isAdmin = false
            databaseId = res.databases[1].databaseId
            userbase.openDatabase({ databaseId: databaseId, changeHandler })
            .catch((e) => document.getElementById('db-error').innerText = e)
        }
    })
}

function showAuth() {
    document.getElementById('todo-view').style.display = 'none'
    document.getElementById('auth-view').style.display = 'block'
    document.getElementById('login-username').value = ''
    document.getElementById('login-password').value = ''
    document.getElementById('login-error').innerText = ''
    document.getElementById('signup-username').value = ''
    document.getElementById('signup-password').value = ''
    document.getElementById('signup-error').innerText = ''
}

function changeHandler(items) {
    document.getElementById('db-loading').style.display = 'none'

    const todosList = document.getElementById('todos')
    
    if (items.length === 0) {
        todosList.innerText = 'Add some groceries using the form below:'
    } else {
        // clear the list
        todosList.innerHTML = ''

        // render all the to-do items
        for (let i = 0; i < items.length; i++) {

            // build the todo delete button
            const todoDelete = document.createElement('button')
            todoDelete.innerHTML = '-'
            todoDelete.style.display = 'inline-block'
            todoDelete.onclick = () => {
                if (isAdmin) {
                    userbase.deleteItem({ databaseName: 'todos', itemId: items[i].itemId })
                    .catch((e) => document.getElementById('add-todo-error').innerHTML = e)
                } else {
                    userbase.deleteItem({ databaseId: databaseId, itemId: items[i].itemId })
                    .catch((e) => document.getElementById('add-todo-error').innerHTML = e)
                }
                
            }

            // build the todo checkbox
            const todoBox = document.createElement('input')
            todoBox.type = 'checkbox'
            todoBox.id = items[i].itemId
            todoBox.checked = items[i].item.complete ? true : false
            todoBox.onclick = (e) => {
                e.preventDefault()
                if (isAdmin) {
                    userbase.updateItem({ databaseName: 'todos', itemId: items[i].itemId, item: {
                        'todo': items[i].item.todo,
                        'complete': !items[i].item.complete
                    }})
                    .catch((e) => document.getElementById('add-todo-error').innerHTML = e)
                } else {
                    userbase.updateItem({ databaseId: databaseId, itemId: items[i].itemId, item: {
                        'todo': items[i].item.todo,
                        'complete': !items[i].item.complete
                    }})
                    .catch((e) => document.getElementById('add-todo-error').innerHTML = e)
                }
            }

            // build the todo label
            const todoLabel = document.createElement('label')
            todoLabel.htmlFor = items[i].itemId
            todoLabel.innerHTML = items[i].item.todo
            todoLabel.style.textDecoration = items[i].item.complete ? 'line-through' : 'none'

            // append the todo item to the list
            const todoItem = document.createElement('div')
            todoItem.className = 'groceryList'
            todoItem.appendChild(todoBox)
            todoItem.appendChild(todoLabel)
            todoItem.appendChild(todoDelete)
            todosList.appendChild(todoItem)
        }

        const deleteList = document.getElementById('clear-grocery-button')

        deleteList.onclick = () => {
            if (isAdmin) {
                for (let i = 0; i < items.length; i++) {
                    userbase.deleteItem({ databaseName: 'todos', itemId: items[i].itemId })
                    .catch((e) => document.getElementById('add-todo-error').innerHTML = e)
                }
            } else {
                for (let i = 0; i < items.length; i++) {
                    userbase.deleteItem({ databaseId: databaseId, itemId: items[i].itemId })
                    .catch((e) => document.getElementById('add-todo-error').innerHTML = e)
                }
            }
            
        }

    }
}

function addTodoHandler(e) {
    e.preventDefault()

    const todo = document.getElementById('add-todo').value

    if (isAdmin) {
        userbase.insertItem({ databaseName: 'todos', item: { 'todo': todo }})
        .then(() => document.getElementById('add-todo').value = '')
        .catch((e) => document.getElementById('add-todo-error').innerHTML = e)
    } else {
        userbase.insertItem({ databaseId: databaseId, item: { 'todo': todo }})
        .then(() => document.getElementById('add-todo').value = '')
        .catch((e) => document.getElementById('add-todo-error').innerHTML = e)
    }

}


document.getElementById('login-form').addEventListener('submit', handleLogin)
document.getElementById('signup-form').addEventListener('submit', handleSignUp)
document.getElementById('add-todo-form').addEventListener('submit', addTodoHandler)
document.getElementById('logout-button').addEventListener('click', handleLogout)

document.getElementById('todo-view').style.display = 'none'
document.getElementById('auth-view').style.display = 'none'
```

<h3>CSS</h3>
```css
*, *:before, *:after {
    box-sizing: border-box;
}

input[type=text], input[type=password], input[type=button], input[type=submit], input[type=checkbox] {
    -webkit-appearance: none;
    -webkit-border-radius: 0;
    border-radius: 3px;
}

input[type="checkbox"] {
    vertical-align: middle;
    position: relative;
    width: 4em;
    height: 4em;
    border: 1px solid #bdc1c6;
}

input[type=checkbox]:checked {
    background-color: #2196F3;
}

input[type=text], input[type=password] {
    cursor: text;
}

input, button {
    cursor: pointer;
}

body {
    display: flex;
    align-items: center;
    justify-content: center;
    margin: 0 10px;
    font-family: 'Source Sans Pro', sans-serif;
}

h1, h2 {
    margin-bottom: 0.2em;
}

hr {
    border: 0;
    height: 0;
    border-top: 1px solid rgba(0, 0, 0, 0.1);
    border-bottom: 1px solid rgba(255, 255, 255, 0.3);
}

label {
    margin-left: 7px;
    font-size: large;
}

button, .add {
    -webkit-appearance: none;
    -webkit-border-radius: 0;
    color: white;
    background: red;
    font-weight: bold;
    font-size: x-large;
    height: 2em;
    width: 2em;
    border: none;
    border-radius: 3px;
    float: right;
    margin-left: auto;
}

.add {
    background-color: #309320;
}

.upgradedInput {
    width: 100%;
    padding: 10px;
    margin: 3px 0;
    font-size: large;
    min-height: 45px;
}

.sendIt {
    color: white;
    background-color: black;
    text-transform: uppercase;
    font-size: 0.8em;
    font-weight: bold;
    border-radius: 3px;
}

.adminInput {
    width: 49%;
}

.groceries {
    width: 84%;
}

.groceryList {
    display: flex;
    align-items: center;
}

#todo-view, #auth-view {
    min-width: 350px;
}
```

<!-- <div class="images-row">
    <figure>
        <picture>
            <img alt="test image." style="width:100%;" loading="lazy" decoding="async" src="/assets/img/2021-10-12/v2.png">
        </picture>
    </figure>
    <figure>
        <picture>
            <img alt="test image." style="width:100%;" loading="lazy" decoding="async" src="/assets/img/2021-10-12/v3.png">
        </picture>
    </figure>
</div> -->

