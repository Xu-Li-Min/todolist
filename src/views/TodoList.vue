/* eslint-disable to ignore the next line. */
<template>
  <div id="app" class="container my-3">
    <div class="input-group mb-3">
      <div class="input-group-prepend">
        <span class="input-group-text" id="basic-addon1">待辦事項</span>
      </div>
      <input
        type="text"
        class="form-control"
        placeholder="準備要做的任務"
        v-model="newTodo"
        @keyup.enter="addTodo()"
      />
      <div class="input-group-append">
        <button class="btn btn-primary" type="button" @click="addTodo()">
          新增
        </button>
      </div>
    </div>
    <div class="card text-center">
      <div class="card-header">
        <ul class="nav nav-tabs card-header-tabs">
          <li class="nav-item">
            <a
              class="nav-link"
              :class="{ active: tagActive == 'all' }"
              @click="tagActive = 'all'"
              href="#"
            >
              全部
            </a>
          </li>
          <li class="nav-item">
            <a
              class="nav-link"
              :class="{ active: tagActive == 'process' }"
              @click="tagActive = 'process'"
              href="#"
              >進行中</a
            >
          </li>
          <li class="nav-item">
            <a
              class="nav-link"
              href="#"
              :class="{ active: tagActive == 'completed' }"
              @click="tagActive = 'completed'"
              >已完成</a
            >
          </li>
        </ul>
      </div>
      <ul class="list-group list-group-flush text-left">
        <li
          class="list-group-item"
          v-for="(item, index) in filterTodos"
          :key="index"
        >
          <div
            class="d-flex align-items-center"
            @dblclick.prevent="editTodo(item)"
            v-if="item.id !== catchTodo.id"
          >
            <div class="form-check">
              <input
                type="checkbox"
                class="form-check-input"
                :id="item.title"
                v-model="item.completed"
              />
              <label
                class="form-check-label"
                :class="{ completed: item.completed }"
                :for="item.title"
              >
                {{ item.title }}
              </label>
            </div>
            <button
              type="button"
              class="btn close ms-auto"
              aria-label="Close"
              @click="delTodo(item)"
            >
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <input
            type="text"
            class="form-control"
            v-if="item.id == catchTodo.id"
            v-model="catchTitle"
            @keyup.enter="doenEdit(item)"
            @keyup.esc="cancleEdit()"
          />
        </li>
        <!-- <li class="list-group-item">
          <input type="text" class="form-control" />
        </li> -->
      </ul>
      <div class="card-footer d-flex justify-content-between">
        <span
          >還有
          <span v-if="todos">{{ todos.length }}</span>
          <span v-else>0</span>
          筆任務未完成</span
        >
        <a href="#" @click.prevent="clearAllTodo">清除所有任務</a>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TodoList',
  data() {
    return {
      newTodo: '',
      todos: [],
      catchTodo: {},
      catchTitle: '',
      tagActive: 'all',
    };
  },
  methods: {
    setLocalStorage() {
      const todoList = JSON.stringify(this.todos);
      localStorage.setItem('todo-list', todoList);
    },
    getLocalStorage() {
      const todoList = JSON.parse(localStorage.getItem('todo-list'));
      this.todos = todoList;
    },
    addTodo() {
      if (!this.newTodo) {
        return;
      }
      const todoId = Math.floor(Date.now());
      const todoTitle = this.newTodo.trim();
      const todoObj = {
        id: todoId,
        title: todoTitle,
        completed: false,
      };
      this.todos.push(todoObj);

      this.newTodo = '';
      this.setLocalStorage();
    },
    delTodo(todo) {
      const index = this.todos.findIndex((item) => item.id === todo.id);
      this.todos.splice(index, 1);
      this.setLocalStorage();
    },
    editTodo(item) {
      this.catchTodo = item;
      this.catchTitle = item.title;
      this.setLocalStorage();
    },
    doenEdit(item) {
      // eslint-disable-next-line no-param-reassign
      item.title = this.catchTitle;
      this.catchTitle = '';
      this.catchTodo = {};
      this.setLocalStorage();
    },
    cancleEdit() {
      this.catchTodo = {};
    },
    clearAllTodo() {
      this.todos = [];
      this.setLocalStorage();
    },
  },
  mounted() {
    this.getLocalStorage();
  },
  computed: {
    filterTodos() {
      let newTodo = [];
      if (this.tagActive === 'all') {
        newTodo = this.todos;
      } else if (this.tagActive === 'process') {
        this.todos.forEach((item) => {
          if (!item.completed) {
            newTodo.push(item);
          }
        });
      } else if (this.tagActive === 'completed') {
        this.todos.forEach((item) => {
          if (item.completed) {
            newTodo.push(item);
          }
        });
      }
      return newTodo;
    },
  },
};
</script>
