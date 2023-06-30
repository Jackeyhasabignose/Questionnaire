<script>
export default {
    data() {
        return {
            keyword: '',
            startTime: '',
            endTime: '',
        }
    },
    methods: {
        search() {
  // 檢查是否指定了開始時間和結束時間
  if (this.startTime && this.endTime) {
    // 觸發日期範圍搜尋事件並傳遞開始時間和結束時間
    this.$emit('dateRangeSearch', this.startTime, this.endTime);
  } else {
    // 觸發關鍵字搜尋事件並傳遞關鍵字
    this.$emit('search', this.keyword, null, null); // 添加 `null` 參數
  }
}
  }

}
</script>

<template>
    <div class="mid">
        <div class="mid1">
            <h2>問卷標題</h2>
            <div class="input">
                <input type="text" v-model="keyword" placeholder="輸入關鍵字">
            </div>
        </div>
        <div class="bot">
            <h2>開始時間/結束時間</h2>
            <div class="input1">
                <input type="date" v-model="startTime" placeholder="開始日期">
                <input type="date" v-model="endTime" placeholder="結束日期">
            </div>
        </div>
        <button type="button" @click="search" class="btn btn-primary m-0">查詢</button>
        <div>
        </div>
    </div>
</template>
  
<style lang="scss" scoped>
.mid {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 700px;
    height: 170px;
    border: 1px solid black;
    padding: 1rem;
    border-radius: 4rem;
}

.btn {
    width: 200px;
    height: 40px;
}



.input {
    margin-top: 4px;
    margin-left: 15px;
}

.mid1 {
    display: flex;
}

.bot {
    display: flex;
}

.btn {
    margin-left: 18rem;
    width: 5rem;
    /* 加入以下樣式 */
    background-color: #007bff;
    color: #fff;
    border: none;
    padding: 0.5rem 1rem;
    cursor: pointer;
    transition: transform 0.3s;
    /* 加入過渡效果 */
}

.btn:hover {
    background-color: #0056b3;
    transform: scale(1.1);
    /* 按鈕縮放 1.1 倍 */
}

.btn:active {
    background-color: #003080;
    transform: scale(0.7);
    /* 按鈕縮放 0.9 倍 */
}

.input1 {
    margin-top: 5px;
    width: 20rem;
    height: 2rem;
    display: flex;
    justify-content: space-between;
}

btn {
    width: 5rem;
}
</style>