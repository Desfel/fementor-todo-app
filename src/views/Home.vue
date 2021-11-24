<template>
  <div class="page-wrapper" :class="{'theme-light' : isLight}">
    <div class="app-wrapper">
      <section class="header-section">
        <h1>Todo</h1>

        <a href="#" class="theme-toggler" @click="toggleTheme">
          <img v-if="!isLight" src="@/assets/img/icon-sun.svg" alt="Light theme" />
          <img v-if="isLight" src="@/assets/img/icon-moon.svg" alt="Dark theme" />
        </a>
      </section>

      <section class="task-form-wrapper">
        <form class="task-form" action="#" method="POST" @submit="addTask">
          <div class="task-container">
            <div class="task-cell">
              <span class="checkbox" @click="addTask">
                <span class="checkbox-inner"></span>
                <img class="checkmark" src="@/assets/img/icon-check.svg">
              </span>

              <input
                v-model="taskValue"
                type="text"
                value=""
                name="task"
                placeholder="Create a new todo..."
              />
            </div>
          </div>
        </form>
      </section>

      <section class="task-container task-list-wrapper">
        <div class="task-cell" v-for="task in tasksArray" v-bind:key="task.id"
          :class="{
            'is-completed': completedTasks.indexOf(task.id) !== -1,
            'is-visible':
              (completedTasks.indexOf(task.id) === -1 && activeFilter === 'active') ||
              (completedTasks.indexOf(task.id) !== -1 && activeFilter === 'completed') ||
              activeFilter === '*'
        }">
          <span class="checkbox" @click="toggleCompleteTask(task.id)">
            <span class="checkbox-inner"></span>
            <img class="checkmark" src="@/assets/img/icon-check.svg">
          </span>

          <p v-text="task.task"></p>

          <span class="remove-task" @click="removeTask(task.id)">
            <img src="@/assets/img/icon-cross.svg" />
          </span>
        </div>

        <div class="task-actions-row">
          <p class="item-counts" v-text="`${tasksArray.length} items left`"></p>

          <div class="task-filters desktop-filters">
            <a href="#" :class="{'is-active' : activeFilter === '*'}" @click="selectFilter('*')">All</a>
            <a href="#" :class="{'is-active' : activeFilter === 'active'}" @click="selectFilter('active')">Active</a>
            <a href="#" :class="{'is-active' : activeFilter === 'completed'}" @click="selectFilter('completed')">Completed</a>
          </div>

          <a href="#" class="clear-completed" @click="clearCompletedTasks">Clear completed</a>
        </div>
      </section>

      <div class="task-actions-row mobile-filters task-container">
        <div class="task-filters">
          <a href="#" :class="{'is-active' : activeFilter === '*'}" @click="selectFilter('*')">All</a>
          <a href="#" :class="{'is-active' : activeFilter === 'active'}" @click="selectFilter('active')">Active</a>
          <a href="#" :class="{'is-active' : activeFilter === 'completed'}" @click="selectFilter('completed')">Completed</a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapState } from 'vuex'

export default {
  data() {
    return {
      isLight: false,
      taskValue: '',
      tasksArray: [],
      completedTasks: [],
      activeFilter: '*'
    }
  },
  head() {
    return {
      title: {
        inner: 'Home'
      },
      meta: [
        {
          name: 'description',
          content: `${this.appTitle} home page`,
          id: 'desc'
        }
      ]
    }
  },
  methods: {
    toggleTheme(e) {
      e.preventDefault()
      this.isLight = !this.isLight
    },
    addTask(e) {
      e.preventDefault()

      const trimmedValue = this.taskValue.trim()

      if(trimmedValue !== '') {
        this.tasksArray.push({id: this.tasksArray.length, task: this.taskValue})
      }
    },
    toggleCompleteTask(taskId) {
      if(this.completedTasks.indexOf(taskId) === -1) {
        this.completedTasks.push(taskId)
      } else {
        this.completedTasks.splice(this.completedTasks.indexOf(taskId), 1)
      }
    },
    removeTask(taskId) {
      this.tasksArray.forEach((task, index) => {
        if(task.id === taskId) {
          this.tasksArray.splice(index, 1)
        }
      })
      this.completedTasks.splice(this.completedTasks.indexOf(taskId), 1)
    },
    clearCompletedTasks() {
      this.completedTasks.forEach(completedTask => {
        this.tasksArray.forEach((task, index) => {
          if(task.id === completedTask) {
            this.tasksArray.splice(index, 1)
          }
        })
      })
      this.completedTasks = []
    },
    selectFilter(filter) {
      this.activeFilter = filter
      console.log(this.activeFilter)
    }
  },
  computed: mapState('app', ['appTitle'])
}
</script>

<style lang="scss" scoped>
@import '@/theme/variables.scss';
@import '@/theme/main.scss';
</style>
