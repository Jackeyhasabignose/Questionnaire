<script>
import SearchView from '../src/components/Search.vue';
import TableView from '../src/components/Table.vue';

export default {
    components: {
        SearchView,
        TableView
    },
    data() {
        return {
            tableColumns: [
                { key: `questionnaireId`, column: "問卷編號" },
                { key: `questionnaireName`, column: "問卷名稱" },
                { key: `status`, column: "狀態" },
                { key: `startTime`, column: "開始時間" },
                { key: `endTime`, column: "結束時間" }
            ],
            allQuestionnaireData: [], // 注意屬性名稱改為小寫
            filteredData: [], // 新增過濾後的資料屬性
            selectedItemId: null,
            items: [], // 你的資料陣列
        };
    },
    computed: {
        formattedData() {
            return this.filteredData.map(item => {
                const formattedStartTime = new Date(item.startTime).toISOString().split('T')[0];
                return {
                    ...item,
                    startTime: formattedStartTime
                };
            }).sort((a, b) => {
                return new Date(a.startTime) - new Date(b.startTime); // 升序排序
            });
        }
    },
    methods: {
        fetchAllQuestionnaireData() {
            fetch("http://localhost:8080/Find_all_questionnaire")
                .then(res => {
                    if (!res.ok) {
                        throw new Error(`HTTP error! status: ${res.status}`);
                    }
                    return res.json();
                })
                .then(data => {
                    console.log(data);
                    this.allQuestionnaireData = data.questionnaire;
                    this.filteredData = data.questionnaire; // 初始時過濾後的資料為全部資料
                })
                .catch(error => {
                    console.error('There was a problem with the fetch operation:', error);
                });
        },

        fetchQuestionnairesWithinDateRange(startTime, endTime) {
            const url = 'http://localhost:8080/find_all_questionnaires_within_date_range';

            fetch(url, {
                method: 'POST', // 將請求方法改為 POST
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ startTime, endTime }) // 將參數作為請求主體發送
            })
                .then(res => {
                    if (!res.ok) {
                        throw new Error(`HTTP error! status: ${res.status}`);
                    }
                    return res.json();
                })
                .then(data => {
                    console.log(data);
                    this.filteredData = data.questionnaire;
                })
                .catch(error => {
                    console.error('There was a problem with the fetch operation:', error);
                });
        },




        handleSearch(keyword, startTime, endTime) {
            // 如果有指定開始時間和結束時間，則使用時間區間進行過濾
            if (startTime && endTime) {
                const filteredData = this.allQuestionnaireData.filter(item => {
                    const itemKeyword = item.questionnaireName.toLowerCase().trim();
                    const itemStartTime = new Date(item.startTime);
                    const itemEndTime = new Date(item.endTime);
                    return (
                        itemKeyword.includes(keyword.toLowerCase().trim()) &&
                        itemStartTime >= startTime && // 移除 `new Date()` 轉換
                        itemEndTime <= endTime // 移除 `new Date()` 轉換
                    );
                });
                this.filteredData = filteredData;
            } else {
                // 如果未指定開始時間和結束時間，則只過濾關鍵字
                const filteredData = this.allQuestionnaireData.filter(item => {
                    const itemKeyword = item.questionnaireName.toLowerCase().trim();
                    return itemKeyword.includes(keyword.toLowerCase().trim());
                });
                this.filteredData = filteredData;
            }
        },


        handleDateRangeSearch(startTime, endTime) {
            this.fetchQuestionnairesWithinDateRange(startTime, endTime);
        },
        deleteSelectedItems() {
  const selectedItemId = this.selectedItemId;
  if (!selectedItemId) {
    console.error('No questionnaire ID selected for deletion.');
    return;
  }

  fetch('http://localhost:8080/Delete_questionnaire', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({ questionnaireId: selectedItemId })
  })
    .then(response => {
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }
      return response.json();
    })
    .then(data => {
      console.log(data);
    })
    .catch(error => {
      console.error(error);
    });
}





    },
    mounted() {
        this.fetchAllQuestionnaireData();
    }
};
</script>


<template>
    <div class="oo">
        <router-link to="/">使用者頁面</router-link>
    </div>
    <div class="container">
        <div class="mt-5">
            <SearchView @search="handleSearch" @dateRangeSearch="handleDateRangeSearch" />
        </div>
        <div class="ooo icon-container">
            <i class=" fas fa-plus " @click="addNewItem"></i>
            <i class=" fas fa-trash" @click="deleteSelectedItems"></i>
        </div>
        <div class="mt-2">
            <TableView :columns="tableColumns" :data="formattedData" :selectedItemId="selectedItemId" />
        </div>
    </div>
</template>

<style lang="scss" scoped>
.container {
    display: flex;
    flex-direction: column;
    align-items: center;
}

.oo {
    text-decoration: none;

}

.ooo {
    padding-right: 31rem;
    padding-top: 5px;
}
</style>
