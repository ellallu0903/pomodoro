<template lang="pug">
  #home
    b-container
      b-row
        b-col(cols="12")
          h1 {{ currentText }}
          h2 {{ timetext }}
          b-btn(variant="danger" v-if="status !== 1" @click="start")
            font-awesome-icon(:icon="['fas','play']")
          b-btn(variant="danger" v-if="status == 1" @click="pause")
            font-awesome-icon(:icon="['fas','pause']")
          b-btn(variant="danger" v-if="current.length > 0")
            font-awesome-icon(:icon="['fas','step-forward']" @click="finish(true)")
</template>

<script>

export default {
  name: 'Home',
  data () {
    return {
      timer: 0
    }
  },
  computed: {
    status: {
      // get / set 為固定寫法
      get () {
        return this.$store.state.status
      },
      set (value) {
        this.$store.commit('changeStatus', value)
      }
    },
    // 去 vuex 拿資料
    current () {
      return this.$store.state.current
    },
    currentText () {
      let result = ''

      if (this.current.length > 0) {
        result = this.current
      } else if (this.todos.length > 0) {
        result = '點擊開始'
      } else {
        result = '沒有事項'
      }

      return result
    },
    timeleft () {
      return this.$store.state.timeleft
    },
    alarm () {
      return this.$store.state.alarm
    },
    todos () {
      return this.$store.state.todos
    },
    timetext () {
      // 換算 分鐘 & 秒數
      const m = Math.floor(this.timeleft / 60)
      const s = Math.floor(this.timeleft % 60)
      return `${m} ： ${s} `
    }
  },
  methods: {
    start () {
      // 如果不是暫停繼續，且有待辦有東西時才把 todos 的第一個變成 current
      if (this.status !== 2 && this.todos.length > 0) {
        this.$store.commit('start')
      }
      // 有東西才會倒數
      if (this.current.length > 0) {
        this.status = 1
        this.timer = setInterval(() => {
          this.$store.commit('countdown')
          if (this.timeleft <= -1) {
            this.finish(false)
          }
        }, 1000)
      }
    },
    finish (skip) {
      // skip = true 手動跳過 / skip = false 自然倒數結束
      clearInterval(this.timer)
      this.status = 0
      this.$store.commit('finish')

      // 不是手動跳過，也就是自然倒數結束的話會有鈴聲響
      if (!skip) {
        const audio = new Audio()
        audio.src = './sounds/' + this.alarm
        audio.play()
      }

      if (this.todos.length > 0) {
        this.start()
      } else {
        alert('全部結束')
      }
    },
    pause () {
      clearInterval(this.timer)
      this.status = 2
    }
  }
}
</script>
