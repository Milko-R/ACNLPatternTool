<template>
  <button
    :class="{
      'menu-button--container': true,
      'menu-button--container--open': open
    }"
    @click="open=true">
    <div class="menu-button--icon-wrapper">
      <IconCompass class="menu-button--icon" />
    </div>

    <ModalContainer v-if="open" @modal-close="open=false">
      <template #window>
        <transition name="fade">
          <NavigationMenu />
        </transition>
      </template>
    </ModalContainer>
  </button>
</template>

<script>
import NavigationMenu from "~/components/partials/NavigationMenu.vue";
import ModalContainer from "~/components/ModalContainer.vue";
import IconCompass from "~/components/icons/IconCompass.vue";

export default {
  data: function() {
    return {
      open: false,
    };
  },
  components: {
    ModalContainer,
    NavigationMenu,
    IconCompass
  }
};
</script>

<style lang="scss" scoped>
@import "../../styles/colors";

.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}

.menu-button--container {
  position: relative;
  top: 0;
  left: 0;
  width: 74px;
  height: 74px;

  background-color: $van-cleef;
  border-radius: 15px;
  outline: none;

  &:hover {
    cursor: pointer;
  }

  .menu-button--icon-wrapper {
    width: 58px;
    height: 58px;

    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate3d(-50%, -50%, 0);

    background-color: $ecru-white;
    border-radius: 999px;
  }

  .menu-button--icon {
    width: 50px;
    height: auto;

    transition: transform 0.45s cubic-bezier(.5,-1.5,.5,1.5);
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%) rotate(30deg);
  }

  &:hover, &.menu-button--container--open {
    .menu-button--icon {
      transform: translate(-50%, -50%) rotate(0deg);
    }
  }
}
</style>