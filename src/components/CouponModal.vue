<template>
  <div
    class="modal fade"
    id="couponModal"
    tabindex="-1"
    role="dialog"
    aria-labelledby="exampleModalLabel"
    aria-hidden="true"
    ref="modal"
  >
    <div class="modal-dialog" role="document">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="exampleModalLabel">優惠卷</h5>
          <button type="button" class="btn-close"
                  data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <div class="mb-3">
            <label for="title">標題</label>
            <input type="text" class="form-control" id="title"
                    placeholder="請輸入標題"
                    v-model="tempCoupon.title"
                    >
          </div>
          <div class="mb-3">
            <label for="coupon_code">優惠碼</label>
            <input type="text" class="form-control" id="coupon_code"
                    placeholder="請輸入優惠碼"
                    v-model="tempCoupon.code"
                    >
          </div>
          <div class="mb-3">
            <label for="due_date">到期日</label>
            <input type="date" class="form-control" id="due_date"
                    v-model="due_date"
                    >
          </div>
          <div class="mb-3">
            <label for="percent">折扣百分比</label>
            <input type="number" class="form-control" id="percent"
                    placeholder="請輸入折扣百分比"
                    v-model="tempCoupon.percent"
                    >
          </div>
          <div class="mb-3">
            <div class="form-check">
              <label class="form-check-label" for="is_enabled">
                是否啟用
              </label>
              <input class="form-check-input" type="checkbox"
                    id="is_enabled"
                    :true-value="1"
                    :false-value="0"
                    v-model="tempCoupon.is_enabled"
                    >
            </div>
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
          <button type="button" class="btn btn-primary"
          @click="$emit('add-coupon',tempCoupon)"
          >確認
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import ModalMixin from '@/mixins/ModalMixin'
export default {
  props: {
    coupon: {}
  },
  emits: ['add-coupon'],
  watch: {
    coupon () {
      // 單向數據流關係，要將外層傳進來的coupon，傳送給內層定義的tempCoupon中
      this.tempCoupon = this.coupon
      // 將時間格式改為 YYYY-MM-DD
      console.log(this.tempCoupon.due_date)
      const dateAndTime = new Date(this.tempCoupon.due_date).toISOString().split('T');
      [this.due_date] = dateAndTime
    },
    due_date () {
      this.tempCoupon.due_date = Math.floor(new Date(this.due_date))
    }
  },
  data () {
    return {
      modal: {},
      // tempCoupon ：編輯資料所儲存的地方
      tempCoupon: {},
      due_date: ''
    }
  },
  mixins: [ModalMixin]
}
</script>
