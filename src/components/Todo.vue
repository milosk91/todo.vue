<template>
  <div>
    <el-input
      class="todo-input"
      placeholder="What need's to be done"
      v-model="newTodo"
      @keyup.enter.native="addTodo"
      clearable
    />
    <transition-group
      name="fade"
      enter-active-class="animated fadeInUp"
      leave-active-class="animated fadeOutDown"
    >
      <div
        v-for="(todo, index) in todosFiltered"
        :key="todo.id"
        class="todo-item"
      >
        <div class="todo-item-left">
          <el-checkbox v-model="todo.completed" />
          <div
            v-if="!todo.editing"
            class="todo-item-label"
            @dblclick="editTodo(todo)"
            :class="{ completed: todo.completed }"
          >
            {{ todo.title }}
          </div>
          <el-input
            v-else
            v-model="todo.title"
            class="todo-item-edit"
            @blur="doneEdit(todo)"
            @keyup.enter.native="doneEdit(todo)"
            @keyup.esc.native="cancelEdit(todo)"
            v-focus
          />
        </div>
        <div class="remove-item" @click="removeTodo(index)">&times;</div>
      </div>
    </transition-group>

    <div class="extra-container">
      <div>
        <label>
          <el-checkbox :checked="!anyRemaining" @change="checkAllTodos" /> Check
          All Todos</label
        >
      </div>
      <div>{{ remaining }} items left</div>
    </div>
    <div class="extra-container">
      <div class="buttons">
        <el-button
          type="primary"
          @click="filter = 'all'"
          :class="{ active: filter == 'all' }"
          >All</el-button
        >
        <el-button
          type="primary"
          @click="filter = 'active'"
          :class="{ active: filter == 'active' }"
          >Active</el-button
        >
        <el-button
          type="primary"
          @click="filter = 'completed'"
          :class="{ active: filter == 'completed' }"
          >Completed</el-button
        >
      </div>
      <div>
        <transition name="fade">
          <el-button
            type="primary"
            @click="clearCompleted"
            v-if="showClearCompletedButton"
            >Clear Completed</el-button
          >
        </transition>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "todo",
  data() {
    return {
      newTodo: "",
      idForTodo: 5,
      beforeEditCache: "",
      filter: "all",
      todos: [
        {
          id: 1,
          title: "Learn",
          completed: false,
          editing: false
        },
        {
          id: 2,
          title: "Work",
          completed: false,
          editing: false
        },
        {
          id: 3,
          title: "Training",
          completed: false,
          editing: false
        },
        {
          id: 4,
          title: "Lunch",
          completed: false,
          editing: false
        }
      ]
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
      return this.todos;
    },
    showClearCompletedButton() {
      return this.todos.filter(todo => todo.completed).length > 0;
    }
  },
  directives: {
    focus: {
      inserted: function(el) {
        el.focus();
      }
    }
  },
  methods: {
    addTodo() {
      if (this.newTodo.trim().length == 0) {
        return;
      }
      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false
      });
      (this.newTodo = ""), (this.idForTodo += 1);
    },
    removeTodo(index) {
      this.todos.splice(index, 1);
    },
    editTodo(todo) {
      this.beforeEditCache = todo.title;
      todo.editing = true;
    },
    doneEdit(todo) {
      if (todo.title.trim() == "") {
        todo.title = this.beforeEditCache;
      }
      todo.editing = false;
    },
    cancelEdit(todo) {
      todo.title = this.beforeEditCache;
      todo.editing = false;
    },
    checkAllTodos() {
      this.todos.forEach(todo => (todo.completed = event.target.checked));
    },
    clearCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed);
    }
  }
};
</script>

<style>
@import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.5.2/animate.min.css");

.todo-input {
  width: 100%;
  padding: 10px 18px;
  font-size: 18px;
  margin-bottom: 16px;
}
.todo-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 12px;
  animation-duration: 0.5s;
}

.remove-item {
  cursor: pointer;
  margin-left: 14px;
}

.remove-item:hover {
  color: black;
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
  font-family: "Avenir", Helvetica, Arial, sans-serif;
}

.todo-item-edit:focus {
  outline: none;
}

.completed {
  text-decoration: line-through;
  color: grey;
}

.extra-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 16px;
  border-top: 1px solid lightgrey;
  padding-top: 14px;
  margin-bottom: 14px;
}

.buttons {
  display: flex;
  align-items: left;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>
