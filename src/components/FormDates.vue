<template>
  <div class="form-item flex-mid" v-ripple>
    <template v-for="(dateObj, index) in dates">
      <div v-if="index" :key="`arrow${index}`">
        <v-icon color="#d78d00" class="icon-arrow m-show">
          mdi-arrow-right
        </v-icon>
        <div class="line m-hide"></div>
      </div>
      <img
        v-else
        :key="`icon${index}`"
        src="../assets/icon-calendar.svg"
        class="icon m-hide"
        alt="icon-calendar"
      />

      <div :key="index">
        <div class="flex-start m-show">
          <img
            src="../assets/icon-calendar.svg"
            class="icon"
            alt="icon-calendar"
          />
          <strong class="mobile-head">{{
            index ? "CHECK OUT" : "CHECK IN"
          }}</strong>
        </div>

        <div class="flex-mid">
          <span class="font-l m-show">{{ dateObj.date }}</span>
          <div class="pad">
            <div class="date">
              <span class="m-hide">{{ dateObj.date }}</span>
              {{ dateObj.month }} {{ dateObj.year }}
            </div>
            <span class="font-s">{{ dateObj.day }}</span>
          </div>
        </div>
      </div>
    </template>
  </div>
</template>

<script lang="ts">
import Vue, { PropType } from "vue";
import { IDate } from "@/Interface/IDate";

export default Vue.extend({
  name: "FormDates",
  props: {
    dates: {
      type: Array as PropType<IDate[]>,
      required: true,
    },
  },
});
</script>

<style lang="scss" scoped>
$color-gray: #e3e3e3;

.mobile-head {
  font-size: 12px;
}

.icon-arrow {
  margin-right: 10px;
}

.line {
  height: 30px;
  border-left: 1px solid $color-gray;
}

.pad {
  padding: 0 5px;
}

.date {
  line-height: 1em;
}

.font-l {
  font-size: 37px;
}

.font-s {
  font-size: 14px;
}

@media screen and (min-width: 768px) {
  .m-show {
    display: none;
  }
}

@media screen and (max-width: 768px) {
  .m-hide {
    display: none;
  }

  .form-item {
    height: 110px;
  }
}
</style>
