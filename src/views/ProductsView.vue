<template>
  <LoadingView :active="isLoading"></LoadingView>
  <div class="text-end">
    <button class="btn btn-primary" type="button" @click="openModal(true)">
      新增產品
    </button>
  </div>
  <table class="table mt-4">
    <thead>
      <tr>
        <th>分類</th>
        <th>產品名稱</th>
        <th>產品圖片</th>
        <th>原價</th>
        <th>售價</th>
        <th>是否啟用</th>
        <th>編輯</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="item in products" :key="item.id">
        <td>{{item.category}}</td>
        <td>{{item.title}}</td>
        <td><img :src="item.imageUrl" alt=""></td>
        <td class="text-right">
          {{$filters.currency(item.origin_price)}}
        </td>
        <td class="text-right">
          {{$filters.currency(item.price)}}
        </td>
        <td>
          <span class="text-success" v-if="item.is_enabled">啟用</span>
          <span class="text-muted" v-else>未啟用</span>
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
  <product-modal ref="showProductModal"
  :product="tempProduct"
  @add-product="updateProduct"
  ></product-modal>
  <delete-modal ref="showDeleteModal"
  :item="tempProduct"
  @delete-item="deleteProduct"
  ></delete-modal>
  <pagination :pages="pagination"
    @change-page="getProducts"
  ></pagination>
</template>

<script>
import productModal from '../components/ProductModal.vue'
import deleteModal from '../components/DeleteModal.vue'
import pagination from '../components/PaginationView.vue'
// import { currency } from '@/methods/filters'
export default {
  data () {
    return {
      products: [],
      pagination: {},
      tempProduct: {},
      isNewProduct: false,
      isLoading: false
    }
  },
  components: {
    productModal,
    deleteModal,
    pagination
  },
  inject: ['emitter'],
  methods: {
    // currency,
    getProducts (page = 1) {
      this.isLoading = true
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/products?page=${page}`
      // console.log(api)
      this.$http.get(api)
        .then((res) => {
          if (res.data.success) {
            this.isLoading = false
            // console.log(res.data)
            this.products = res.data.products
            this.pagination = res.data.pagination
            // console.log(this.products, this.pagination)
          }
        })
        .catch((err) => console.log(err.response))
    },
    // 開啟新增＆編輯
    openModal (isNewProduct, item) {
      // console.log(isNewProduct, item)
      if (isNewProduct) {
        // 每次都清空資料欄
        this.tempProduct = {}
      } else {
        this.tempProduct = { ...item }
      }
      this.isNewProduct = isNewProduct
      const productModalComponent = this.$refs.showProductModal
      productModalComponent.showModal()
    },
    updateProduct (item) {
      this.tempProduct = item

      // 新增狀態
      let api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/product`
      let httpMethod = 'post'

      // 編輯狀態
      if (!this.isNewProduct) {
        api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/product/${item.id}`
        httpMethod = 'put'
      }

      const productModalComponent = this.$refs.showProductModal
      this.$http[httpMethod](api, { data: this.tempProduct })
        .then((res) => {
          // console.log(res)
          productModalComponent.hideModal()
          // 製作吐司列表（ 查看回傳成功與否）
          if (res.data.success) {
            this.getProducts()
            this.emitter.emit('pushMsg', {
              style: 'success',
              title: '更新成功'
            })
          } else {
            this.emitter.emit('pushMsg', {
              style: 'danger',
              title: '更新失敗',
              content: res.data.message.join('、')
            })
          }
        })
        .catch((err) => console.log(err.response))
    },
    // 開啟刪除
    openDeleteModal (item) {
      this.tempProduct = { ...item }
      const deleteModalComponent = this.$refs.showDeleteModal
      deleteModalComponent.showModal()
      // console.log(item)
    },
    deleteProduct () {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/product/${this.tempProduct.id}`
      this.$http.delete(api, { data: this.tempProduct })
        .then((res) => {
          // console.log(res)
          const deleteModalComponent = this.$refs.showDeleteModal
          deleteModalComponent.hideModal()
          if (res.data.success) {
            this.getProducts()
            this.emitter.emit('pushMsg', {
              style: 'success',
              title: '刪除成功'
            })
          }
        })
        .catch((err) => console.log(err.response))
    }
  },
  created () {
    this.getProducts()
  }
}
</script>
