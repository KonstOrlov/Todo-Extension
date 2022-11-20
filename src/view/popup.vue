<template>
  <div class="container">
    <img alt="Vue logo" src="@/assets/logo.png" class="logo">
    <input type="text"
           class="todo-input"
           placeholder="What needs to be done?"
           v-model="newTodo"
           @keyup.enter="addTodo"
    >
    <div v-show="!todos.length" class="todos-empty">Todo List empty &#9997;</div>
    <div class="todo-item_wrapper" >
      <div v-for="(todo, index) in todosFiltered" :key="todo.id" class="todo-item" >
        <div class="todo-item-left" >
          <input type="checkbox" v-model="todo.completed" >
          <div v-if="!todo.editing"
               @dblclick="editTodo(todo)"
               class="todo-item-label"
               :class="{ completed : todo.completed }"
          >
            {{ todo.title }}
          </div >
          <input v-else
                 v-focus
                 class="todo-item-edit"
                 type="text"
                 v-model="todo.title"
                 @blur="doneEdit(todo)"
                 @keyup.enter="doneEdit(todo)"
                 @keyup.esc="cancelEdit(todo)" >
        </div >
        <div class="remove-item" @click="removeTodo(index)" >
          &times;
        </div >
      </div >
    </div >

    <div class="extra-container">
      <div>
        <label>
          <input type="checkbox"
                 :checked="!anyRemaining"
                 @change="checkAllTodos"
          >
          Check All
        </label>
      </div>
      <div>{{ remaining }} items left</div>
    </div>

    <div class="extra-container">
      <div class="filters-container">
        <button class="filters-container__button" :class="{ active: filter === 'all' }" @click="filter = 'all'">All</button>
        <button class="filters-container__button" :class="{ active: filter === 'active' }" @click="filter = 'active'">Active</button>
        <button class="filters-container__button" :class="{ active: filter === 'completed' }" @click="filter = 'completed'">Completed</button>
      </div>

      <button v-if="showClearCompletedButton" @click="clearCompleted" class="filters-container__button">Clear Completed</button>

    </div>
  </div>
</template>

<script >
export default {
  name: 'TodoList',
  data() {
    return {
      newTodo: '',
      idForTodo: 0,
      beforeEditCache: '',
      filter: 'all',
      todos: []
    }
  },
  computed: {
    remaining() {
      return this.todos.filter(todo => !todo.completed).length
    },
    anyRemaining() {
      return this.remaining !== 0
    },
    todosFiltered() {
      if (this.filter === 'all') {
        return this.todos
      } else if (this.filter === 'active') {
        return this.todos.filter(todo => !todo.completed)
      } else if (this.filter === 'completed') {
        return this.todos.filter(todo => todo.completed)
      }
      return this.todos
    },
    showClearCompletedButton() {
      return this.todos.filter(todo => todo.completed).length > 0
    }
  },
  directives: {
    focus: {
      mounted(el) {
        el.focus()
      }
    }
  },
  methods: {
    addTodo() {
      if (this.newTodo.trim().length === 0) {
        return;
      }

      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false,
        editing: false
      })

      this.newTodo = '';
      this.idForTodo++
    },
    removeTodo(index) {
      this.todos.splice(index, 1)
    },
    editTodo(todo) {
      this.beforeEditCache = todo.title
      todo.editing = true;
    },
    doneEdit(todo) {
      if (todo.title.trim() === '') {
        todo.title = this.beforeEditCache;
      }
      todo.editing = false;

    },
    cancelEdit(todo) {
      todo.title = this.beforeEditCache;
      todo.editing = false
    },
    checkAllTodos() {
      this.todos.forEach(todo => todo.completed = event.target.checked)
    },
    clearCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed)
    }
  }
}
</script >

<style >
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 20px;
  font-size: 24px;
}

* {
  box-sizing: border-box;
}

.container {
  min-width: 400px;
  margin: 0 auto 20px;
  max-height: 450px;
}

.logo {
  display: block;
  margin: 20px auto;
  height: 75px;
}

.todos-empty {
  display: flex;
  justify-content: center;
  margin-bottom: 14px;
}

.todo-input {
  width: 100%;
  padding: 10px 18px;
  margin-bottom: 16px;
  font-size: 18px;
  display: flex;
  justify-content: space-between;
  border-radius: 5px;
  border: 1px solid #ccc;
}

.todo-item_wrapper{
  max-height: 200px;
  overflow-y: scroll;
}

.todo-item {
  margin-bottom: 12px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  animation-duration: 0.3s;
}

.todo-item-left {
  display: flex;
  align-items: center;
}

.todo-item-label {
  padding: 10px;
  border: 1px solid white;
  margin-left: 12px;
}

.todo-item-edit {
  font-size: 24px;
  color: #2c3e50;
  margin-left: 12px;
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
}

.todo-item-edit:hover {
  outline: none;
}

.remove-item {
  cursor: pointer;
  margin-left: 14px;
}

.remove-item:hover {
  color: black;
}

.completed {
  text-decoration: line-through;
  color: gray;
}

.extra-container{
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 16px;
  border-top: 1px solid lightgrey;
  padding-top: 14px;
  margin-bottom: 14px;
}

.active {
  background-color: lightgreen;
}

.filters-container{
  display: flex;
  gap: 10px;
}

.filters-container__button {
  font-size: 14px;
  background-color: white;
  appearance: none;
  border-radius: 5px;
  border: 1px solid #ccc;
  padding: 5px;
}

.filters-container__button:hover {
  background: lightgreen;
}

.filters-container__button:focus {
  outline: none;
}
</style >