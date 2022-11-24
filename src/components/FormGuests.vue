<template>
  <v-menu
    v-model="menu"
    :nudge-bottom="60"
    :close-on-click="false"
    :close-on-content-click="false"
  >
    <template v-slot:activator="{ on }">
      <div class="form-item flex-mid" v-on="on" v-ripple @click="onOpen">
        <img
          src="../assets/icon-passengers.svg"
          class="icon"
          alt="icon-passengers"
        />
        <div class="flex-start guests">
          {{ adults }} Adults - {{ children }} Children&nbsp;
          <span class="rooms">
            <span class="dash">- </span>{{ rooms }} {{ roomsText }}
          </span>
        </div>
        <v-icon>mdi-menu-down</v-icon>
      </div>
    </template>

    <div class="white form-item menu">
      <div class="grid selections">
        <!-- Header -->
        <div></div>
        <div class="grid people">
          <div>Adults</div>
          <div>Children</div>
        </div>
        <div></div>

        <!-- Rooms -->
        <template v-for="(guestData, index) in guestsData">
          <div :key="`room${index}`" class="room">Room {{ index + 1 }}</div>
          <div class="grid people" :key="`guests${index}`">
            <v-select
              outlined
              dense
              hide-details
              :items="adultsRange"
              :value="guestData.adults"
              @change="setAdults($event, index)"
            />
            <v-select
              outlined
              dense
              hide-details
              :items="filterSelection(childrenRange, guestData.adults)"
              :value="guestData.children.length"
              @change="setChildren($event, index)"
            />
            <template v-if="guestData.children.length">
              <div class="grid-span">Children ages</div>
              <v-select
                v-for="ageIndex in guestData.children.length"
                outlined
                dense
                hide-details
                :key="`age${ageIndex}`"
                :items="childrenAgesRange"
                :value="childrenAgesRange[0]"
                @change="setChildrenAge($event, index, ageIndex)"
              />
            </template>
          </div>
          <v-icon
            @click="onRemove(index)"
            :key="`icon${index}`"
            :class="{ disabled: !index }"
            class="button-remove"
          >
            {{ index ? "mdi-close-circle" : "" }}
          </v-icon>
        </template>
      </div>

      <!-- Error & Buttons -->
      <p v-if="error" class="error-text">{{ error }}</p>
      <div class="grid buttons">
        <button
          v-ripple
          @click.prevent="onAdd"
          class="button-border"
          :class="{ disabled: !canAdd }"
        >
          Add room
        </button>
        <button v-ripple @click.prevent="onCancel" class="button-border">
          Cancel
        </button>
        <button v-ripple @click.prevent="onDone" class="button-s">Done</button>
      </div>
    </div>
  </v-menu>
</template>

<script lang="ts">
import Vue, { PropType } from "vue";
import { IGuestRoom } from "@/Interface/IGuestRoom";
import { Emit } from "@/constants/Emit";

const MAX_GUESTS = 9;
const MAX_CHILDREN = 4;
const MAX_ROOMS = 4;
const MAX_CHILD_AGE = 18;

export default Vue.extend({
  name: "FormGuests",
  props: {
    guestRooms: {
      type: Array as PropType<IGuestRoom[]>,
      required: true,
    },
    defaultGuestRoom: {
      type: Object as PropType<IGuestRoom>,
      required: true,
    },
  },
  data: (): {
    menu: boolean;
    guestsData: IGuestRoom[];
    adultsRange: number[];
    childrenRange: number[];
    childrenAgesRange: number[];
    error: string;
  } => ({
    menu: false,
    guestsData: [],
    adultsRange: [],
    childrenRange: [],
    childrenAgesRange: [],
    error: "",
  }),
  mounted(): void {
    /**
     * Fill <v-select> arrays
     */
    this.adultsRange = [...Array(MAX_GUESTS + 1).keys()];
    this.childrenRange = [...Array(MAX_CHILDREN + 1).keys()];
    this.childrenAgesRange = [...Array(MAX_CHILD_AGE + 1).keys()];

    // Remove `0` from arrays that need at least 1
    this.adultsRange.shift();
    this.childrenAgesRange.shift();
  },
  computed: {
    /**
     * Count total adults selected
     */
    adults(): number {
      return this.guestRooms.reduce(
        (accumulator, room) => accumulator + room.adults,
        0
      );
    },
    /**
     * Count total children selected
     */
    children(): number {
      return this.guestRooms.reduce(
        (accumulator, room) => accumulator + room.children.length,
        0
      );
    },
    /**
     * Count total rooms selected
     */
    rooms(): number {
      return this.guestRooms.length;
    },
    /**
     * Count total guests user selected in menu
     */
    guests(): number {
      return this.guestsData.reduce(
        (accumulator, room) => accumulator + room.adults + room.children.length,
        0
      );
    },
    roomsText(): string {
      return this.rooms > 1 ? "Rooms" : "Room";
    },
    canAdd(): boolean {
      return this.guestsData.length < MAX_ROOMS;
    },
  },
  methods: {
    /**
     * Set adults selected in room
     */
    setAdults(adults: number, room: number): void {
      this.guestsData[room].adults = adults;
    },
    /**
     * Set children selected in room
     */
    setChildren(children: number, room: number): void {
      const childrenArr = Array(children);
      childrenArr.fill(1);
      this.guestsData[room].children = childrenArr;
    },
    /**
     * Set child age selected
     */
    setChildrenAge(age: number, room: number, child: number): void {
      this.guestsData[room].children[child] = age;
    },
    /**
     * Filter number of children to be no more than adults according to requirements
     */
    filterSelection(childrenRange: number[], adults: number): number[] {
      return JSON.parse(JSON.stringify(childrenRange.slice(0, adults + 1)));
    },
    validate(): void {
      this.error =
        this.guests > MAX_GUESTS
          ? `Maximum number of guests is ${MAX_GUESTS}`
          : "";
    },
    toggle(): void {
      this.menu = !this.menu;
    },
    /**
     * Reset to parent component's saved data
     */
    reset(): void {
      this.guestsData = JSON.parse(JSON.stringify(this.guestRooms));
    },
    /**
     * Fill initial data
     */
    onOpen(): void {
      if (!this.guestsData.length) {
        this.reset();
      }
    },
    onAdd(): void {
      if (this.canAdd) {
        this.guestsData.push(JSON.parse(JSON.stringify(this.defaultGuestRoom)));
      }
    },
    /**
     * Trigger only for rooms other than 1st
     */
    onRemove(index: number): void {
      if (index) {
        this.guestsData.splice(index, 1);
      }
    },
    /**
     * Reset data and close menu
     */
    onCancel(): void {
      this.toggle();
      this.reset();
    },
    /**
     * Validate and emit data to parent if valid
     */
    onDone(): void {
      this.validate();

      if (!this.error) {
        this.$emit(Emit.CHANGE, JSON.parse(JSON.stringify(this.guestsData)));
        this.toggle();
      }
    },
  },
});
</script>

<style lang="scss" scoped>
$color-orange: #d78d00;

::v-deep .v-input__slot {
  min-height: auto !important;
  height: 35px;
  fieldset {
    color: #e3e3e3 !important;
  }
}

.form-item {
  &.menu {
    padding: 18px;
    height: auto;
  }
}

.room {
  line-height: 2em;
}

.guests {
  flex: 1 2 auto;
  padding: 10px;
  flex-wrap: wrap;
}

.buttons {
  grid-template-columns: repeat(auto-fill, minmax(80px, 1fr));
  font-size: 14px;
}

.selections {
  grid-template-columns: 1.5fr 4fr 0.5fr;
  margin-bottom: 20px;
}

.people {
  grid-template-columns: repeat(2, minmax(70px, 100px));
}

.grid-span {
  grid-column: span 2;
}

.button-border {
  border: 1px solid $color-orange;
  border-radius: 5px;
}

.button-remove {
  align-items: flex-start !important;
  margin-top: 7px;
}

.disabled {
  pointer-events: none;
  opacity: 0.7;
}

@media screen and (min-width: 768px) {
  .rooms {
    flex: 1 2 100%;
    font-size: 14px;
  }

  .dash {
    display: none;
  }
}
</style>
