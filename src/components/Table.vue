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
            <input type="radio" :checked="selectedItemId === item.questionnaireId" :value="item.questionnaireId" @click="handleRadioClick(item)">
          </td>
          <td v-for="(column, index) in columns" :key="column.key">
            <template v-if="column.key === 'options' && column.hasButton">
              <div v-for="(option, optionIndex) in item[column.key]" :key="option">
                {{ option }}
              </div>
              <button v-if="index === columns.length - 1" type="button" class="btn btn-sm btn-secondary" @click="openOptionsDialog(item)">
                變更
              </button>
            </template>
            <template v-else>
              {{ item[column.key] }}
            </template>
          </td>
        </tr>
      </tbody>
    </table>
    <!-- 分頁按鈕等... -->
  </div>
  
  
  <Modal v-if="isOptionsDialogOpen" @pushOutside="closeOptionsDialog">
  <h2>填寫選項</h2>
  <form>
    <input v-model="optionInput" type="text" placeholder="輸入選項">
    <button type="button" @click="saveOptions">儲存</button>
  </form>
</Modal>

</template>

<script>
import Modal from './Modal.vue';
export default {
  components: {      
     Modal
    },
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
    },
    selectedItemId: {
      type: Number,
      default: null
    }
  },
  data() {
    return {
      currentPage: 1,
      displayedPages: [],
      isOptionsDialogOpen: false,
      optionInput: '',
      selectedQuestion: null
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
      this.$emit('choose', item);
    },
    handleRadioClick(item) {
      this.$emit('update:selectedItemId', item.questionnaireId);
    },
    openOptionsDialog(item) {
      this.$emit('openOptionsDialog', item);
      this.selectedQuestion = item;
      this.isOptionsDialogOpen = true;
    },
    closeOptionsDialog() {
      this.isOptionsDialogOpen = false;
    },
    saveOptions() {
      if (this.selectedQuestion) {
        this.selectedQuestion.options.push(this.optionInput);
      }
      this.isOptionsDialogOpen = false;
      this.optionInput = '';
    }
  }
};
</script>

<style lang="scss" scoped>
.table-fixed {
  table-layout: fixed;
  width: 100%;
}

.pagination .page-link:hover {
  cursor: pointer;
}

.modal {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: white;
  padding: 20px;
  border-radius: 5px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.modal h2 {
  margin-top: 0;
}

.modal form {
  display: flex;
  gap: 10px;
}
</style>
