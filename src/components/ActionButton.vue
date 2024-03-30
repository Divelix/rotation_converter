<template>
    <button>
        <slot name="icon"></slot>
    </button>
</template>
<style scoped>
button {
    width: 50px;
    height: 50px;
    text-align: center;
    justify-content: center;
    padding: 10px;
    background-color: #282828;
    border-radius: 20%;
    border: 1px solid var(--c-divider-dark-1);
    cursor: pointer;
}

button:hover {
    background-color: #333;
}

button:active {
    background-color: #555;
}
</style>