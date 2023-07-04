<script>
export default {
    methods: {
        push() {
            console.log("push method called");
            this.$emit("pushOutside")
        }
    }
}
</script>

<template>
    <div class="frame">
        <div class="modal">
            <i class="fa-solid fa-x" @click="push"></i>
            <slot></slot>
        </div>
        <div class="bg"></div>
    </div>
</template>

<style lang="scss" scoped>
.frame {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;

    .modal {
        width: 65vw;
        height: 65vh;
        background-color: white;
        border-radius: 0.5rem;
        position: relative;
        display: flex;
        flex-direction: column;
        align-items: center;
        overflow-y: auto;

        i {
            position: absolute;
            top: 10px;
            right: 10px;
            cursor: pointer;

        }
    }

    .bg {
        position: absolute;
        top: 0;
        left: 0;
        width: inherit;
        height: inherit;
        background-color: black;
        opacity: 0.8;
        z-index: -1;
    }
}
</style>