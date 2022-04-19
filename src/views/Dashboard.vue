<template>
  <div class="py-4 container-fluid">
    <h1 class="pb-5">Energy Dashboard</h1>
    <div class="row mb-4">
      <div class="col-lg-12 position-relative z-index-2">
        <div class="row">
          <div class="col-lg-3 col-md-6 col-sm-6 mb-3">
            <mini-cards-min-max
              title="Current Usage"
              :value="`${usage.current} W`"
              :min="`${usage.min} W`"
              :max="`${usage.max} W`"
              timeFrame="24h"
              iconName="bolt"
              iconClass="text-white"
              iconBackground="bg-gradient-dark"
            />
          </div>
          <div class="col-lg-3 col-md-6 col-sm-6 mb-3">
            <mini-cards-min-max
              title="Daily Usage"
              :value="`${daily.current} Kwh`"
              :min="`${daily.min} Kwh`"
              :max="`${daily.max} Kwh`"
              timeFrame="month"
              iconName="calendar_today"
              iconClass="text-white"
              iconBackground="bg-gradient-success"
            />
          </div>
          <div class="col-lg-3 col-md-6 col-sm-6 mb-3">
            <mini-cards-min-max
              title="Monthly Usage"
              :value="`${monthly.current} Kwh`"
              :min="`${monthly.min} Kwh`"
              :max="`${monthly.max} Kwh`"
              timeFrame="1a"
              iconName="date_range"
              iconClass="text-white"
              iconBackground="bg-gradient-primary"
            />
          </div>
          <div class="col-lg-3 col-md-6 col-sm-6 mb-3">
            <mini-cards-min-max
              title="Yearly Usage"
              :value="`${yearly.current} Kwh`"
              :min="`${yearly.min} Kwh`"
              :max="`${yearly.max} Kwh`"
              timeFrame="all"
              iconName="calendar_month"
              iconClass="text-white"
              iconBackground="bg-gradient-info"
            />
          </div>
          <div class="col-lg-3 col-md-6 col-sm-6 mb-3">
            <mini-cards
              :title="`Current Meter`"
              :value="`${currentMeter} KWh`"
              iconName="speed"
              iconClass="text-white"
              iconBackground="bg-gradient-dark"
            />
          </div>
          <div class="col-lg-3 col-md-6 col-sm-6 mb-3">
            <mini-cards
              :title="`Monthly Costs (${KWhPrice}€/KWh)`"
              :value="`${monthCost} €`"
              iconName="payments"
              iconClass="text-white"
              iconBackground="bg-gradient-warning"
            />
          </div>
          <div class="col-lg-3 col-md-6 col-sm-6 mb-3">
            <mini-cards
              :title="`Yearly Costs (${KWhPrice}€/KWh)`"
              :value="`${yearCost} €`"
              iconName="monetization_on"
              iconClass="text-white"
              iconBackground="bg-gradient-warning"
            />
          </div>
        </div>
        <div class="row mt-1 pe-4 mb-4">
          <div>
            <Plotly :data="plotDifferenceData" :layout="plotLayout"></Plotly>
          </div>
        </div>
        <!-- this shall be overview stuff, and placed in the middle
        <div class="row mt-1">
          <div class="col-lg-4 col-md-6 mt-4">
            <chart-bars />
          </div>
          <div class="col-lg-4 col-md-6 mt-4">
            <chart-line />
          </div>
          <div class="col-lg-4 mt-4">
            <chart-line-tasks />
          </div>
        </div>-->
      </div>
    </div>
  </div>
</template>

<script setup>
import { reactive, ref, onMounted, computed } from "vue";

import ChartBars from "./components/ChartBars.vue";
import ChartLine from "./components/ChartLine.vue";
import ChartLineTasks from "./components/ChartLineTasks.vue";
import MiniCardsMinMax from "./components/MiniCardsMinMax.vue";
import MiniCards from "./components/MiniCards.vue";
import Plotly from "./components/Plotly.vue";

let KWhPrice = 0.3216;

let plotLayout = reactive({
  title: "Energy Usage",
  yaxis: {
    //type: "log",
    autorange: true,
  },
});

onMounted(async () => {
  update();
  setInterval(() => {
    update();
  }, 10000);
});

let meterData = ref([]);
async function update() {
  let rawData = await (
    await fetch("http://raspberrypi:8000/dataLog.txt")
  ).text();
  meterData.value = rawData
    .trim()
    .split("\n")
    .map((s) => s.split(";"));
}

let plotDifferenceData = computed(() => ({
  x: meterData.value
    .map(
      (d) =>
        new Date(parseInt(d[0]) * 1000 + 2 * 60 * 60 * 1000) //adjust for timezone difference, the data saved is in utc
          .toISOString()
          .replace("T", " ")
          .split(".")[0]
    )
    .slice(0, -1),
  y: meterData.value
    .map(
      (d, i, arr) =>
        ((parseFloat((arr[i + 1] || [])[1] - parseFloat(d[1])) * 1000) /
          ((arr[i + 1] || [])[0] - d[0])) *
        1000
    )
    .slice(0, -1),
  type: "scatter",
}));

let currentMeter = computed(() =>
  parseFloat((meterData.value[meterData.value.length - 1] || [])[1])
);

let usage = computed(() => ({
  current: parseInt(
    plotDifferenceData.value.y[plotDifferenceData.value.y.length - 1]
  ),
  min: "/",
  max: "/",
}));

let daily = reactive({
  current: "/",
  min: "/",
  max: "/",
});
let monthly = reactive({
  current: "/",
  min: "/",
  max: "/",
});
let yearly = reactive({
  current: "/",
  min: "/",
  max: "/",
});
let monthCost = ref("/");
let yearCost = ref("/");
</script>
