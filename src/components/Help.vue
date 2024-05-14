<script setup lang="ts">
import { shallowRef } from 'vue';
import IconQuestion from './icons/IconQuestion.vue';

const active = shallowRef(false)
</script>

<template>
    <div class="help btn">
        <button @click="active = !active">
            <IconQuestion />
        </button>
    </div>
    <div class="help content" v-if="active">
        <div class="hotkeys">
            <h2>Hotkeys</h2>
            <table>
                <tr class="header-row">
                    <td>Action</td>
                    <td>Key</td>
                </tr>
                <tr>
                    <td>Snap size via wheel</td>
                    <td>Alt</td>
                </tr>
                <tr>
                    <td>Fast Y</td>
                    <td>Shift</td>
                </tr>
                <tr>
                    <td>Fast Z</td>
                    <td>Ctrl</td>
                </tr>
                <tr>
                    <td>Focus X axis</td>
                    <td>X / S / 1</td>
                </tr>
                <tr>
                    <td>Focus Y axis</td>
                    <td>Y / D / 2</td>
                </tr>
                <tr>
                    <td>Focus Z axis</td>
                    <td>Z / F / 3</td>
                </tr>
                <tr>
                    <td>Snap size increase</td>
                    <td>Up / K</td>
                </tr>
                <tr>
                    <td>Snap size decrease</td>
                    <td>Down / J</td>
                </tr>
                <tr>
                    <td>Rotate plus</td>
                    <td>Left / H</td>
                </tr>
                <tr>
                    <td>Rotate minus</td>
                    <td>Right / L</td>
                </tr>
                <tr>
                    <td>Switch local rotation</td>
                    <td>A / G</td>
                </tr>
                <tr>
                    <td>Reset</td>
                    <td>Space / R / C</td>
                </tr>
            </table>
        </div>
        <div>
            <p>Use wheel to change angle or snap size</p>
        </div>
    </div>
</template>

<style scoped>
.help {
    position: fixed;
}

.btn {
    z-index: 2;
    top: 25px;
    right: 25px;
}

button {
    width: 50px;
    height: 50px;
    padding: 0 15px 0 15px;
    border: 1px solid var(--c-btn-border);
    background-color: var(--c-btn-bg);
    border-radius: 50%;
    cursor: pointer;
    z-index: 2;
}

button:hover {
    background-color: var(--c-btn-bg-hover);
}

button:active {
    background-color: var(--c-btn-bg-active);
}

.content {
    z-index: 1;
    top: 15px;
    right: 15px;
    border: 1px solid var(--c-table-border);
    background-color: var(--c-table-bg);
    padding: 0 70px 20px 70px;
    display: flex;
    flex-direction: column;
}

table {
    border-collapse: collapse;
    line-height: 1;
    color: var(--c-text-help)
}

.header-row {
    color: var(--c-text-accent);
    background-color: var(--c-table-header);
}

td {
    border: 1px solid var(--c-table-border);
    padding: 10px;
}
</style>