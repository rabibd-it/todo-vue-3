<script setup>
import { ref, reactive, computed } from 'vue'
import { toast } from 'vue3-toastify';
import 'vue3-toastify/dist/index.css';
const todos = reactive([
  {
    id: 1,
    title: 'Todo One',
    status: true
  },
  {
    id: 2,
    title: 'Todo Two',
    status: false
  }
])

const countAllTodo = computed(() => {
  return todos.length;
})

const countAllCompleteTodo = computed(() => {
  return todos.filter(todo => todo.status).length;
})

const form = reactive({
  title: '',
  status: ''
});

const editId = ref('')

const addTodo = (id) => {
  if (form.title === '') {
    return toast.warning('Title field is required');
  }
  if (id) {
    toast.success(`${form.title} has been updated`);
    const todo = todos.findIndex(todo => todo.id === id)
    todos[todo].title = form.title
    todos[todo].status = form.status ? true : false
    editId.value = ''
  }
  else {
    todos.push({
      id: todos.length + 1,
      title: form.title,
      status: form.status ? true : false
    })
    toast.success(`${form.title} has been created`);
  }
  form.title = '';
  form.status = false;
}

const editTodo = (id) => {
  editId.value = id
  const todo = todos.find(todo => todo.id === id)
  form.title = todo.title
  form.status = todo.status ? true : false
}

const checkStatus = (index) => {
  todos[index].status = !todos[index].status
  editId.value = ''
  return toast.success(`${todos[index].title} has been changed`);
}

const remove = (index) => {
  toast.success(`${todos[index].title} has been deleted`);
  editId.value = ''
  return todos.splice(index, 1)
}

</script>

<template>
  <div class="container">
    <div class="row mt-5">
      <div class="col-md-7 mx-auto">
        <div class="card">
          <div class="card-header">
            To Do List
            <span v-if="countAllTodo">({{ countAllCompleteTodo }}/{{ countAllTodo }})</span>
          </div>
          <div class="card-body">
            <form class="row" @submit.prevent="addTodo(editId)">
              <div class="col-md-8">
                <label class="visually-hidden" for="title">Title</label>
                <div class="input-group">
                  <div class="input-group-text">Title</div>
                  <input type="text" class="form-control" v-model="form.title" id="title" placeholder="Title">
                </div>
              </div>
              <div class="col-md-2">
                <div class="form-check mt-2">
                  <input class="form-check-input" v-model="form.status" type="checkbox" id="status">
                  <label class="form-check-label" for="status">
                    Status
                  </label>
                </div>
              </div>
              <div class="col-md-2">
                <button type="submit" class="btn btn-primary">{{ editId ? 'Update' : 'Submit' }}</button>
              </div>
            </form>

            <div class="table-responsive mt-3">
              <table class="table table-bordered table-sm">
                <thead class="table-info">
                  <tr>
                    <th style="width: 5%;">#</th>
                    <th style="width: 60%;">Title</th>
                    <th style="width: 15%;">Status</th>
                    <th style="width: 20%;">Action</th>
                  </tr>
                </thead>
                <tbody v-if="todos.length">
                  <tr v-for="(todo, index) in todos" :key="todo.id">
                    <td>{{ index + 1 }}</td>
                    <td>
                      <span v-if="todo.status">
                        <del>{{ todo.title }}</del>
                      </span>
                      <span v-else>{{ todo.title }}</span>
                    </td>
                    <td>
                      <div class="form-check form-switch">
                        <input class="form-check-input" @click="checkStatus(index)" type="checkbox" :checked="todo.status" role="switch" :id="`todo-${index}`">
                      </div>
                    </td>
                    <td>
                      <button @click="editTodo(todo.id)" type="button" class="btn btn-success btn-sm mx-1">Edit</button>
                      <button @click="remove(index)" type="button" class="btn btn-danger btn-sm">Delete</button>
                    </td>
                  </tr>
                </tbody>
                <tbody v-else>
                  <tr>
                    <td colspan="4">
                      No To Do Yet.
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped></style>
