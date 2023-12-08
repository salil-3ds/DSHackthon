
<template>
    <div class="canvas" id="mainCanvas" style="padding: 150px; width: 100vw; height: 100vh; display: flex; justify-content: center; align-items: center;">
        <span class="block draggable" id="h1_block">
            
        </span>
        <div class="block draggable" id="h2_block" style="left: 100px; top: 100px; font-size: small; display: flex; justify-content: center; align-items: center;">
            Type : TYPE_SCR17
        </div>
        <div class="connector h2_block h1_block down_start down_end">
            <label class="source-label">from</label>
            <label class=".middle-label">middle</label>
            <label class="destination-label">To</label>
            <img src="../assets/arrow.gif" class="connector-start">
            <img src="../assets/arrow.gif" class="connector-end">
        </div>
    </div>
</template>

<script setup>
</script>

<style scoped>
.canvas {
    font-family: tahoma;
}

.block {

    height: 100px;
    width: 100px;
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius: 50%;
    position: absolute;
    border: 1px solid #7DAB76;
    background-color: #BAFFB0;
    animation: floatAnimation 2s ease-in-out infinite;
}


@keyframes floatAnimation {

    0%,
    100% {
        transform: translateY(0);
        /* Start and end position */
    }

    50% {
        transform: translateY(-10px);
        /* Middle position, adjust the value to control the height of the float */
    }
}

.connector {
    background-color: #FF9900;
}

.source-label,
.middle-label,
.destination-label {
    padding: 5px;
}
</style>