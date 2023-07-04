<template>
    <div class="container">
        <div class="rectangle">
            <template v-if="currentPage === 'page1'">
                <!-- 第一頁內容 -->
                <h1 class="text-center">新增問卷</h1>
                <div class="oo">
                    <p class="m-0">問卷名稱</p>
                    <input v-model="dataField1" placeholder="請輸入問卷題目" style="width: 250px; height: 40px;">
                </div>
                <div class="ooo">
                    <p class="m-0">描述內容</p>
                    <input v-model="dataField2" placeholder="請輸入問卷描述" style="width: 400px; height: 70px;">
                </div>
                <div class="oooo">
                    <p class="m-0">開始時間</p>
                    <input type="date" v-model="dataField3" placeholder="請輸入問卷描述" style="width: 250px; height: 40px;">
                </div>
                <div class="ooooo">
                    <p class="m-0">結束時間</p>
                    <input type="date" v-model="dataField4" placeholder="請輸入問卷描述" style="width: 250px; height: 40px;">
                </div>
                <button type="button" @click="submitForm" class="btn next-button btn-dark">下一頁</button>

                <div class="text-center">
                    <button type="button" class="btn submit-button btn-dark" @click="submitForm">送出</button>
                </div>
                <div class="pp">
                    <router-link to="/backhome" class="btn submit-buttonn btn-dark">取消</router-link>
                </div>
            </template>

            <!-- 其他頁面的內容 -->
        </div>
    </div>
</template>
  
<script>
export default {
    data() {
        return {
            currentPage: 'page1', // 頁面狀態
            dataField1: '', // 資料 1 的值
            dataField2: '', // 資料 2 的值
            dataField3: '', // 資料 3 的值
            dataField4: '', // 資料 4 的值
            questionnaireId: null, // 問卷的 questionnaire_id
        };
    },
    methods: {
        nextPage() {
            this.$router.push('/addquestion/' + this.questionnaireId); // 將 questionnaireId 作為參數傳遞
        },
        previousPage() {
            if (this.currentPage === 'page2') {
                this.currentPage = 'page1';
            } else if (this.currentPage === 'page3') {
                this.currentPage = 'page2';
            }
        },
        submitForm() {
            if (this.validateFields()) {
                if (this.dataField1.trim() === '') {
                    console.log('問卷名稱是必填項目');
                    return;
                }

                const requestData = {
                    questionnaireName: this.dataField1,
                    description: this.dataField2,
                    startTime: this.dataField3,
                    endTime: this.dataField4
                };

                // 執行 fetch 或 @PostMapping 請求提交問卷數據
                fetch('http://localhost:8080/Add_questionnaire', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify(requestData),
                })
                    .then((response) => response.json())
                    .then((data) => {
                        // 處理回應數據
                        console.log(data);
                        this.questionnaireId = data.questionnaire[0].questionnaireId; // 儲存問卷的 questionnaire_id
                        console.log(this.questionnaireId)
                        // 導航到 '/addquestionnaire' 並傳遞 questionnaireId
                        this.$router.push({ path: '/addquestion', query: { questionnaireId: this.questionnaireId } });
                    })
                    .catch((error) => {
                        // 處理錯誤
                        console.error('問卷提交失敗', error);
                    });
            } else {
                // 輸入欄位驗證失敗的處理
                console.log('請填寫完整表單');
            }
        },

        validateFields() {
            // 在此進行輸入欄位的驗證
            // 檢查是否所有輸入欄位都已填寫完畢
            if (this.dataField1 && this.dataField2 && this.dataField3 && this.dataField4) {
                return true;
            }
            return false;
        },
        previousPage() {
            if (this.currentPage === 'page2') {
                this.currentPage = 'page1';
            }
        },
    },
};
</script>
  
<style lang="scss" scoped>
.container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}

.rectangle {
    position: relative;
    width: 50rem;
    height: 35rem;
    border: 1px solid black;
    padding: 20px;
    border-radius: 5rem;
}

.next-button {
    position: absolute;
    bottom: 10px;
    right: 3rem;
}

.submit-buttonn {
    position: absolute;
    bottom: 10px;
    left: 7rem;
}

.oo {
    padding-top: 3rem;
    display: flex;
    justify-content: space-evenly;
    align-items: center;
}

.ooo {
    padding-top: 3rem;
    padding-left: 4.5rem;
    display: flex;
    justify-content: space-evenly;
    align-items: center;
}

.oooo {
    padding-top: 3rem;
    display: flex;
    justify-content: space-evenly;
    align-items: center;
}

.ooooo {
    padding-top: 3rem;
    display: flex;
    justify-content: space-evenly;
    align-items: center;
}

.submit-button {
    position: absolute;
    bottom: 10px;
}
</style>
