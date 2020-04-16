<template>
  <div class="container" id="todo-list">
    <div class="row">
      <div class="col-md-6 mx-auto">
        <h1 class="text-center">TODO List App</h1>
        <form v-on:submit.prevent="addNewTask">
          <label for="tasknameinput">Task Name</label>
          <input v-model="taskname" class="form-control" id="tasknameinput" placeholder="Add New Task">
          <button v-if="this.isEdit == false" type="submit" class="btn btn-success btn-block mt-3">
            Submit
          </button>
          <button v-else type="button" v-on:click="updateTask()" class="btn btn-primary btn-block mt-3">
            Update
          </button>
        </form>

        <table class="table">
          <tr v-for="(todo) in todos" v-bind:key="todo._id" v-bind:task_name="todo.task_name">
            <td class="text-left">{{ todo.task_name }}</td>
            <td class="text-right">
              <button v-on:click="editTask(todo.task_name, todo._id)" class="btn btn-info">Edit</button>
              <button v-on:click="deleteTask( todo._id)" class="btn btn-danger">Delete</button>
            </td>
          </tr>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data () {
    return {
      todos: [],
      id: '',
      taskname: '',
      isEdit: false
    }
  },
  mounted () {
    this.getTasks()
  },
  methods: {
    /* eslint-disable */
    getTasks() {
      axios({ method: "GET", url:'/api/tasks'}).then(
        result => {
          console.log(result.data);
          this.todos = result.data;
        },
        error => {
          console.error(error);
        }
      )
    },
    addNewTask() {
      axios.post('/api/tasks',
        {task_name: this.taskname})
      .then((res) => {
        this.taskname = '';
        this.getTasks();
        console.log(res);
      })
      .catch((err) => {
        console.log(err);
      })
    },
    editTask(title, id) {
      this.id = id;
      this.taskname = title;
      this.isEdit = true;
    },
    updateTask() {
      axios.put(`/api/tasks/${this.id}`,
        {task_name: this.taskname})
      .then((res) => {
        this.taskname = '';
        this.isEdit = false;
        this.getTasks();
        console.log(res)
      })
      .catch((err) => {
        console.log(err);
      })
    },
    deleteTask(id) {
      axios.delete(`/api/tasks/${id}`)
      .then((res) => {
        this.taskname = '';
        this.getTasks();
        console.log(res)
      })
      .catch((err) => {
        console.log(err);
      })
    }
  }
}
</script>
