<template>
    <div class="index-container">
        <img :src="'/images/stock.webp'">
        <div class="nav">
            <NFlex align="center" justify="center">
                <NPageHeader title="开始摇滚 📈" />
                <RouterLink :to="'/stock'">
                    <NButton>
                        股票
                    </NButton>
                </RouterLink>
                <RouterLink :to="'/watch'">
                    <NButton>
                        盯盘
                    </NButton>
                </RouterLink>
                <NButton tag="a" text href="https://github.com/alchem-x/chives-watch" target="_blank">
                    <template #icon>
                        <GitHubIcon />
                    </template>
                    <template #default>
                        GitHub
                    </template>
                </NButton>
            </NFlex>
        </div>
    </div>
</template>

<script setup>
import { NButton, NFlex, NPageHeader } from 'naive-ui'
import GitHubIcon from '#web/common/GitHubIcon.vue'
</script>


<style scoped lang="less">
.index-container {
    box-sizing: border-box;
    min-height: 100vh;
    padding: 1rem;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 1rem;

    img {
        user-select: none;
        max-width: 400px;
        width: 100%;
    }

    .nav {
        text-align: center;
    }
}
</style>
