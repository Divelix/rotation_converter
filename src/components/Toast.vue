<script setup lang="ts">
defineProps({
    msg: String
})
</script>

<template>
    <div class="toast-wrapper">
        <div class="toast">
            {{ msg }}
        </div>
    </div>
</template>

<style scoped>
.toast-wrapper {
    position: fixed;
    left: 50%;
    bottom: 20px;
    transform: translate(-50%, 0);
}

.toast {
    color: #fff;
    background-color: #1e6b00;
    padding: 20px;
    border-radius: 20px;
    text-align: center;
    max-width: 400px;
    margin: 0 auto;
}
</style>