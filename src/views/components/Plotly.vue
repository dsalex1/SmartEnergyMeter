<template>
  <div :id="plotlyId"></div>
</template>

<script>
import { defineComponent, nextTick, createApp } from "vue";
import * as Plotly from "plotly.js";

export default defineComponent({
  data() {
    return { plotlyId: "__plotly__" };
  },
  props: ["data", "layout", "config"],
  mounted() {
    this.plotlyId = `plotly-1`;
    nextTick(() =>
      Plotly.newPlot(this.plotlyId, [this.data], this.layout, this.config)
    );
  },
  watch: {
    data: {
      handler() {
        console.log("data changed", this.data);
        Plotly.update(this.plotlyId, { x: [this.data.x], y: [this.data.y] });
      },
      deep: true,
    },
  },
});
</script>
