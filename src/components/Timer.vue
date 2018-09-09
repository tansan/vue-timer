<template>
  <div id="timer">
    <div id="now">
      <p>{{ nowTime }}</p>
    </div>
    <div
      v-for="item in items" :key="item.id">
      <div class="input-group mb-3">
        <div class="input-group-prepend">
          <div class="input-group-text">
            <input
              type="checkbox"
              v-model="item.isChecked"
              v-on:click="toggleTimer(item)">
          </div>
        </div>
        <input
          type="text"
          class="form-control"
          v-model="item.title"
          placeholder="Please input alert name!">
        <div class="input-group-append">
          <div class="btn-group">
            <div class="btn-group">
              <button
                class="btn btn-secondary dropdown-toggle"
                type="button"
                id="selectHour"
                data-toggle="dropdown"
                aria-hashopup="true"
                aria-expanded="false">
                {{ item.hour }}
              </button>
              <div
                class="dropdown-menu"
                aria-labelledby="selectHour">
                <button
                  class="dropdown-item"
                  v-for="i in 23"
                  v-bind:key="i"
                  v-on:click="item.hour = i">
                  {{ i }}
                </button>
              </div>
            </div>
            <div class="btn-group">
              <button
                class="btn btn-secondary dropdown-toggle"
                type="button"
                id="selectMinutes"
                data-toggle="dropdown"
                aria-hashopup="true"
                aria-expanded="false">
                {{ item.minutes }}
              </button>
              <div
                class="dropdown-menu"
                aria-labelledby="selectMinutes">
                <button
                  class="dropdown-item"
                  v-for="i in 59"
                  v-bind:key="i"
                  v-on:click="item.minutes = i">
                  {{ i }}
                </button>
              </div>
            </div>
            <div class="btn-group">
              <button
                class="btn btn-danger"
                v-on:click="removeTimer(item)">
                delete
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div>
      <button
        class="btn btn-secondary"
        v-on:click="addTimer">
        add timer
      </button>
      <button
        class="btn btn-danger"
        v-on:click="clearAll">
        clear all timer
      </button>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      nextId: 0,
      items: [],
      nowTime: null
    }
  },
  methods: {
    addTimer: function () {
      this.items.push({
        id: this.nextId++,
        title: '',
        hour: 23,
        minutes: 59,
        isActive: false,
        intervalId: undefined
      })
    },
    toggleTimer: function (item) {
      item.isChecked ? this.stopTimer(item) : this.startTimer(item)
    },
    startTimer: function (item) {
      var intervalId = setInterval(function () {
        var now = new Date()
        if (item.hour === now.getHours() && item.minutes === now.getMinutes()) {
          clearInterval(intervalId)
          item.isChecked = false
          alert('alert ' + item.title + ' expired!')
        }
      })
      item.intervalId = intervalId
    },
    stopTimer: function (item) {
      clearInterval(item.intervalId)
    },
    removeTimer: function (item) {
      this.items.splice(this.items.indexOf(item), 1)
    },
    clearAll: function () {
      this.items = []
    },
    saveCache: function () {
      localStorage.setItem('nextId', JSON.stringify(this.nextId))
      localStorage.setItem('items', JSON.stringify(this.items))
    },
    loadCache: function () {
      this.nextId = JSON.parse(localStorage.getItem('nextId'))
      if (!this.nextId) {
        this.nextId = 0
      }
      this.items = JSON.parse(localStorage.getItem('items'))
      if (!this.items) {
        this.items = []
      }
    }
  },
  mounted: function () {
    this.loadCache()
  },
  created: function () {
    var self = this
    setInterval(function () {
      var now = new Date()
      self.nowTime = now.getHours() + ':' + now.getMinutes() + ':' + now.getSeconds()
    }, 1000)
  },
  watch: {
    items: {
      handler: function () {
        this.saveCache()
      },
      deep: true
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#timer {
  width: 550px;
  padding-top: 20px;
  padding-bottom: 20px;
  margin: 0px auto;
}
</style>
