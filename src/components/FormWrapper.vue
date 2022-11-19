<template>
  <form class="form white">
    <div>
      <div class="form-item">
        <img src="../assets/icon-bed.svg" class="icon" alt="icon-bed" />
        <v-autocomplete
          v-model="destination"
          :items="items"
          :search-input.sync="search"
          solo
          flat
          hide-details
          hide-no-data
          loader-height="0"
          label="Destination"
          item-text="name"
          item-value="code"
          @change="validateSearch"
        />
      </div>
      <p v-if="searchError" class="error-text">{{ searchError }}</p>
    </div>

    <v-dialog v-model="dialog" content-class="dialog">
      <template #activator="{ on }">
        <div>
          <FormDatePicker class="form-item" @click.native="toggle" v-on="on" />
          <p v-if="datesError" class="error-text">{{ datesError }}</p>
        </div>
      </template>
      <v-date-picker
        v-model="dates"
        :min="minDate"
        :max="maxDate"
        :first-day-of-week="1"
        show-adjacent-months
        scrollable
        no-title
        color="#D78D00"
        header-color="#D78D00"
        range
        @change="validateDates"
      />
      <div class="white center">
        <FormDates
          v-if="hasDates"
          :dateStart="formatDate(dates[0])"
          :dateEnd="formatDate(dates[1])"
          :diff="daysDiff"
        />
        <button
          @click.prevent="
            validateDates();
            toggle();
          "
          class="button-s"
        >
          Done
        </button>
      </div>
    </v-dialog>

    <div>
      <div class="form-item">
        <img
          src="../assets/icon-passengers.svg"
          class="icon"
          alt="icon-passengers"
        />
        <v-select
          v-model="selectAdults"
          :items="adults"
          solo
          flat
          hide-details
          suffix="Adults"
          label="Adults"
          @change="validateGuests"
        />
        <v-select
          v-model="selectChildren"
          :items="children"
          solo
          flat
          hide-details
          suffix="Children"
          label="Children"
          @change="validateGuests"
        />
        <v-select
          v-model="selectRooms"
          :items="rooms"
          solo
          flat
          hide-details
          suffix="Rooms"
          label="Rooms"
        />
      </div>
      <p v-if="guestsError" class="error-text">{{ guestsError }}</p>
    </div>

    <button v-ripple class="button" @click.prevent="onClick">Search</button>
  </form>
</template>

<script lang="ts">
import Vue from "vue";
import FormDatePicker from "./FormDatePicker.vue";
import FormDates from "@/components/FormDates.vue";

declare let destinations: Array<{ name: string; code: string }>;
const MAX_GUESTS = 10;
const MAX_ROOMS = 5;
const DAY_MS = 1_000 * 60 * 60 * 24;

export default Vue.extend({
  name: "FormWrapper",
  components: {
    FormDates,
    FormDatePicker,
  },
  data: (): {
    items: Array<{ name: string; code: string }>;
    adults: number[];
    children: number[];
    rooms: number[];
    destination: string;
    search: string;
    dialog: boolean;
    dates: string[];
    selectAdults: number;
    selectChildren: number;
    selectRooms: number;
    searchError: string;
    datesError: string;
    guestsError: string;
  } => ({
    items: [],
    adults: [],
    children: [],
    rooms: [],
    destination: "",
    search: "",
    dialog: false,
    dates: [],
    selectAdults: 2,
    selectChildren: 0,
    selectRooms: 1,
    searchError: "",
    datesError: "",
    guestsError: "",
  }),
  computed: {
    minDate(): string {
      return new Date(new Date().getTime() + DAY_MS * 2).toISOString();
    },
    maxDate(): string {
      const endDate = this.dates.length
        ? new Date(this.dates[0]).getTime() + DAY_MS * 30
        : new Date().getTime() + DAY_MS * 365;

      return new Date(endDate).toISOString();
    },
    hasDates(): boolean {
      return this.dates.length > 1;
    },
    daysDiff(): number {
      let diff = 0;

      if (this.hasDates) {
        const date0 = new Date(this.dates[0]).getTime();
        const date1 = new Date(this.dates[1]).getTime();
        const dateDiff = date1 - date0;
        diff = dateDiff / DAY_MS;
      }

      return diff;
    },
  },
  mounted(): void {
    const guestArr = [...Array(MAX_GUESTS).keys()];
    const roomArr = [...Array(MAX_ROOMS).keys()];

    this.adults = guestArr.slice(1, MAX_GUESTS);
    this.children = guestArr.slice(0, -1);
    this.rooms = roomArr.slice(1, MAX_ROOMS);
  },
  watch: {
    search(val) {
      if (val?.length > 2 && !this.items.length) {
        this.items = destinations;
        this.items.length = 5_000;
      }
    },
  },
  methods: {
    toggle(): void {
      this.dialog = !this.dialog;
    },
    formatDate(dateStr: string): {
      day: string;
      month: string;
      date: string;
      year: string;
    } {
      const dateFull = new Date(dateStr).toDateString();
      const [day, month, date, year] = dateFull.split(" ");

      return { day, month, date, year };
    },
    validateSearch(): void {
      this.searchError = this.destination ? "" : "Please select a destination.";
    },
    validateDates(): void {
      if (new Date(this.dates[0]) > new Date(this.dates[1])) {
        this.dates.sort();
      }

      this.datesError = this.hasDates ? "" : "Please select dates.";
    },
    validateGuests(): void {
      const total = this.selectAdults + this.selectChildren;

      if (total >= MAX_GUESTS) {
        this.guestsError = `Max number of guests is ${MAX_GUESTS - 1}.`;
      } else if (this.selectAdults < this.selectChildren) {
        this.guestsError =
          "Number of children per room must not exceed the number of adults per room.";
      } else {
        this.guestsError = "";
      }
    },
    onClick(): void {
      this.validateSearch();
      this.validateDates();
      this.validateGuests();

      if (this.searchError || this.datesError || this.guestsError) return;

      const obj = {
        destination: this.destination,
        dates: this.dates,
        adults: this.selectAdults,
        children: this.selectChildren,
        rooms: this.selectRooms,
      };

      console.log(obj);
    },
  },
});
</script>

<style lang="scss">
$desktop-breakpoint: 1024px;

%form-item {
  border-radius: 5px;
  height: 60px;
}

.form {
  padding: 15px;
  border-radius: 10px;
  opacity: 0.88;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(350px, 1fr));
  gap: 10px;
}

.form-item {
  @extend %form-item;

  padding: 10px 15px;
  border: 1px solid #e3e3e3;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.icon {
  width: 20px;
  margin-right: 10px;
  fill: #d78d00;
}

.error-text {
  color: red;
  margin: 5px 0 10px;
}

.white {
  background: white;
}

%button {
  background: #d78d00;
  color: white;
  font-weight: bold;
}

.button {
  @extend %form-item, %button;
}

.button-s {
  @extend %form-item, %button;

  height: 34px;
  width: 237px;
  margin-bottom: 20px;
}

@media screen and (min-width: $desktop-breakpoint) {
  .form {
    display: flex;
    max-width: $desktop-breakpoint;
  }

  .button {
    min-width: 150px;
  }
}
</style>
