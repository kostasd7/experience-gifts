<template>
  <form class="form" ref="form">
    <div class="form-item">
      <img src="../assets/icon-bed.svg" class="icon" alt="icon-bed" />
      <v-autocomplete
        :items="items"
        solo
        flat
        hide-details
        loader-height="0"
        label="Destination"
        item-text="name"
        item-value="code"
      />
    </div>

    <div class="form-item">
      <img
        src="../assets/icon-passengers.svg"
        class="icon"
        alt="icon-passengers"
      />
      <v-select
        :items="adults"
        :value="2"
        solo
        flat
        hide-details
        label="Adults"
      />
      <v-select
        :items="children"
        :value="0"
        solo
        flat
        hide-details
        label="Children"
      />
      <v-select
        :items="children"
        :value="1"
        solo
        flat
        hide-details
        label="Rooms"
      />
    </div>

    <button v-ripple class="button" @click.prevent="onClick">Search</button>
  </form>
</template>

<script lang="ts">
import Vue from "vue";

declare let destinations: Array<{ name: string; code: string }>;
const MAX_GUESTS = 10;
const MAX_ROOMS = 4;

export default Vue.extend({
  name: "FormWrapper",
  data: (): {
    items: Array<{ name: string; code: string }>;
    adults: number[];
    children: number[];
    rooms: number[];
  } => ({
    items: [],
    adults: [],
    children: [],
    rooms: [...Array(MAX_ROOMS).keys()],
  }),
  mounted(): void {
    const arr = [...Array(MAX_GUESTS).keys()];

    this.items = destinations;
    this.adults = arr.slice(1, MAX_GUESTS);
    this.children = arr.slice(0, -1);
  },
  methods: {
    onClick(): void {
      const formData = new FormData(this.$refs.form as HTMLFormElement);
      console.log(Object.fromEntries(formData.entries()));
    },
  },
});
</script>

<style lang="scss">
%radius {
  border-radius: 5px;
}

.form {
  padding: 15px;
  min-width: 320px;
  border-radius: 10px;
  background: white;
  opacity: 0.88;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  grid-template-rows: repeat(auto-fit, minmax(16px, max-content));
  gap: 10px;
}

.form-item {
  @extend %radius;

  padding: 10px 15px;
  border: 1px solid #e3e3e3;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.icon {
  width: 15px;
  fill: #d78d00;
}

.button {
  @extend %radius;

  background: #d78d00;
  color: white;
  font-weight: bold;
  height: 60px;
}
</style>
