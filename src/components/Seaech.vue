<script>
</script>

<template>
    <div class="mid">
        <div class="mid1">

            <h2>問卷標題</h2>
            <div class="input">
                <input type="text">
            </div>
        </div>
        <div class="bot">
            <h2>開始時間/結束時間</h2>
            <div class="input1">
                <input type="datetime-local">
                <input type="datetime-local">



            </div>
            
        
            
        </div>
        <button type="button" class="btn btn-primary">查詢</button>
        <div>
        </div>
    </div>
</template>

<style lang="scss" scoped>
.mid {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 700px;
    height: 150px;
    border: 1px solid black;

    padding: 1rem 1rem
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
        transition: transform 0.3s; /* 加入過渡效果 */
    }

    .btn:hover {
        background-color: #0056b3;
        transform: scale(1.1); /* 按鈕縮放 1.1 倍 */
    }

    .btn:active {
        background-color: #003080;
        transform: scale(0.7); /* 按鈕縮放 0.9 倍 */
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