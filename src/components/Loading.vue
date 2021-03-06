<template>
  <transition name="fade">
    <div class="s-loader" :swapMode="swapMode">
      <div
        :class="{
          's-loader__bg--semi': semiOpaque,
          's-loader--modeswap': swapMode
        }"
        class="s-loader__bg"
      >
        <div class="s-loader__inner">
          <Spinner
            :swap="swapMode"
            class="s-spinner__overlay"
            :size="'large'"
          />
          <div class="s-loader__text">{{ loaderText }}</div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import Spinner from "./../components/Spinner.vue";
import Button from "./../components/Button.vue";

@Component({
  components: { Spinner, Button }
})
export default class Loading extends Vue {
  @Prop()
  loadingStrs!: any[] | string;

  @Prop({ default: false })
  semiOpaque!: boolean;

  @Prop({ default: false })
  isRandom!: boolean;

  @Prop({ default: false })
  swapMode!: Boolean;

  loaderText: string = "";
  index: number = 0;

  mounted() {
    if (typeof this.loadingStrs === "string") {
      this.loaderText = this.loadingStrs;
    } else {
      this.distinguishNumberOfArrays();
    }
  }

  distinguishNumberOfArrays() {
    if (this.loadingStrs.length > 1) {
      if (this.isRandom) {
        this.loopRandomText();
      } else {
        this.loopText();
      }
    } else {
      this.loaderText = this.loadingStrs[0];
    }
  }

  loopText() {
    this.loaderText = this.loadingStrs[this.index];
    this.index++;
    if (this.index === this.loadingStrs.length) {
      this.index = 0;
    }
    setTimeout(this.loopText, 4000);
  }

  loopRandomText() {
    const randomIndex = Math.floor(Math.random() * this.loadingStrs.length);
    if (this.loaderText === this.loadingStrs[randomIndex]) {
      this.loopRandomText();
    } else {
      this.loaderText = this.loadingStrs[randomIndex];
      setTimeout(this.loopRandomText, 4000);
    }
  }
}
</script>

<style lang="less">
@import "./../styles/Imports";

.s-loader__bg {
  position: fixed;
  box-sizing: border-box;
  top: 0;
  left: 0;
  width: 100%;
  height: 100vh;
  background: @white;
  z-index: 999;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.s-loader__bg--semi {
  background: @day-overlay;
}

.s-loader__inner {
  width: 100%;
  height: auto;
}

.s-loader__text {
  text-align: center;
  color: @dark-2;
  .weight(@medium);
  .margin-top(3);
  .margin-bottom(0);
}

.s-spinner__overlay {
  .padding-top(0);
}

// in case day/night mode needs to be switched
.s-loader--modeswap {
  background: @dark-3;

  .s-loader__text {
    color: @white;
  }
}

.night,
.night-theme {
  .s-loader__bg {
    background: @dark-3;
  }

  .s-loader__bg--semi {
    background: @night-overlay;
  }

  .s-loader__text {
    color: @white;
  }

  // in case day/night mode needs to be switched
  .s-loader--modeswap {
    background: @white;

    .s-loader__text {
      color: @dark-2;
    }
  }
}
</style>
