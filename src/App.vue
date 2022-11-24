<template>
  <v-app class="wrapper">
    <v-main class="main">
      <h1 class="header center">Book your hotel stay</h1>
      <p class="center light">
        Redeem your Hotelgift voucher for your next hotel stay.
      </p>
      <form-wrapper />
    </v-main>
  </v-app>
</template>

<script lang="ts">
import Vue from "vue";
import FormWrapper from "@/components/FormWrapper.vue";

export default Vue.extend({
  name: "App",
  components: {
    FormWrapper,
  },
});
</script>

<style lang="scss">
$color-header: #1c2651;
$color: #1a244f;

// Overwrite vuetify CSS
.v-application {
  &.wrapper {
    padding: 25px;
    background: url("./assets/bg-image.jpg") fixed center left 10% / cover
      no-repeat;
    font-size: 16px;
    font-family: "Ubuntu", sans-serif;
    min-width: 375px;
  }
}

input,
.v-label,
.v-application.wrapper,
.v-select__selection,
.v-text-field__suffix,
.v-btn:not(.v-btn--disabled):not(.v-btn--active) .v-btn__content,
.v-date-picker-header__value button,
.v-icon:not(.icon-arrow) {
  color: $color !important;
}

.v-main__wrap {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}

.v-input__slot {
  padding: 0 !important;
}

.v-select__selection {
  order: 1;
}

.v-autocomplete__content {
  margin-left: -55px;
}

.v-dialog {
  max-width: 500px;
}

.flex-start {
  display: flex;
  align-items: center;
}

.flex-mid {
  @extend .flex-start;

  justify-content: space-between;
}

.header {
  color: $color-header;
  font-size: 30px;
  letter-spacing: 0.6px;
}

.center {
  text-align: center;
}

.light {
  font-weight: lighter;
}

@media screen and (min-width: 768px) {
  .header {
    font-size: 40px;
    letter-spacing: 0.8px;
  }
}

@media screen and (min-width: 1280px) {
  .header {
    font-size: 50px;
    letter-spacing: 1px;
  }

  .light {
    font-size: 20px;
  }
}
</style>
