<template>
  <v-dialog v-model="dialog" content-class="dialog" :persistent="!hasDates">
    <template #activator="{ on }">
      <form-dates :dates="datesFormatted" @click.native="toggle" v-on="on" />
    </template>

    <v-date-picker
      v-model="dates"
      :min="minDate"
      :max="maxDate"
      :value="defaultDates"
      :first-day-of-week="1"
      show-adjacent-months
      scrollable
      no-title
      color="#D78D00"
      range
      @change="onChange"
    />
    <div class="white center">
      <p class="dates">
        <strong v-if="hasDates">
          {{ datesDisplay }} ({{ daysDiff }} nights)
        </strong>
        <strong v-else class="error-text">Please select dates</strong>
      </p>
      <button v-ripple @click.prevent="toggle" class="button-s">Done</button>
    </div>
  </v-dialog>
</template>

<script lang="ts">
import Vue from "vue";
import FormDates from "@/components/FormDates.vue";
import { Emit } from "@/constants/Emit";
import { IDate } from "@/Interface/IDate";

const DAY_START = 2;
const DAY_END = 6;
const DAYS_MAX_RANGE = 30;
const DAYS_MAX = 365;
const DAY_MS = 1_000 * 60 * 60 * 24;

export default Vue.extend({
  name: "FormDatePicker",
  components: {
    FormDates,
  },
  data: (): {
    dialog: boolean;
    dates: string[];
  } => ({
    dates: [],
    dialog: false,
  }),
  mounted(): void {
    this.dates = this.defaultDates;
    this.onChange();
  },
  computed: {
    hasDates(): boolean {
      return this.dates.length > 1;
    },
    /**
     * Min date is +2 days
     */
    minDate(): string {
      return new Date(new Date().getTime() + DAY_MS * DAY_START).toISOString();
    },
    /**
     * Max date is 1 year if no date is selected, else it's +30 days
     */
    maxDate(): string {
      const endDate = this.dates.length
        ? new Date(this.dates[0]).getTime() + DAY_MS * DAYS_MAX_RANGE
        : new Date().getTime() + DAY_MS * DAYS_MAX;

      return new Date(endDate).toISOString();
    },
    /**
     * Add default dates for user so that it's not empty
     */
    defaultDates(): string[] {
      const defaultEnd = new Date(
        new Date().getTime() + DAY_MS * DAY_END
      ).toISOString();

      return [this.minDate, defaultEnd];
    },
    /**
     * Format and display dates according to design
     */
    datesDisplay(): string {
      const dateStart = this.formatDate(this.dates[0]);
      const dateEnd = this.formatDate(this.dates[1]);

      return `${dateStart.day}, ${dateStart.month} ${dateStart.date} - ${dateEnd.day}, ${dateEnd.month} ${dateEnd.date}`;
    },
    datesFormatted(): IDate[] {
      return this.dates.map(this.formatDate);
    },
    /**
     * Returns a positive integer with dates difference
     */
    daysDiff(): number {
      let diff = 0;

      if (this.hasDates) {
        const date0 = new Date(this.dates[0]).getTime();
        const date1 = new Date(this.dates[1]).getTime();
        const dateDiff = date1 - date0;
        diff = dateDiff / DAY_MS;
      }

      return Math.round(diff);
    },
  },
  methods: {
    toggle(): void {
      if (this.hasDates) {
        this.dialog = !this.dialog;
      }
    },
    /**
     * Create an object with values needed for display
     */
    formatDate(dateStr: string): IDate {
      const dateFull = new Date(dateStr).toLocaleDateString("en-us", {
        weekday: "long",
        year: "numeric",
        month: "long",
        day: "numeric",
      });
      const [day, month, date, year] = dateFull.replaceAll(",", "").split(" ");

      return { day, month, date, year };
    },
    /**
     * Sort dates before submitting. Remove last date if user selected the same.
     */
    onChange(): void {
      const dateStart = new Date(this.dates[0]);
      const dateEnd = new Date(this.dates[1]);

      if (dateStart > dateEnd) {
        this.dates.sort();
      } else if (dateStart.getTime() === dateEnd.getTime()) {
        this.dates.pop();
      }

      this.$emit(Emit.CHANGE, this.dates);
    },
  },
});
</script>

<style lang="scss" scoped>
.dates {
  font-size: 13px;
  margin-bottom: 10px;
}

.form-item {
  padding-right: 5px;
}

.button-s {
  width: 237px;
  margin-bottom: 20px;
}
</style>
