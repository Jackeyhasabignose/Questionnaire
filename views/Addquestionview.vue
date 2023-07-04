<template>
    <div class="container">
        <div class="rectangle">
            <h1 class="text-center">新增問題</h1>
            <button type="button" @click="lastPage" class="btn last-button btn-dark">上一頁</button>
            <div class="oo">
                <p class="m-0">問題</p>
                <input v-model="questionText" placeholder="請輸入問題名稱" style="width: 250px; height: 40px;">
                <p class="m-0">必填</p>
                <input type="checkbox" v-model="isRequired">
                <button type="button" @click="saveQuestion" class="btn btn-dark">新增</button>
            </div>
            <div class="ooo">
                <p class="m-0">問題類型</p>
                <select v-model="questionType" style="width: 250px; height: 40px;">
                    <option value="文字">文字</option>
                    <option value="單選方塊">單選方塊</option>
                    <option value="複選方塊">複選方塊</option>
                </select>
            </div>

            <!-- 使用 TableView 組件展示問題列表 -->
            <TableView :columns="tableColumns" :data="questions" :isOptionsDialogOpen="isOptionsDialogOpen"
                @openOptionsDialog="openOptionsDialog"></TableView>
            <div class="text-center">
                <button type="button" class="btn submit-button btn-dark" @click="homePage">問題儲存</button>
            </div>

            <!-- 選項填寫的小視窗 -->
            <!-- 選項填寫的小視窗 -->
<Modal v-if="isOptionsDialogOpen" @pushOutside="closeOptionsDialog">
    <h2>填寫選項</h2>
    <form>
        <input v-model="optionInput" type="text" placeholder="輸入選項">
        <button type="button" @click="saveOptions">儲存</button>
    </form>
</Modal>

        </div>
    </div>
</template>
  
<script>
import TableView from '../src/components/Table.vue';
import Modal from '../src/components/Modal.vue';

export default {
    components: {
        TableView,
        Modal
    },
    data() {
        return {
            tableColumns: [
                { key: 'questionNumber', column: '#' },
                { key: 'questionText', column: '問題名稱' },
                { key: 'questionType', column: '類型' },
                { key: 'isRequired', column: '必填' },
                { key: 'options', column: '選項', hasButton: true }
            ],
            questionText: '',
            questionType: '文字',
            isRequired: false,
            questions: [],
            isOptionsDialogOpen: false,
            selectedQuestion: null,
            optionInput: '',
        };
    },
    methods: {
        lastPage() {
            this.$router.push('/addquestionnaire');
        },
        homePage() {
            this.$router.push('/backhome');
        },
        saveQuestion() {
            const requestBody = {
                questionText: this.questionText,
                isRequired: this.isRequired,
                questionType: this.questionType,
                questionnaire: {
                    questionnaireId: this.$route.query.questionnaireId
                }
            };

            fetch('http://localhost:8080/Add_question', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(requestBody)
            })
                .then(response => response.json())
                .then(data => {
                    // 处理请求成功后的逻辑
                    console.log(data);

                    // 将新增的问题对象添加到questions数组
                    const newQuestion = {
                        questionNumber: this.questions.length + 1,
                        questionText: this.questionText,
                        questionType: this.questionType,
                        isRequired: this.isRequired,
                        questionId: data.questionId, // 从返回的数据中获取questionId
                        questionnaireId: this.$route.query.questionnaireId, // 使用路由参数中的questionnaireId
                        options: [] // 可根据需要添加选项数据
                    };
                    this.questions.push(newQuestion);
                })
                .catch(error => {
                    // 处理请求失败后的逻辑
                    console.error(error);
                });

            // 清空输入框和复选框的值
            this.questionText = '';
            this.questionType = '文字';
            this.isRequired = false;
        }



        ,
        openOptionsDialog(item) {
            console.log('openOptionsDialog is called');
            this.isOptionsDialogOpen = true;
            this.selectedQuestion = item;
            console.log('selectedQuestion:', this.selectedQuestion);
        },
        closeOptionsDialog() {
            console.log('closeOptionsDialog is called')
            this.isOptionsDialogOpen = false;
        },
        saveOptions() {
            if (this.selectedQuestion) {
                const optionRequestBody = {
                    optionText: this.optionInput.split(';').map(option => option.trim()),
                    questionnaire: {
                        questionnaireId: this.selectedQuestion.questionnaireId
                    },
                    question: {
                        questionId: this.selectedQuestion.questionId
                    }
                };

                fetch('http://localhost:8080/Add_option', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify(optionRequestBody)
                })
                    .then(response => response.json())
                    .then(optionData => {
                        // 处理选项请求成功后的逻辑
                        console.log(optionData);

                        // 将新增的选项添加到相应问题对象的options数组中
                        const questionIndex = this.questions.findIndex(
                            question => question.questionId === this.selectedQuestion.questionId
                        );
                        if (questionIndex !== -1) {
                            this.questions[questionIndex].options = optionData.options;
                        }
                    })
                    .catch(error => {
                        // 处理选项请求失败后的逻辑
                        console.error(error);
                    });
            }

            this.isOptionsDialogOpen = false;
            this.optionInput = '';
        }



    }
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

.submit-button {
    position: absolute;
    bottom: 10px;
}

.last-button {
    position: absolute;
    bottom: 10px;
    left: 3rem;
}

.oo {
    padding-right: 9rem;
    padding-top: 3rem;
    display: flex;
    justify-content: space-evenly;
    align-items: center;
}

.ooo {
    display: flex;
    justify-content: space-evenly;
    align-items: center;
    padding-top: 3rem;
    padding-right: 23.5rem;
    padding-bottom: 2rem;
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
  