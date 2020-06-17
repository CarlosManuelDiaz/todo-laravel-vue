<template>
  <div>
    <form @submit.prevent="editTask(task)" v-if="editMode">
      <h3>Edit task</h3>
      <input
        type="text"
        class="form-control mb-2"
        placeholder="New task"
        v-model="task.description"
      />
      <button class="btn btn-success" type="submit">Save</button>
      <button class="btn btn-danger" type="submit" @click="cancelEdit">Cancel</button>
    </form>
    <form @submit.prevent="add" v-else>
      <h3>New task</h3>
      <input
        type="text"
        class="form-control mb-2"
        placeholder="New task"
        v-model="task.description"
      />
      <button class="btn btn-primary btn-lg btn-block" type="submit">Add</button>
    </form>
    <hr />
    <h3>Tasks:</h3>
    <ul class="list-group">
      <li class="list-group-item" v-for="(item, index) in tasks" :key="index">
        <p>#ID: {{item.id}}</p>
        <p>Task: {{item.description}}</p>
        <p>
          <button class="btn btn-warning btn-sm" @click="editForm(item)">Edit</button>
          <button class="btn btn-danger btn-sm" @click="deleteTask(item, index)">Delete</button>
        </p>
      </li>
    </ul>
  </div>
</template>


<script>
export default {
  data() {
    return {
      tasks: [],
      editMode: false,
      task: { description: "" }
    };
  },
  created() {
    axios.get("/tasks").then(res => {
      this.tasks = res.data;
    });
  },
  methods: {
    add() {
      if (this.task.description.trim() === "") {
        alert("The field cannot be empty");
        return;
      }
      const newTask = this.task;
      this.task = { description: "" };
      axios.post("/tasks", newTask).then(res => {
        const taskServer = res.data;
        this.tasks.push(taskServer);
      });
    },
    editForm(item) {
      this.task.description = item.description;
      this.task.id = item.id;
      this.editMode = true;
    },
    editTask(task) {
      if (this.task.description.trim() === "") {
        alert("The field cannot be empty");
        return;
      }
      const params = { description: task.description };
      axios.put(`/tasks/${task.id}`, params).then(res => {
        this.editMode = false;
        const index = this.tasks.findIndex(item => item.id === task.id);
        this.tasks[index] = res.data;
      });
    },
    deleteTask(task, index) {
      const confirmacion = confirm(`Delete task ${task.description}`);
      if (confirmacion) {
        axios.delete(`/tasks/${task.id}`).then(() => {
          this.tasks.splice(index, 1);
        });
      }
    },
    cancelEdit() {
      this.editMode = false;
      this.task = { nombre: "", description: "" };
    }
  }
};
</script>
