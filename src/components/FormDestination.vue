<template>
  <div>
    <div class="form-item flex-mid" v-ripple>
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
        @change="onChange"
      >
        <template #prepend>
          <img src="../assets/icon-bed.svg" class="icon" alt="icon-bed" />
        </template>
        <template #item="{ item }">
          <v-icon left>{{ getIcon(item.type) }}</v-icon>
          <span class="pad-left">{{ item.name }}</span>
        </template>
      </v-autocomplete>
    </div>
    <p v-if="error" class="error-text">{{ error }}</p>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import { DestinationType } from "@/constants/DestinationType";
import { Emit } from "@/constants/Emit";

declare let destinations: Array<{ name: string; code: string; type: string }>;

export default Vue.extend({
  name: "FormDestination",
  props: {
    error: {
      type: String,
      required: true,
    },
  },
  data: (): {
    items: Array<{ name: string; code: string }>;
    destination: string;
    search: string;
  } => ({
    items: [],
    destination: "",
    search: "",
  }),
  watch: {
    /**
     * Add items to component when it's empty and after user has typed at least 2 letters
     */
    search(val) {
      if (val?.length > 2 && !this.items.length) {
        this.items = destinations;
      }
    },
  },
  methods: {
    /**
     * Get material design icon depending on type
     */
    getIcon(type: DestinationType): string {
      let icon;
      switch (type) {
        case DestinationType.COUNTRY:
          icon = "flag";
          break;
        case DestinationType.CITY:
          icon = "city";
          break;
        case DestinationType.DESTINATION:
          icon = "map-marker";
          break;
        default:
          icon = "";
      }

      return `mdi-${icon}`;
    },
    onChange(): void {
      this.$emit(Emit.CHANGE, this.destination);
    },
  },
});
</script>

<style lang="scss" scoped>
.pad-left {
  padding-left: 10px;
}

.icon {
  margin-top: 5px;
}
</style>
