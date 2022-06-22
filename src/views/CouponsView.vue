<template>
  <div>
    <LoadingView :active="isLoading"></LoadingView>
    <div class="text-end mt-4">
      <button class="btn btn-primary"
        @click="openModal(true)"
      >
        建立新的優惠券
      </button>
    </div>
    <table class="table mt-4">
      <thead>
      <tr>
        <th>名稱</th>
        <th>代碼</th>
        <th>折扣百分比</th>
        <th>到期日</th>
        <th>是否啟用</th>
        <th>編輯</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="item in coupons" :key="item.id">
        <td>{{ item.title }}</td>
        <td>{{ item.code }}</td>
        <td>{{ item.percent }}%</td>
        <td>{{ $filters.date(item.due_date) }}</td>
        <td>
          <span v-if="item.is_enabled === 1" class="text-success">啟用</span>
          <span v-else class="text-muted">未起用</span>
        </td>
        <td>
          <div class="btn-group">
            <button class="btn btn-outline-primary btn-sm"
            @click="openModal(false,item)"
            >編輯</button>
            <button class="btn btn-outline-danger btn-sm"
            @click="openDeleteModal(item)"
            >刪除</button>
          </div>
        </td>
      </tr>
      </tbody>
    </table>
  </div>
  <coupon-modal
    ref="showCouponModal"
    :coupon="tempCoupon"
    @add-coupon="updateCoupon"
  ></coupon-modal>
  <delete-modal
    ref="showDeleteModal"
    :item="tempCoupon"
    @delete-item="deleteCoupon"
  ></delete-modal>
  <pagination :pages="pagination"
    @change-page="getCoupons"
  ></pagination>
</template>

<script>
import couponModal from '../components/CouponModal.vue'
import deleteModal from '../components/DeleteModal.vue'
import pagination from '../components/PaginationView.vue'
export default {
  components: {
    couponModal,
    deleteModal,
    pagination
  },
  data () {
    return {
      coupons: [],
      tempCoupon: {
      // 原本預設寫在這，但預設被覆蓋掉了，因此索性把這裡刪掉直接在openModal那加入預設。
      // is_enabled: 0,
      // title: '',
      // percent: 100,
      // code: ''
      },
      pagination: {},
      isNew: false,
      isLoading: false
    }
  },
  methods: {
    getCoupons (page = 1) {
      this.isLoading = true
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/coupons?page=${page}`
      // console.log(api)
      this.$http.get(api)
        .then((res) => {
          this.isLoading = false
          this.coupons = res.data.coupons
          this.pagination = res.data.pagination
          console.log(res.data)
        })
        .catch((err) => console.log(err.response))
    },
    updateCoupon (item) {
      this.tempCoupon = item
      console.log('up', this.isNew)
      // 新增
      if (this.isNew) {
        const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/coupon`
        this.$http.post(api, { data: this.tempCoupon })
          .then(res => {
            this.getCoupons()
            console.log(res, this.tempCoupon)
          })
          .catch(err => console.log(err.response))
        this.$refs.showCouponModal.hideModal()
      } else {
        // 編輯
        const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/coupon/${this.tempCoupon.id}`
        this.$http.put(api, { data: this.tempCoupon })
          .then(res => {
            this.getCoupons()
            console.log(res)
          })
          .catch(err => console.log(err.response))
        this.$refs.showCouponModal.hideModal()
      }
    },
    openModal (isNew, item) {
      this.isNew = isNew
      console.log(isNew)
      if (this.isNew) {
        this.tempCoupon = {
          // 由於預設被覆蓋掉，所以索性把上面預設刪除，直接在這裡設定預設值
          title: '',
          percent: 100,
          code: '',
          // 因為在新增時候如果沒點checkbox，讓他先吃到true&false的話，會一直是空值，雖然搞不懂為什麼會這樣，但試著在這裡直接填寫預設，就解決問題了。
          is_enabled: 0,
          due_date: new Date().getTime()

          // 日期轉換格式-老師寫法
          // due_date: new Date().getTime() / 1000
        }
      } else {
        this.tempCoupon = { ...item }
      }
      this.$refs.showCouponModal.showModal()
    },
    openDeleteModal (item) {
      this.tempCoupon = { ...item }
      this.$refs.showDeleteModal.showModal()
    },
    deleteCoupon () {
      // console.log(this.tempCoupon.id)
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/coupon/${this.tempCoupon.id}`
      this.$http.delete(api)
        .then(res => {
          console.log(res)
          this.getCoupons()
        })
        .catch(err => console.log(err.response))
      this.$refs.showDeleteModal.hideModal()
    }
  },
  created () {
    this.getCoupons()
  }
}
</script>
