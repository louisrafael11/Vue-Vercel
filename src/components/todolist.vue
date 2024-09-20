<template>
  <v-container>
    <v-row class="justify-center text-center">
      <v-col cols="12" md="8" lg="6">
        <v-card class="pa-4 elevation-3 title-card mb-4">
          <h2 class="title">What to Do?</h2>
        </v-card>

        <v-card class="pa-4 elevation-3 task-card">
          <v-text-field
            v-model="newTask"
            label="New Task"
            @keyup.enter="addTask"
            outlined
            dense
            color="blue"
          />
          <v-btn color="blue darken-2" @click="addTask" class="ma-2">Add Task</v-btn>
          <v-btn color="green darken-1" @click="toggleAllTasksCompletion" class="ma-2">
            {{ areAllTasksCompleted ? 'Unmark All' : 'Mark All as Done' }}
          </v-btn>

          <v-divider class="my-4"></v-divider>

          <v-chip class="ma-2" color="blue lighten-2" label>
            {{ remainingTasks }} Task{{ remainingTasks === 1 ? '' : 's' }} Remaining
          </v-chip>

          <v-row>
            <v-col
              v-for="(task, index) in tasks"
              :key="index"
              cols="12"
              md="12"
            >
              <v-card
                class="d-flex align-center justify-space-between custom-card mt-5"
                outlined
                elevation="2"
              >
                <div class="v-card-title" :class="{ completed: task.completed }">
                  {{ task.text }}
                </div>
                <v-card-actions>
                  <v-checkbox
                    v-model="task.completed"
                    @change="saveTasks"
                    class="mt-5"
                    color="blue"
                  ></v-checkbox>

                  <v-btn
                    icon
                    class="text-white bg-warning"
                    @click="toggleEditTask(index)"
                  >
                    <v-icon>mdi-pencil</v-icon>
                  </v-btn>

                  <v-btn
                    icon
                    class="text-white bg-red me-3"
                    @click="deleteTask(index)"
                  >
                    <v-icon>mdi-delete</v-icon>
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-col>
          </v-row>

          <v-btn
            v-if="hasCompletedTasks"
            color="red darken-1"
            @click="removeCompletedTasks"
            class="ma-2 mt-4"
          >
            Remove Completed Tasks
          </v-btn>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: 'TodoList',

  data: () => ({
    newTask: '',
    tasks: [],
  }),

  mounted() {
    this.loadTasks();
  },

  computed: {
    remainingTasks() {
      return this.tasks.filter(task => !task.completed).length;
    },
    areAllTasksCompleted() {
      return this.tasks.length && this.tasks.every(task => task.completed);
    },
    hasCompletedTasks() {
      return this.tasks.some(task => task.completed);
    },
  },

  methods: {
    addTask() {
      if (this.newTask.trim() !== '') {
        const timestamp = new Date().toLocaleString();
        this.tasks.push({ text: this.newTask, completed: false, timestamp, isEditing: false });
        this.newTask = '';
        this.saveTasks();
      }
    },
    deleteTask(index) {
      this.tasks.splice(index, 1);
      this.saveTasks();
    },
    updateTask(index) {
      const timestamp = new Date().toLocaleString();
      this.tasks[index].timestamp = timestamp;
      this.tasks[index].isEditing = false; 
      this.saveTasks();
    },
    toggleEditTask(index) {
      this.tasks[index].isEditing = !this.tasks[index].isEditing;
      if (!this.tasks[index].isEditing) {
        this.updateTask(index);
      }
    },
    removeCompletedTasks() {
      this.tasks = this.tasks.filter(task => !task.completed);
      this.saveTasks();
    },
    toggleAllTasksCompletion() {
      const shouldMarkAsDone = !this.areAllTasksCompleted;
      this.tasks.forEach(task => {
        task.completed = shouldMarkAsDone;
      });
      this.saveTasks();
    },
    saveTasks() {
      localStorage.setItem('tasks', JSON.stringify(this.tasks));
    },
    loadTasks() {
      const savedTasks = localStorage.getItem('tasks');
      if (savedTasks) {
        this.tasks = JSON.parse(savedTasks);
      }
    },
  },
};
</script>

<style scoped>

.fun-font {
  font-family: 'Comic Sans MS', Cursive, sans-serif;
  font-size: 30px;
}

.v-btn {
  margin: 10px;
}

.v-text-field {
  width: 100%;
}

.timestamp {
  display: block;
  font-size: 12px;
  color: rgb(35, 196, 237);
}

.completed {
  text-decoration: line-through;
  color: rgb(11, 52, 149);
}

.task-item {
  margin-bottom: 24px;
}

.v-card.task-card {
  background-color: #e0f7fa;
  border-radius: 12px;
}

.v-card.title-card {
  background-color: #1e88e5;
  color: white;
  border-radius: 12px;
}

.title {
  margin: 0;
  font-size: 24px;
  font-weight: bold;
}

.v-container {
  padding-top: 50px;
  min-height: 100vh;
  background-color: #bbdefb;
}

.v-divider {
  background-color: #1976d2;
}
</style>
