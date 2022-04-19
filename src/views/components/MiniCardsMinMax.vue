<template>
  <div class="card mb-2" :class="directionReverse ? reverseDirection : ''">
    <div class="card-header p-3 pt-2">
      <div
        class="icon icon-lg icon-shape shadow-dark shadow text-center border-radius-xl mt-n4 position-absolute"
        :class="iconBackground"
      >
        <i
          class="material-icons opacity-10"
          :class="iconClass"
          aria-hidden="true"
          >{{ iconName }}</i
        >
      </div>
      <div
        class="pt-1"
        :class="this.$store.state.isRTL ? 'text-start' : 'text-end'"
      >
        <p class="text-sm mb-0 text-capitalize">{{ title }}</p>
        <h3 class="mb-0">{{ value }}</h3>
      </div>
    </div>
    <hr class="dark horizontal my-0" />
    <div
      class="card-footer p-3"
      :class="this.$store.state.isRTL ? 'text-start' : 'text-end'"
    >
      <div class="mb-0 row">
        <div class="col-6 text-center">
          <span class="text-sm">min. ({{ timeFrame }})</span>
          <br />
          <span class="font-weight-bolder">{{ min }}</span>
        </div>
        <div class="col-6 text-center">
          <span class="text-sm">max. ({{ timeFrame }})</span>
          <br />
          <span class="font-weight-bolder">{{ max }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "mini-cards-min-max",
  data() {
    return {
      reverseDirection: "flex-row-reverse justify-content-between",
    };
  },
  props: {
    directionReverse: Boolean,
    title: {
      type: String,
      required: true,
    },
    detail: {
      type: String,
      required: true,
    },
    value: {
      required: true,
    },
    min: {
      type: String,
      required: true,
    },
    max: {
      type: String,
      required: true,
    },
    timeFrame: {
      type: String,
      required: true,
    },
    iconName: {
      type: String,
      required: true,
    },
    iconClass: {
      type: String,
      required: true,
    },
    iconBackground: {
      type: String,
      default: "bg-white",
    },
  },
};
</script>
