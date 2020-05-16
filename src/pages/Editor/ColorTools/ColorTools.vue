<template>
  <div class="color-tools--container open">
    <Palette
      :drawingTool="drawingTool"
      @change-current-color="onChangeCurrentColor"/>


    <div class="color-tools--color-pickers">
      <div class="color-tools--color-picker-tabs">
        <div class="color-tools--color-picker-tab open">
          ACNL
        </div>
        <div class="color-tools--color-picker-tab">
          ACNH
        </div>
        <div class="color-tools--color-picker-tab">
          Wheel
        </div>
      </div>

      <div class="color-tools--color-picker-content">
        <ACNLColorPicker
          :drawingTool="drawingTool"
          @color-picked="onColorPicked"/>
      </div>
    </div>

  </div>
</template>


<script>
import Palette from "./Palette";
import DrawingTool from "~/libs/DrawingTool";
import ACNLColorPicker from "./ACNLColorPicker";

export default {
  name: "ColorTools",
  props: {
    drawingTool: {
      type: DrawingTool,
      required: true
    },
    openColorPicker: {
      type: Boolean,
      required: false
    },
    mode: {
      type: String,
      required: false
    }
  },
  components: {
    Palette,
    ACNLColorPicker
  },
  methods: {
    onChangeCurrentColor: function(idx) {
      this.$emit("change-current-color", idx);
    },
    onColorPicked: function(color) {
      this.$emit("color-picked", color);
    },
    onColorPickerClose: function() {
      this.$emit("color-picker-close");
    }
  }
}
</script>

<style lang="scss" scoped>
@import "styles/colors";

@keyframes color-tools-barberpole {
  0% {background-position: 100% 100%;}
  100% {background-position: 0% 0%;}
}
.color-tools--container {
  user-select: none;
  display: inline-block;
  background: $pink;

  padding: 0px 25px 5px 25px;
  padding-top: 0px;
  padding-right: 25px;
  padding-bottom: 5px;
  padding-left: 25px;
  border-radius: 0px 0px 50px 50px;
  text-align: left;

  &.open {
    background: repeating-linear-gradient(
      -45deg,
      $pastel-pink,
      $pastel-pink 20px,
      $piggy-pink 20px,
      $piggy-pink 40px
    );
    background-size: 200% 200%;
    // animation: color-tools-barberpole 20s linear infinite;
    padding-bottom: 25px;
  }
}

.color-tools--color-pickers {
  margin-top: 15px;
}

.color-tools--color-picker-tabs {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: auto;
  color: $umber;


  .color-tools--color-picker-tab {
    padding-top: 10px;
    padding-bottom: 15px;
    background-color: $pastel-pink;
    text-align: center;
    &.open {
      background-color: $pink;
      cursor: default;
    }
    cursor: pointer;
    border-radius: 25px 25px 0px 0px;
  }
}

.color-tools--color-picker-content {
  background-color: pink;
  padding: 20px 25px;
  border-radius: 0px 20px 20px 20px;
}
</style>