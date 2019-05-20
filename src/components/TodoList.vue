// todo - decompose into components
// todo - make checkbox in fixed spot and bottom line not all the way
// todo - understand native events html + vue events
// todo - understand all scss in here

<template>
  <div>
    <h2>Todo List</h2>
    <input
      type="text"
      ref="input"
      class="new-todo-input"
      placeholder="Insert your new todo"
      v-model="newTodo"
      @keyup.enter="addTodo"
    />
    <transition-group
      name="fade"
      enter-active-class="animated fadeInUp"
      leave-active-class="animated fadeOutDown"
    >
      <todo-item
        v-for="(todo, index) in todosFiltered"
        :key="todo.id"
        :todo="todo"
        :index="index"
        :check-all="!anyRemaining"
        :removeTodo="removeTodo"
        :editTodo="editTodo"
        :doneEdit="doneEdit"
        :cancelEdit="cancelEdit"
      >
      </todo-item>
    </transition-group>

    <div class="status-bar">
      <div>
        <label>
          <input
            type="checkbox"
            :checked="!anyRemaining"
            @change="checkAllTodos"
          />
          Check all
        </label>
      </div>
      <div>{{ remaining }} todos left</div>
    </div>

    <div class="status-bar">
      <div>
        <button :class="{ active: filter == 'all' }" @click="filter = 'all'">
          All
        </button>
        <button
          :class="{ active: filter == 'active' }"
          @click="filter = 'active'"
        >
          Active
        </button>
        <button
          :class="{ active: filter == 'completed' }"
          @click="filter = 'completed'"
        >
          Completed
        </button>
      </div>

      <div>
        <transition name="fade">
          <button v-if="showClearCompletedBtn" @click="clearCompleted">
            Clear Completed
          </button>
        </transition>
      </div>
    </div>
  </div>
</template>

<script>
import TodoItem from "./TodoItem";

export default {
  name: "todo-list",
  components: {
    TodoItem
  },
  data() {
    return {
      newTodo: "",
      idForTodo: 0,
      titleBeforeEdit: "",
      todos: [],
      filter: "all"
    };
  },

  computed: {
    remaining() {
      return this.todos.filter(todo => !todo.completed).length;
    },

    anyRemaining() {
      return this.remaining != 0;
    },

    todosFiltered() {
      if (this.filter == "all") {
        return this.todos;
      } else if (this.filter == "active") {
        return this.todos.filter(todo => !todo.completed);
      } else if (this.filter == "completed") {
        return this.todos.filter(todo => todo.completed);
      }
    },

    showClearCompletedBtn() {
      return this.todos.filter(todo => todo.completed).length > 0;
    }
  },

  methods: {
    addTodo() {
      if (this.newTodo.trim().length != 0) {
        this.todos.push({
          id: this.idForTodo,
          title: this.newTodo,
          completed: false,
          editing: false
        });

        this.newTodo = "";
        this.idForTodo++;
      }
    },

    removeTodo(index) {
      this.todos.splice(index, 1);
    },

    editTodo(index) {
      this.todos[index].titleBeforeEdit = this.todos[index].title;
      this.todos[index].editing = true;
    },

    doneEdit(index) {
      if (this.todos[index].title.trim() == "") {
        this.todos[index].title = this.todos[index].titleBeforeEdit;
      }
      this.todos[index].title = event.target.value;
      this.todos[index].editing = false;
    },

    cancelEdit(index) {
      this.todos[index].title = this.todos[index].titleBeforeEdit;
      this.todos[index].editing = false;
    },

    checkAllTodos(event) {
      this.todos.forEach(todo => (todo.completed = event.target.checked));
    },

    clearCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed);
    },
  }
};
</script>

<style lang="scss">
// todo fade in and out transitions import
@import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.5.2/animate.min.css");

.new-todo-input {
  width: 50%;
  padding: 10px 18px;
  font-size: 15px;
  margin-bottom: 16px;

  &:focus {
    outline: 0;
  }
}

.todo-item {
  margin-bottom: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  animation-duration: 0.3s;
}

.remove-item {
  cursor: pointer;
  margin-left: 14px;
  &:hover {
    color: red;
  }
}

.todo-item-textbox {
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
  border: 1px solid #ccc; // overide default
  font-family: "Avenir", Arial, Helvetica, sans-serif;

  &:focus {
    outline: none;
  }
}

.todo-item-completed {
  padding: 10px;
  border: 1px solid white;
  margin-left: 12px;
  text-decoration: line-through;
  color: grey;
}

.status-bar {
  display: flex;
  align-items: center;
  justify-content: space-evenly;
  font-size: 16px;
  border-top: 1px solid lightgrey;
  padding-top: 14px;
  margin-bottom: 14px;
}

button {
  font-size: 14px;
  background-color: white;
  appearance: none;

  &:hover {
    background: lightgreen;
  }

  &:focus {
    outline: none;
  }
}

.active {
  background: lightgreen;
}

// CSS transitions
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>
