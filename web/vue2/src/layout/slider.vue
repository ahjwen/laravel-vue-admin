<template>
  <div class="app-slider">
    <div class="app-slider__logo">
      <img
        src="@/assets/logo.png"
      />
      <span v-if="!menuCollapse || browser.isMini">{{ appInfo.name }}</span>
    </div>
    <div class="app-slider__menu">
      <menu-slider />
    </div>
  </div>
</template>
<script>
import { mapGetters } from "vuex";
import MenuSlider from "@/layout/menu/index";

export default {
  computed: {
    ...mapGetters(["menuCollapse", "browser", "appInfo"]),
  },
  components: {
    MenuSlider,
  },
};
</script>
<style lang="scss" scoped>
.app-slider {
  height: 100%;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
  background-color: #2f3447;

  &__logo {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 80px;
    cursor: pointer;

    img {
      height: 30px;
      width: 30px;
    }

    span {
      color: #fff;
      font-weight: bold;
      font-size: 26px;
      margin-left: 10px;
      font-family: inherit;
      white-space: nowrap;
    }
  }

  &__menu {
    height: calc(100% - 80px);
  }
}
</style>