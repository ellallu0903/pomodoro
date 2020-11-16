<template lang="pug">
  #settings
    b-container
      b-row
        b-col(cols="12")
          b-table(:items="items" :fields="fields" @row-clicked="slectAlarm")
            template(#cell(file)="data")
              audio(controls :src="'./sounds/'+data.item.file")
            template(#cell(select)="data")
              font-awesome-icon(v-if="data.item.file === alarm" :icon="['fas', 'check']")
</template>

<script>
export default {
  name: 'Settings',
  data () {
    return {
      items: [
        {
          name: '鬧鈴',
          file: 'alarm1.mp3'
        },
        {
          name: 'yay',
          file: 'yay.mp3'
        }
      ],
      fields: [
        {
          key: 'name',
          label: '名稱'
        },
        {
          key: 'file',
          label: '預覽'
        },
        {
          key: 'select',
          label: '選擇'
        }
      ]
    }
  },
  computed: {
    alarm () {
      return this.$store.state.alarm
    }
  },
  methods: {
    // 上面的 item 是 row-clicked 元件事件裡面的 item
    // 不是我們自己設的 item
    slectAlarm (item) {
      this.$store.commit('slectAlarm', item.file)
    }
  }
}
</script>
