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
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header bg-danger">
          <h5 class="modal-title" id="staticBackdropLabel">刪除{{item.title}}</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          確認要刪除{{item.title}}嗎？
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-outline-secondary" data-bs-dismiss="modal">取消</button>
          <button type="button" class="btn btn-danger"
          @click="$emit('delete-item')"
          >確認</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ModalMixin from '@/mixins/ModalMixin'
export default {
  props: {
    item: {}
  },
  emits: ['delete-item'],
  data () {
    return {
      modal: {}
    }
  },
  mixins: [ModalMixin]
}
</script>
