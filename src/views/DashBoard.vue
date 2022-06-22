<template>

  <Navbar></Navbar>
  <div class="container-fluid">
    <Toast></Toast>
    <router-view />
  </div>

</template>

<script>
import emitter from '@/methods/emitter'
import Navbar from '../components/NavBar.vue'
import Toast from '@/components/ToastList.vue'
export default {
  components: {
    Navbar,
    Toast
  },
  provide () {
    return {
      emitter
    }
  },
  created () {
    const token = document.cookie.replace(/(?:(?:^|.*;\s*)myToken\s*=\s*([^;]*).*$)|^.*$/, '$1')
    this.$http.defaults.headers.common.Authorization = token
    // console.log(token)

    const api = `${process.env.VUE_APP_API}api/user/check`
    // console.log(api)
    this.$http.post(api, this.user)
      .then((res) => {
        if (!res.data.success) {
          this.$router.push('/login')
        }
        // console.log(res)
      })
      .catch((err) => console.log(err.response))
  }
}
</script>
