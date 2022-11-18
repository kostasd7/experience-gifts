<template>
  <v-app class="wrapper">
    <v-main class="main">
      <h1 class="header center">Book your hotel stay</h1>
      <p class="center">
        Redeem your Hotelgift voucher for your next hotel stay.
      </p>
      <FormWrapper />
    </v-main>
  </v-app>
</template>

<script lang="ts">
import Vue from "vue";
import FormWrapper from "./components/FormWrapper.vue";

export default Vue.extend({
  name: "App",
  components: {
    FormWrapper,
  },
});
</script>

<style lang="scss">
.v-application {
  &.wrapper {
    padding: 25px;
    background: url("./assets/bg-image.jpg") center left/cover no-repeat;
    color: #1a244f;
    font-size: 16px;
  }
}

.v-main__wrap {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}

.header {
  color: #1c2651;
  font-size: 30px;
}

.center {
  text-align: center;
}
</style>
