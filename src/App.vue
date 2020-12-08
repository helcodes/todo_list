<template>
    <div class="wrapper">
    <div id="newtodo">
      <input type="text" name="todo-text" v-model.trim="newTodoText"
        class="todo-text" placeholder="New todo"/>
      <datepicker v-on:update="dateUpdated"/>
      <button class="toto-add-button" v-on:click="addTodo()">Add</button>
    </div>

    <ul>
      <li class="todo">
        <span class="todo-text list-header">Todo</span>
        <span class="todo-date list-header">Due Date</span>
        <span class="todo-empty-button list-header"></span>
      </li>

      <li class="todo" v-for="todo in todos" :key="todo.id">
        <!--<span>{{todo.text}}</span>
        <button v-on:click="removeTodo(todo)">Remove</button>-->
        <todo :todo="todo" v-on:remove="removeTodo(todo)"/>
      </li>
    </ul>
    <!--p class="none" v-else>Add a new todo in the input above</p-->
  </div>
</template>


<script>
import datepicker from './components/datepicker.vue'
import todo from './components/todo.vue'
export default {
  name: 'App',
  components: {
    datepicker,
    todo
  },
  data () {
    return {
        newTodoText: "",
        newTodoDate: "",
        todos: [],
    }
  },
  methods: {
    addTodo () {
      // check if this.newTodoText is set (prevent adding of empty items)
      if (this.newTodoText) {
          // this.todos.push() adds a new entry to the back of a list
          // a todo item consists of the text and an id, and we will simply use the current time for the id
          this.todos.push({
              text: this.newTodoText,
              date: this.newTodoDate,
              id: Date.now(),
              done: false
          })
          this.todos.sort((todoA,todoB) => -todoA.date.diff(todoB.date))
          // after addind the todo item, clear the text
          this.newTodoText = ''
      }
    },
    removeTodo (item) {
      // the function gets a todo item as an argument
      // this.todos.filter() filters out all occurences of this item from the list this.todos
      // we simply re-assign the filtered list of todos to list.todos
      this.todos = this.todos.filter(_item => _item !== item)
    },
    dateUpdated (date) {
      this.newTodoDate=date.clone()
    }
  }
}
</script>



<style>
*, *::before, *::after {
  box-sizing: border-box;
}

html, body {
  font: 16px/1.2 BlinkMacSystemFont, -apple-system, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  padding: 10px;
}

.wrapper {
  width: 75%;
  margin: 0 auto;
}

#newtodo {
  width: 100%;
}

input {
  width: 90%;
}

button {
  border: 1px solid green;
  background: green;
  color: white;
  font-size: 0.8rem;
  padding: 2px 4px;
  cursor: pointer;
  width: 10%;
}


form #todo-text {
  width: 100%;
  padding: 10px;
  border: 1px solid #777;
}

ul, li {
  margin: 0;
  padding: 0;
}

p.none {
  color: #888;
  font-size: small;
}

.todo li {
  display: flex;
  margin: 5px 0;
}

.todo li span {
  flex: 1;
}


.todo li button {
  border: 1px solid red;
  background: red;
  color: white;
  font-size: 0.8rem;
  padding: 2px 4px;
  cursor: pointer;
}
</style>