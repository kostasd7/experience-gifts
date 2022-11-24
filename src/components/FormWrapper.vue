<template>
  <form class="form white">
    <form-destination
      @[emitEvent]="changeDestination"
      :error="destinationError"
    />

    <form-date-picker @[emitEvent]="changeDates" />

    <form-guests
      @[emitEvent]="changeGuests"
      :guest-rooms="guests"
      :default-guest-room="defaultGuests"
    />

    <button v-ripple class="button" @click.prevent="onClick">Search</button>
  </form>
</template>

<script lang="ts">
import Vue from "vue";
import FormDatePicker from "@/components/FormDatePicker.vue";
import FormDestination from "@/components/FormDestination.vue";
import FormGuests from "@/components/FormGuests.vue";
import { Emit } from "@/constants/Emit";
import { IGuestRoom } from "@/Interface/IGuestRoom";

export default Vue.extend({
  name: "FormWrapper",
  components: {
    FormDestination,
    FormDatePicker,
    FormGuests,
  },
  data: (): {
    destination: string;
    dates: string[];
    guests: IGuestRoom[];
    destinationError: string;
    emitEvent: string;
  } => ({
    destination: "",
    dates: [],
    guests: [],
    destinationError: "",
    emitEvent: Emit.CHANGE,
  }),
  mounted(): void {
    this.guests.push(this.defaultGuests);
  },
  computed: {
    defaultGuests(): IGuestRoom {
      return { adults: 2, children: [] };
    },
  },
  methods: {
    changeDestination(destination: string): void {
      this.destination = destination;
      this.validateDestination();
    },
    changeDates(dates: string[]): void {
      this.dates = dates;
    },
    changeGuests(guests: IGuestRoom[]): void {
      this.guests = guests;
    },
    validateDestination(): void {
      this.destinationError = this.destination
        ? ""
        : "Please select a destination.";
    },
    onClick(): void {
      this.validateDestination();

      if (this.destinationError) return;

      const obj = {
        destination: this.destination,
        dates: this.dates,
        guests: this.guests,
      };

      console.log(obj);
    },
  },
});
</script>

<style lang="scss">
$color-orange: #d78d00;
$color-gray: #e3e3e3;

%form-item {
  border-radius: 5px;
  height: 60px;
}

.grid {
  display: grid;
  gap: 10px;
}

.form {
  @extend .grid;
  max-width: 400px;
  padding: 15px;
  border-radius: 10px;
  opacity: 0.88;
  grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
}

.form-item {
  @extend %form-item;

  padding: 10px 16px;
  border: 1px solid #e3e3e3;
}

.icon {
  width: 20px;
  margin-right: 10px;
  fill: $color-orange;
}

.error-text {
  color: red;
  margin: 5px 12px;
}

.white {
  background: white;
}

%button {
  background: $color-orange;
  color: white;
  font-weight: bold;
}

.button {
  @extend %form-item, %button;
}

.button-s {
  @extend %form-item, %button;

  height: 34px;
}

@media screen and (min-width: 768px) {
  .form {
    max-width: 750px;
  }
}

@media screen and (min-width: 1280px) {
  .form {
    display: flex;
    max-width: 1200px;
  }

  .button {
    width: 150px;
  }

  .icon {
    margin-right: 5px;
  }
}
</style>
