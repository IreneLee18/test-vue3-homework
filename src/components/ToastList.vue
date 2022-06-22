<template>
  <div class="toast-container position-absolute pe-3 top-0 end-0" style="z-index: 1050">
    <Toast v-for="(item, key) in messages" :key="key"
      :msg="item"
    ></Toast>
  </div>
</template>

<script>
import Toast from '@/components/ToastMsg.vue'
export default {
  components: {
    Toast
  },
  data () {
    return {
      messages: []
    }
  },
  inject: ['emitter'],
  mounted () {
    // 註冊監聽
    this.emitter.on('pushMsg', (msg) => {
      const { style = 'success', title, content } = msg
      // 一直找不到錯誤地方才發現this.messages.push({ style, title, content })，原本外面忘了加大括號，所以才一直傳送不了正確格式（正確格式:[{},{},{}]，錯誤格式:[...,...,...]）
      this.messages.push({ style, title, content })
      console.log(this.messages)
    })
  }
}
</script>
