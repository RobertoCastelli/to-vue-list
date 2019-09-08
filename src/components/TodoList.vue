<template>
  <div class="container">
    <h2 class="text-center mt-3">TO-<span style="color: #41b883">VUE</span>-LIST</h2>
    <input type="text" class="form-control w-100 my-3" placeholder="enter things to-vue" 
           v-model="newTodo" @keyup.enter="addTodo" />

    <div class="bg-light rounded" v-for="(todo, index) in todosFiltered" :key="todo.id">
      <input type="checkbox" name="task" v-model="todo.completed" />
     
      <label for="task" v-if="!todo.editing" @dblclick="editTodo(todo)" :class="{completed : todo.completed}">{{ todo.title }}</label>
      <input type="text" v-else @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" v-model="todo.title">

      <div class="remove-item float-right" @click="removeTodo(index)">&times;</div>
    </div>

    <div class="sub-container">
      <input name="allChecked" type="checkbox" :checked="!anyRemaining" @change="checkAllTodos" /> check all
      <label class="float-right" for="allChecked">{{ remaining }} task remaining</label>  
    </div>

    <div>
      <button class="btn btn-sm btn-outline-success" :class="{ active: filter == 'all'}" @click="filter = 'all'" >all</button>
      <button class="btn btn-sm btn-outline-success" :class="{ active: filter == 'active'}" @click="filter = 'active'">active</button>
      <button class="btn btn-sm btn-outline-success" :class="{ active: filter == 'completed'}" @click="filter = 'completed'">completed</button>
      <button class="btn btn-sm btn-outline-success float-right" v-if="showClearButton" @click="clearCompleted">clear completed</button>
    </div>

    <footer class="text-muted text-center my-5">
      <small>copyright &copy; 2019 Roberto Castelli - www.robertocastelli.dev</small>
    </footer>
  </div>
</template>

<script>
export default {
  name: "TodoList",
  data() {
    return {
      newTodo: "",
      idForTodo: 2,
      filter: "all",
      todos: [
        {
          id: 1,
          title: "test 1",
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
    showClearButton() {
      return this.todos.filter(todo => todo.completed).length > 0;
    }
  },
  methods: {
    addTodo() {
      if (this.newTodo == "") {
        return;
      }

      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false,
        editing: false
      });
      this.newTodo = "";
      this.idForTodo++;
    },
    removeTodo(index) {
      this.todos.splice(index, 1);
    },
    checkAllTodos() {
      this.todos.forEach(todo => (todo.completed = event.target.checked));
    },
    clearCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed);
    },
    editTodo(todo) {
      todo.editing = true;
    },
    doneEdit(todo) {
      todo.editing = false;
    }
  },
  mounted() {
    console.log("Application is mounted!");
    if (localStorage.getItem("todos")) {
      this.todos = JSON.parse(localStorage.getItem("todos"));
    }
  },
  watch: {
    todos: {
      handler() {
        console.log("To-Do-List is changed!");
        localStorage.setItem("todos", JSON.stringify(this.todos));
      },
      deep: true
    }
  }
};
</script>

<style>
.container {
  max-width: 500px;
}

.active {
  background: #41b883 !important;
}

.completed {
  text-decoration: line-through;
  color: #2c3e50;
  opacity: 0.5;
}

.sub-container {
  color: #41b883;
  font-size: 14px;
  border-top: 1px dotted #2c3e50;
  padding-top: 10px;
  margin: 20px 0;
}

.remove-item {
  cursor: pointer;
}

.remove-item:hover {
  transform: scale(1.5);
}

.btn-outline-success,
.btn-outline-success:active,
.btn-outline-success:visited,
.btn-outline-success:focus {
  border-color: #41b883 !important;
  color: #41b883;
}

.btn-outline-success:hover {
  background-color: #41b883 !important;
}
</style>