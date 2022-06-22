<template>
  <nav class="navbar navbar-expand-lg navbar-light bg-light">
    <div class="container-fluid">
      <a class="navbar-brand" href="#">範例練習：</a>
      <button
        class="navbar-toggler"
        type="button"
        data-bs-toggle="collapse"
        data-bs-target="#navbarNav"
        aria-controls="navbarNav"
        aria-expanded="false"
        aria-label="Toggle navigation"
      >
        <span class="navbar-toggler-icon">?</span>
      </button>
      <div class="collapse navbar-collapse" id="navbarNav">
        <div class="navbar-nav">
          <router-link to="/dashboard/products" class="nav-link">產品</router-link>
          <router-link to="/dashboard/orders" class="nav-link">訂單</router-link>
          <router-link to="/dashboard/coupons" class="nav-link">優惠券</router-link>
          <a href="#" @click.prevent="logout" class="nav-link">登出</a>
        </div>
      </div>
    </div>
  </nav>
</template>
<script>
export default {
  methods: {
    logout () {
      const api = `${process.env.VUE_APP_API}logout`
      console.log(api)
      this.$http.post(api)
        .then((res) => {
          if (res.data.success) {
            document.cookie = `myToken=;expires= ${new Date(0)}`
            this.$router.push('/login')
          }
          console.log(res)
        })
        .catch((err) => console.log(err.response))
    }
  }
}
</script>
