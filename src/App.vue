<template>
  <div id="app" :class="classes">
    <router-view></router-view>
    <NavigationButton class="nav-button" />
    <ModalManager/>
  </div>
</template>

<script>
import NavigationButton from '~/components/partials/NavigationButton.vue';
import ModalManager from '~/components/ModalManager.vue';
import { isMobile } from '~/utils/if-env';

export default {
  name: "App",
  data: function() {
    return {
      classes: {
        mobile: isMobile,
        desktop: !isMobile
      }
    };
  },
  components: {
    ModalManager,
    NavigationButton
  },
}
</script>

<style lang="scss" scoped>
.app {
  position: relative;
  top: 0;
  left: 0;
  height: 100%;
}

.nav-button {
  position: absolute;
  top: 0;
  left: 0;
}
</style>