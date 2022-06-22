<template>
  <ul class="pagination justify-content-center">
    <li class="page-item" :class="{'disabled':!pages.has_pre}">
      <a class="page-link" href="#" aria-label="Previous"
      @click.prevent="updatedPage(pages.current_page - 1)"
      >
        <span aria-hidden="true">&laquo;</span>
      </a>
    </li>
    <li class="page-item"
      v-for="page in pages.total_pages" :key="page"
      :class="{'active':pages.current_page === page}"
    >
      <a class="page-link" href="#"
        @click.prevent="updatedPage(page)"
      >{{page}}</a>
    </li>
    <li class="page-item" :class="{'disabled':!pages.has_next}">
      <a class="page-link" href="#" aria-label="Next"
      @click.prevent="updatedPage(pages.current_page + 1)"
      >
        <span aria-hidden="true">&raquo;</span>
      </a>
    </li>
  </ul>
</template>
<script>
export default {
  props: ['pages'],
  methods: {
    updatedPage (page) {
      this.$emit('change-page', page)
      // console.log(page)
    }
  }
}
</script>
