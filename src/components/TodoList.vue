<template>
  <div>
    <input
      type="text"
      class="todo-input"
      placeholder="Plese in put your list"
      v-model="newTodo"
      @keyup.enter="addTodo"
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
        <div todo class="todo-item-left">
          <input type="checkbox" v-model="todo.completed" />
          <div
            v-if="!todo.editing"
            @click="editTodo(todo)"
            class="todo-item-label"
            :class="{ completed: todo.completed }"
          >
            {{ todo.title }}
          </div>
          <input
            v-else
            class="todo-item-edit"
            type="text"
            v-model="todo.title"
            @blur="doneEdit(todo)"
            @keyup.enter="doneEdit(todo)"
            v-focus
          />
        </div>
        <button type="button" class="btn btn-danger " @click="removeTodo">
          Delete
        </button>
      </div>
    </transition-group>
    <div class="extra-container">
      <div>
        <label
          ><input
            type="checkbox"
            :checked="!anyRemaining"
            @change="checkAllTodos"
          />
          Check List All</label
        >
      </div>
      <div class="font-listall">{{ remaining }} List</div>
    </div>
    <div class="extra-container">
      <div>
        <button
          class="btn btn-info"
          :class="{ active: filter == 'all' }"
          @click="filter = 'all'"
        >
          All
        </button>
        <button
          class="btn btn-success"
          :class="{ active: filter == 'active' }"
          @click="filter = 'active'"
        >
          Active
        </button>
        <button
          class="btn btn-primary"
          :class="{ active: filter == 'completed' }"
          @click="filter = 'completed'"
        >
          Completed
        </button>
      </div>
      <div>
        <transition name="fade">
          <button
            v-if="showClearCompleatedButton"
            class="btn btn-warning"
            @click="clearCompleted"
          >
            Clear Completed
          </button>
        </transition>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "todo-list",
  data() {
    return {
      newTodo: "",
      idForTodo: 4,
      // beforeEditCahe: "",
      filter: "all",
      todos: [
        {
          id: 1,
          title: "ข้าวผัด",
          completed: false,
          editing: false
        },
        {
          id: 2,
          title: "กระเพราไก่",
          completed: false,
          editing: false
        },
        {
          id: 3,
          title: "ข้าวหน้าหมูทอด",
          completed: false,
          editing: false
        }
      ]
    };
  },
  directives: {
    focus: {
      // directive definition
      inserted: function(el) {
        el.focus();
      }
    }
  },

  methods: {
    addTodo() {
      console.log(this.idForTodo);
      if (this.newTodo.trim().length == 0) {
        return;
      }
      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false
      });
      this.newTodo = "";
      this.idForTodo++;
    },
    editTodo(todo) {
      todo.editing = true;
    },
    doneEdit(todo) {
      todo.editing = false;
    },
    removeTodo(index) {
      // console.log(index);
      // this.$swal("Delete Success", this.todos.splice(index, 1));
      this.todos.splice(index, 1);
    },
    checkAllTodos() {
      // console.log("Test");
      this.todos.forEach(todo => (todo.completed = event.target.checked));
    },
    clearCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed);
    }
  },

  computed: {
    remaining() {
      return this.todos.filter(todo => !todo.completed).length;
    },
    anyRemaining() {
      return this.idForTodo;
    },
    todosFiltered() {
      // console.log("test");
      if (this.filter == "all") {
        return this.todos;
      } else if (this.filter == "active") {
        return this.todos.filter(todo => !todo.completed);
      } else if (this.filter == "completed") {
        return this.todos.filter(todo => todo.completed);
      }
      return this.todos;
    },
    showClearCompleatedButton() {
      return this.todos.filter(todo => todo.completed).length > 0;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style Lang="scss">
@import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.5.2/animate.min.css");
.todo-input {
  width: 100%;
  padding: 10px 18px;
  font-size: 18px;
  margin-bottom: 16px;
}
.todo-item {
  margin-bottom: 12px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  animation-duration: 0.3s;
}
.remove-item {
  cursor: pointer;
  margin-left: 14px;
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
  font-family: "Avenir", Helvetica, Arial, sans-serif;
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
.font-listall {
  font-size: 18px;
  font-weight: bold;
  color: green;
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
