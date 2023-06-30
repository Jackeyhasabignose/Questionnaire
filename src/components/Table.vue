<script>
export default {
  props: {
    columns: {
      type: Array,
      required: true
    },
    data: {
      type: Array,
      required: true
    },
    itemsPerPage: {
      type: Number,
      default: 15
    },
    showControl: {
      type: Boolean,
      default: false
    },
    showEditButton: {
      type: Boolean,
      default: false
    },
    showDeleteButton: {
      type: Boolean,
      default: false
    },
    showCompleteButton: {
      type: Boolean,
      default: false
    },
    showChooseButton: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      currentPage: 1,
      displayedPages: [],
      
    };
  },
  computed: {
    totalPages() {
      return Math.ceil(this.data.length / this.itemsPerPage);
    },
    paginatedData() {
      const startIndex = (this.currentPage - 1) * this.itemsPerPage;
      const endIndex = startIndex + this.itemsPerPage;
      const sortedData = this.data.slice().reverse(); // 將資料逆序排列
      return sortedData.slice(startIndex, endIndex);
    }
  },
  methods: {
    previousPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
      }
      this.updateDisplayedPages();
    },
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++;
      }
      this.updateDisplayedPages();
    },
    goToPage(page) {
      if (page >= 1 && page <= this.totalPages) {
        this.currentPage = page;
        this.updateDisplayedPages();
      }
    },
    updateDisplayedPages() {
      const range = 2;
      const startPage = Math.max(1, this.currentPage - range);
      const endPage = Math.min(this.totalPages, this.currentPage + range);
      this.displayedPages = Array.from(
        { length: endPage - startPage + 1 },
        (_, i) => startPage + i
      );
    },
    editItem(item) {
      this.$emit('edit', item);
    },
    deleteItem(item) {
      this.$emit('delete', item);
    },
    completeItem(item) {
      this.$emit('complete', item);
    },
    chooseItem(item) {
      this.selectedItemId = item.questionnaireId; // 設置選中的項目的 ID
      this.$emit('choose', item);
    },
    handleRadioClick(item) {
      this.selectedItemId = item.questionnaireId; // 設置選中的項目的 ID
      console.log(item)
    }
  }
};

</script>

<template>
    <div class="position-relative w-95%">
      <table class="table mb-5 table-striped table-hover">
        <thead>
          <tr class="table-dark">
            <th></th>
            <th v-for="column in columns" :key="column.key">{{ column.column }}</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in paginatedData" :key="item.id">
            <td>
              <input type="radio" v-model="selectedItemId" :value="item.questionnaireId" @click="handleRadioClick(item)">
            </td>
            <td v-for="column in columns" :key="column.key">{{ item[column.key] }}</td>
          </tr>
        </tbody>
      </table>
      <!-- 分頁按鈕等... -->
    </div>
  </template>
  

<style lang="scss" scoped>
.table-fixed {
    table-layout: fixed;
    width: 100%;
}

.pagination .page-link:hover {
    cursor: pointer;
}
</style>
