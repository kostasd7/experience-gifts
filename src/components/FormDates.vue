<template>
  <p>
    <strong>{{ start }} - {{ end }} ({{ diff }} nights)</strong>
  </p>
</template>

<script lang="ts">
import Vue from "vue";

export default Vue.extend({
  name: "FormDates",
  props: {
    dateStart: {
      type: Object,
      required: true,
    },
    dateEnd: {
      type: Object,
      required: true,
    },
    diff: {
      type: Number,
      required: true,
    },
  },
  computed: {
    start(): string {
      return `${this.dateStart.day}, ${this.dateStart.month} ${this.dateStart.date}`;
    },
    end(): string {
      return `${this.dateEnd.day}, ${this.dateEnd.month} ${this.dateEnd.date}`;
    },
  },
});
</script>
