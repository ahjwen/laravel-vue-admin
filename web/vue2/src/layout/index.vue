<template>
  <div class="page-layout" :class="{ collapse: menuCollapse }">
    <div class="page-layout__mask"></div>
    <div class="page-layout__left">
      <slider />
    </div>
    <div class="page-layout__right">
      <div class="page-layout__topbar">
        <topbar />
      </div>
      <div class="page-layout__container">
        <div class="page-layout__view">
          <keep-alive>
            <router-view v-if="isKeepAlive"></router-view>
          </keep-alive>
          <router-view v-if="!isKeepAlive"></router-view>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import Slider from '@/layout/slider'
import Topbar from '@/layout/topbar'
import { mapGetters } from 'vuex';
export default {
  components:{
    Topbar,
    Slider
  },
  computed: {
    ...mapGetters(["menuCollapse", "appInfo"]),
    isKeepAlive() {
      return this.$route.meta.keepAlive;
    },
  },
};
</script>
<style lang="scss" scoped>
.page-layout {
  display: flex;
  background-color: #f7f7f7;
  width: 100%;
  height: 100%;
  overflow: hidden;

  &__left {
    overflow: hidden;
    height: 100%;
    width: 250px;
    transition: left 0.2s;
  }

  &__right {
    display: flex;
    flex-direction: column;
    height: 100%;
    width: calc(100% - 255px);
  }

  &__topbar {
    margin-bottom: 10px;
  }

  &__container {
    width: 100%;
    box-sizing: border-box;
    flex: 1;
    overflow: hidden;
    margin-bottom: 10px;
  }

  &__mask {
    position: fixed;
    left: 0;
    top: 0;
    background-color: rgba(0, 0, 0, 0.5);
    height: 100%;
    width: 100%;
    z-index: 999;
  }

  &__view {
    height: 100%;
    width: 100%;
    box-sizing: border-box;
    padding: 0 10px;
    border-radius: 3px;

    & > div {
      height: 100%;
    }
  }

  @media only screen and (max-width: 768px) {
    .page-layout__left {
      position: absolute;
      left: 0;
      z-index: 9999;
      transition: transform 0.3s cubic-bezier(0.7, 0.3, 0.1, 1),
        box-shadow 0.3s cubic-bezier(0.7, 0.3, 0.1, 1);
    }

    .page-layout__right {
      width: 100%;
    }

    &.collapse {
      .page-layout__left {
        transform: translateX(-100%);
      }

      .page-layout__mask {
        display: none;
      }
    }
  }

  @media only screen and (min-width: 768px) {
    .page-layout__left,
    .page-layout__right {
      transition: width 0.2s;
    }

    .page-layout__mask {
      display: none;
    }

    &.collapse {
      .page-layout__left {
        width: 64px;
      }

      .page-layout__right {
        width: calc(100% - 64px);
      }
    }
  }
}
</style>