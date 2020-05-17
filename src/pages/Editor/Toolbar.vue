<template>
  <div class="toolbar--container">
    <div class="toolbar--options">
      <!-- to be done -->
    </div>
    <div class="toolbar--shortcuts">
      <button class="toolbar--designs-button">
        <div class="toolbar--designs-icon-container">
          <IconScan class="toolbar--designs-icon" />
        </div>
        <span class="toolbar--designs-button-text">Designs</span>
      </button>

      <div class="toolbar--shortcuts-row colors">
        <div
          :class="{
              'toolbar--shortcut palette': true,
              'active': colorPicker === 'palettes',
              }"
        >
          <div class="toolbar--shortcut-icon-container">
            <IconPalette class="toolbar--shortcut-icon" />
          </div>
          <div class="toolbar--shortcut-tooltip">Change Palette</div>
        </div>

        <div
          :class="{
                'toolbar--shortcut color-picker': true,
                'active': colorPicker != null,
              }"
          @click="onChangeColorPicker(prevColorPicker)"
        >
          <div class="toolbar--shortcut-icon-container">
            <IconPaintTube class="toolbar--shortcut-icon" />
          </div>
          <div class="toolbar--shortcut-tooltip">Change Color</div>
        </div>
      </div>

      <div class="toolbar--shortcuts-divider"></div>
      <div class="toolbar--shortcuts-row drawing">

        <div
          :class="{
              'toolbar--shortcut brush': true,
              }">
          <div class="toolbar--shortcut-icon-container">
            <IconBrushLarge class="toolbar--shortcut-icon" />
          </div>
          <div class="toolbar--shortcut-tooltip">Brush</div>
        </div>

        <div
          :class="{
              'toolbar--shortcut fill': true,
              }">
          <div class="toolbar--shortcut-icon-container">
            <IconColorFill class="toolbar--shortcut-icon" />
          </div>
          <div class="toolbar--shortcut-tooltip">Fill</div>
        </div>
      </div>
      <div class="toolbar--shortcuts-divider"></div>
      <div class="toolbar--shortcuts-row etc">
        <div
          :class="{
              'toolbar--shortcut settings': true,
              }">
          <div class="toolbar--shortcut-icon-container">
            <IconColorFill class="toolbar--shortcut-icon" />
          </div>
          <div class="toolbar--shortcut-tooltip">Settings</div>
        </div>
        <div
          :class="{
              'toolbar--shortcut preview': true,
              }">
          <div class="toolbar--shortcut-icon-container">
            <IconColorFill class="toolbar--shortcut-icon" />
          </div>
          <div class="toolbar--shortcut-tooltip">Fill</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// icons
import IconScan from "~/components/icons/IconScan.vue";
import IconPaintTube from "~/components/icons/IconPaintTube.vue";
import IconPalette from "~/components/icons/IconPalette.vue";
import IconBrushLarge from "~/components/icons/IconBrushLarge.vue";
import IconColorFill from "~/components/icons/IconColorFill.vue";
import IconDetail from "~/components/icons/IconDetail.vue";
import IconQRCode from "~/components/icons/IconQRCode.vue";

export default {
  name: "ToolBar",
  components: {
    IconScan,
    IconPaintTube,
    IconPalette,
    IconBrushLarge,
    IconColorFill,
    IconDetail,
    IconQRCode
  },
  props: {
    prevColorPicker: {
      type: String,
      required: false
    },
    colorPicker: {
      type: String,
      required: false
    }
  },
  methods: {
    onChangeColorPicker: function(mode) {
      this.$emit("change-color-picker", mode);
    }
  }
};
</script>

<style lang="scss" scoped>
@import "styles/colors";
@import "styles/animations";

.toolbar--container {
  user-select: none;
  align-self: center;
  display: grid;
  grid-template-columns: auto 1fr;
  grid-template-rows: auto;

  width: 350px;
  height: 650px;

  background-color: $pink;
  border-radius: 0px 50px 50px 0px;
  overflow: hidden;
}

.toolbar--options {
  align-self: center;
  display: grid;

  background-color: $misty-rose;
}

.toolbar--shortcuts {
  display: grid;
  grid-template-columns: auto;
  grid-template-rows: min-content;
  align-items: start;
  align-content: start;
  overflow: scroll;
  height: 100%;
}

.toolbar--designs-button {
  // reset
  appearance: none;
  border: 0px;
  outline: none;
  font-family: inherit;

  color: $sand-dune;
  justify-self: center;
  display: inline-block;
  margin-top: 20px;
  background-color: $ecru-white;
  padding: 8px 8px;
  border-radius: 999px;
  cursor: pointer;
}

.toolbar--designs-icon-container {
  position: relative;
  top: 0;
  left: 0;

  display: inline-block;
  vertical-align: middle;
  width: 35px;
  height: 35px;

  background-color: $sand-dune;
  border-radius: 999px;
}

.toolbar--designs-icon {
  position: absolute;
  top: 50%;
  left: 50%;

  width: 80%;
  height: 80%;

  transform: translate(-50%, -50%);
  fill: $ecru-white;
}

.toolbar--designs-button-text {
  display: inline-block;
  margin-left: 5px;
  margin-right: 15px;
  font-size: 1.7rem;
  font-weight: 600;
  vertical-align: middle;
  letter-spacing: 0.5px;
}

.toolbar--shortcuts-row {
  display: grid;
  grid-template-columns: max-content max-content;
  grid-template-rows: max-content;
  column-gap: 15px;
  justify-content: center;

  &.colors {
    margin-top: 14px;
    margin-bottom: 10px;
  }

  &.drawing {
    margin-top: 14px;
    margin-bottom: 10px;
  }
}

.toolbar--shortcuts-divider {
  justify-self: center;
  background-color: $salmon;
  width: 55%;
  height: 4px;
  border-radius: 999px;
}

.toolbar--shortcut {
  &:nth-child(1) {
    justify-self: end;
  }
  &:nth-last-child(1) {
    justify-self: start;
  }

  cursor: pointer;
  position: relative;
  top: 0;
  left: 0;

  .toolbar--shortcut-icon-container {
    box-sizing: border-box;
    width: 60px;
    height: 60px;

    position: relative;
    top: 0;
    left: 0;
    padding: 3px;

    border-radius: 999px;
  }

  .toolbar--shortcut-icon {
    position: relative;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);

    width: 100%;
    height: 100%;
    fill: $blossom;
  }

  &.color-picker {
    .toolbar--shortcut-icon {
      width: 80%;
      height: 80%;
    }
  }

  &.brush {
    .toolbar--shortcut-icon {
      width: 80%;
      height: 80%;
    }
  }

  .toolbar--shortcut-tooltip {
    transition: transform 0.15s cubic-bezier(0.5, 0.1, 0.3, 1.5);
    display: inline-block;
    white-space: nowrap;
    padding: 10px 20px;
    font-size: 1.5rem;
    color: white;
    position: absolute;
    bottom: 100%;
    left: 50%;
    transform: translate(-50%, -10px) scale(0.8);
    background-color: rgba($tiffany-blue, 0.9);
    border-radius: 10px;
    pointer-events: none;
    opacity: 0;
  }

  &:hover,
  &.active {
    .toolbar--shortcut-icon-container {
      background: repeating-linear-gradient(
        -45deg,
        $tiffany-blue-light,
        $tiffany-blue-light 15px,
        $tiffany-blue 15px,
        $tiffany-blue 30px
      );
      background-size: 200% 200%;
      animation: barberpole 3s linear infinite;
    }

    .toolbar--shortcut-icon {
      fill: $frosted-mint;
    }
  }


  &:hover .toolbar--shortcut-tooltip {
    opacity: 1;
    transform: translate(-50%, -10px) scale(1);
  }
}
</style>