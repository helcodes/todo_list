<template>
  <div class="wrapper">
    <div id="newtodo">
      <input type="text" name="todo-text" v-model.trim="newTodoText"
        class="todo-text" placeholder="New todo"/>
      <datepicker v-on:update="dateUpdated"/>
      <button class="todo-add-button" v-on:click="addTodo()">Add</button>
    </div>

    <ul v-if="todos.length">
      <li class="todo">
        <span class="todo-text list-header">Todo</span>
        <span class="todo-date list-header">Due Date</span>
        <span class="todo-empty-button list-header"></span>
        <span class="todo-empty-button list-header"></span>
      </li>

      <li class="todo" v-for="todo in todos" :key="todo.id">
        <!--<span>{{todo.text}}</span>
        <button v-on:click="removeTodo(todo)">Remove</button>-->
        <todo :todo="todo" v-on:remove="removeTodo(todo)" v-on:done="doneTodo(todo)"/>
      </li>
    </ul>
    <p class="none" v-else>Add a new todo in the input above</p>
  </div>
</template>


<script>
import datepicker from './components/datepicker.vue'
import todo from './components/todo.vue'
import API from './api.js'
const apiUrl='http://localhost:8081/todos'

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

  mounted(){
    this.api=new API(apiUrl)
    this.getAllTodos()
  },
  methods: {
    getAllTodos(){
      this.todos=[]
      this.api.getAllTodos()
      .then(data => {
        for (let d of data) {
          this.todos.push(d)
        }
        this.todos.sort((todoA,todoB) => -todoA.date.diff(todoB.date))
      })
    },

    addTodo () {
      // check if this.newTodoText is set (prevent adding of empty items)
      if (this.newTodoText) {
        // this.todos.push() adds a new entry to the back of a list
        // a todo item consists of the text and an id, and we will simply use the current time for the id
        this.api.addTodo({
            text: this.newTodoText,
            date: this.newTodoDate,
            done: false
        }).then(todo => {
          this.todos.push(todo)
          this.todos.sort((todoA,todoB) => -todoA.date.diff(todoB.date))
          // after addind the todo item, clear the text
          this.newTodoText = ""
        })
      }
    },
    removeTodo (item) {
      // the function gets a todo item as an argument
      // this.todos.filter() filters out all occurences of this item from the list this.todos
      // we simply re-assign the filtered list of todos to list.todos
      this.todos = this.todos.filter((_item) => _item !== item)
      this.api.removeTodo(item.id)
    },
    doneTodo(todo){
      this.api.updateTodo(todo)
    },
    dateUpdated (date) {
      this.newTodoDate=date.clone()
    }
  },
};
</script>



<style>
html,
body {
  font: 16px/1.2 BlinkMacSystemFont, -apple-system, "Segoe UI", Roboto,
    Helvetica, Arial, sans-serif;
  padding: 10px;
}
.wrapper {
  width: 75%;
  margin: 0 auto;
}
.newtodo {
  display: flex;
  width: 100%;
}

.todo {
  display: flex;
  width: 100%;
}

.list-header{
  text-align: center;
  padding: 5px;
  margin-left: 2px;
  margin-right: 2px;
  background-color: #eee;
  color: black;
}
.todo-text {
  flex: 4;
  text-align: center;
}
.todo-date {
  flex: 3;
  text-align: center;
}
.todo-add-button {
  flex: 1;
  border: 1px solid green;
  background: green;
  color: white;
  font-size: 0.8rem;
  padding: 2px 4px;
  cursor: pointer;
  margin-left: 2px;
  margin-right: 2px;
  outline: none;
}
.todo-remove-button {
  flex: 1;
  border: 1px solid red;
  background-color: red;
  color: white;
  font-size: 0.8rem;
  padding: 2px 4px;
  cursor: pointer;
  margin-left: 2px;
  margin-right: 2px;
}
.todo-empty-button {
  flex: 1;
  padding: 2px 4px;
  margin-left: 2px;
  margin-right: 2px;
}
ul, li {
  list-style-type: none;
  margin: 0;
  padding: 0;
}
ul {
    margin-top: 40px;
}
button {
  border: 1px solid green;
  background: green;
  color: white;
  font-size: 0.8rem;
  padding: 2px 4px;
  cursor: pointer;
}
p.none {
  color: #888;
  font-size: small;
}
</style>