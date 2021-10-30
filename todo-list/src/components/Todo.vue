<template>
  <div>
    <input type="text" class="taskInput" placeholder="Add task to list" v-model="newTask" @keyup.enter="addTask">
    <div v-for="(task) in taskState" :key="task.id" class="taskItem">
      <div class="currentTask">
        <input type="checkbox" v-model="task.finished">
        <div v-if="!task.editing" @click="editTask(task)" class="currentTaskLabel" :class="{ finished : task.finished }">{{ task.title }}</div>
        <input v-else class="currentTaskEdit" type="text" v-model="task.title" @keyup.enter="finishEdit(task)" @keyup.esc="cancelEdit(task)" v-focus>
      </div>

      <div class="priority">
        <input type="number" placeholder="Add task priority" v-model="newPriority" @keyup.enter="addPriority">
      </div>

      <div class="delete" @click="deleteTask()">
        &bigotimes;
      </div>
    </div>

    <div class="buttons">
      <label><input type="checkbox" :checked="!remainingTasks" @change="checkAllTasks"> Check All</label>
    </div>

    <div class="buttons">
      <button :class="{ active: filter === 'list' }" @click="filter = 'list'">List</button>
      <button :class="{ active: filter === 'archive' }" @click="filter = 'archive'">Archive</button>
      <button :class="{ active: filter === 'all' }" @click="filter = 'all'">All</button>
      <button v-if="finishedButton" @click="clearFinished">Clear Archive</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'priority list app',
  msg: String,

  data () {
    return {
      filter: 'list',
      tasks: []
    }
  },

  computed: {
    remainingTasks() {
      return this.remaining !== 0
    },

    taskState() {
      if (this.filter === 'all') {
        return this.tasks
      } else if (this.filter === 'list') {
        return this.tasks.filter(task => !task.finished)
      } else if (this.filter === 'archive') {
        return this.tasks.filter(task => task.finished)
      }
      return this.tasks
    },

    finishedButton() {
      return this.tasks.filter(task => task.finished).length > 0
    }
  },

  methods: {
    addTask() {
      if (this.newTask.trim().length === 0) {
        return
      }
      this.tasks.push({
        id: this.taskID,
        title: this.newTask,
        finished: false,
        editing: false,
      })
      this.newTask = ''
      this.taskID++
    },

    addPriority() {
      if (this.newPriority.trim().length === 0) {
        return
      }

      if (this.newPriority < 1) {
        this.newPriority = 1
      }

      if (this.newPriority > 10) {
        this.newPriority = 10
      }
    },

    editTask(task) {
      this.beforeEditCache = task.title
      task.editing = true
    },

    finishEdit(task) {
      if (task.title.trim() === '') {
        task.title = this.beforeEditCache
      }
      task.editing = false
    },

    cancelEdit(task) {
      task.title = this.beforeEditCache
      task.editing = false
    },

    deleteTask(index) {
      this.tasks.splice(index, 1)
    },

    checkAllTasks() {
      this.tasks.forEach((task) => task.finished = event.target.checked)
    },

    clearFinished() {
      this.tasks = this.tasks.filter(task => !task.finished)
    }
  }
}
</script>

<style>
.taskInput {
  width: 55%;
  display: flex;
  padding: 15px;
  font-size: 20px;
  margin-bottom: 16px;
}

.taskItem {
  margin-bottom: 10px;
  display: flex;
  align-items: center;
}

.currentTask {
  width: 40%;
  display: flex;
  align-items: center;
}

.priority {
  width: 10%;
  align-items: center;
}

.currentTaskLabel {
  padding: 10px;
  margin-left: 10px;
}

.buttons {
  display: flex;
}

.delete {
  width: 10%;
  cursor: default;
  margin-left: 14px;
}

.finished {
  text-decoration: line-through;
}

.active {
  background: greenyellow;
}
</style>