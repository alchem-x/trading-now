<template>
    <div class="stock-chart-container">
        <LoadingSegment v-if="!stockStore.symbol" />
        <div class="lw-chart" ref="chartContainer"></div>
    </div>
</template>

<script setup>
import { shallowRef, ref, watch, onMounted, onBeforeUnmount } from 'vue'
import { createChart } from 'lightweight-charts'
import dayjs from 'dayjs'
import { debounce } from 'lodash-es'
import { useStockStore } from '#web/store/stock.js'
import { simplifyNumber } from '#web/common/formating.js'
import LoadingSegment from '#web/common/LoadingSegment.vue'

const chartContainer = ref()
const chart = shallowRef()
const stockStore = useStockStore()

function createLWChart() {
    if (stockStore.chartMinuteData) {
        const { items, last_close: lastClose } = stockStore.chartMinuteData
        chart.value = createChart(chartContainer.value, {
            crosshair: {
                vertLine: {
                    labelVisible: false,
                },
            },
            timeScale: {
                borderVisible: false,
                tickMarkFormatter: (time) => {
                    return dayjs(time).format('HH:mm')
                }
            },
            handleScroll: false,
            handleScale: false,
            localization: {
                priceFormatter: (price) => {
                    return price >= 10000
                        ? simplifyNumber(price)
                        : parseFloat(price.toFixed(3))
                },
            },
        })
        //
        {
            const lineSeries = chart.value.addLineSeries()
            lineSeries.priceScale().applyOptions({
                borderVisible: false,
            })
            const priceData = items.map((it) => {
                return {
                    time: it.timestamp,
                    value: it.current,
                }
            })
            lineSeries.setData(priceData)
        }
        //
        {
            const volumeSeries = chart.value.addHistogramSeries({
                color: '#26a69a',
                priceFormat: {
                    type: 'volume',
                },
                priceScaleId: '',
                scaleMargins: {
                    top: 0.7,
                    bottom: 0,
                },
            })
            volumeSeries.priceScale().applyOptions({
                borderVisible: false,
                scaleMargins: {
                    top: 0.7,
                    bottom: 0,
                },
            })
            const volumeData = items.map((it, index) => {
                return {
                    time: it.timestamp,
                    value: it.volume,
                    color: it.current >= (items[index - 1]?.current ?? lastClose) ? '#ee2500' : '#093',
                }
            })
            volumeSeries.setData(volumeData)
        }
        //
        chart.value.timeScale().fitContent()
    }
}

function destroyLWChart() {
    if (chart.value) {
        chart.value.remove()
        chart.value = null
    }
}

function resetLWChart() {
    destroyLWChart()
    createLWChart()
}

const resetLWChartDebounced = debounce(resetLWChart, 100)

onMounted(() => {
    if (stockStore.chartMinuteData) {
        resetLWChart()
    }
    stockStore.startCartDataJob()
    window.addEventListener('resize', resetLWChartDebounced)
})

onBeforeUnmount(() => {
    stockStore.stopCharDataJob()
    window.removeEventListener('resize', resetLWChartDebounced)
    destroyLWChart()
})

watch(() => stockStore.chartMinuteData, (data) => {
    if (data && chartContainer.value) {
        resetLWChart()
    } else {
        destroyLWChart()
    }
})
</script>

<style scoped lang="less">
.stock-chart-container {
    position: relative;
    box-sizing: border-box;
    border: 1px solid #efeff5;
    border-radius: 3px;
    padding: 12px;
    overflow: hidden;

    .lw-chart {
        box-sizing: border-box;
        overflow: hidden;
        height: 200px;
        direction: ltr;
        user-select: none;
        -webkit-tap-highlight-color: transparent;

        :deep(#tv-attr-logo) {
            display: none;
        }
    }
}
</style>