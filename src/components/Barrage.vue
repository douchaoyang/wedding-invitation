<template>
  <div
    class="wedding-barrage"
    ref="barrage"
    :style="{ opacity: canStart ? 1 : 0 }"
  >
    <div v-html="codeInStyleTag"></div>
    <p class="code barrage-0" ref="barrageFirst" :style="{ top: '.2rem' }">
      <span class="mine">{{ wish }}</span>
      <span v-for="(item, index) in filterBarrage(barrages, 0)" :key="index">{{
        item
      }}</span>
    </p>
    <p class="code barrage-1" ref="barrageSecond" :style="{ top: '.6rem' }">
      <span v-for="(item, index) in filterBarrage(barrages, 1)" :key="index">{{
        item
      }}</span>
    </p>
    <p class="code barrage-2" ref="barrageThird" :style="{ top: '1rem' }">
      <span v-for="(item, index) in filterBarrage(barrages, 2)" :key="index">{{
        item
      }}</span>
    </p>
    <p class="code barrage-3" ref="barrageFourth" :style="{ top: '1.4rem' }">
      <span v-for="(item, index) in filterBarrage(barrages, 3)" :key="index">{{
        item
      }}</span>
    </p>
  </div>
</template>

<script>
import data from "../mock/data";

export default {
  props: ["wish", "canStart"],
  data() {
    return {
      barrages: data.barrages,
      animationStyle: "",
      initialOffset: 0,
    };
  },
  computed: {
    codeInStyleTag: function () {
      return `<style>${this.animationStyle}</style>`;
    },
  },
  watch: {
    canStart: function (val) {
      if (val === true) {
        this.barrageAnimationStart();
      }
    },
  },
  mounted() {
    // alert(this.getWidth(this.$refs.barrage));
    this.barrageAnimationStart();
  },
  methods: {
    // 弹幕动画开始
    barrageAnimationStart() {
      let barrageWidth = this.getWidth(this.$refs.barrage);
      let barrageWidthGroup = [
        this.getWidth(this.$refs.barrageFirst),
        this.getWidth(this.$refs.barrageSecond),
        this.getWidth(this.$refs.barrageThird),
        this.getWidth(this.$refs.barrageFourth),
      ];
      this.initialOffset = barrageWidth + 15;
      barrageWidthGroup.map((item, index) => {
        this.animationStyle += `
            .barrage-${index}{
              animation: barrage-${index} ${item / 40}s linear infinite;
              -webkit-animation: barrage-${index} ${item / 40}s linear infinite;
            }
            @keyframes barrage-${index} {
              from {
                transform:translate3d(${barrageWidth + 15}px,0,0);
                -webkit-transform:translate3d(${barrageWidth + 15}px,0,0);
              }
              to {
                transform:translate3d(-${item + 15}px,0,0);
                -webkit-transform:translate3d(-${item + 15}px,0,0);
              }
            }`;
      });
    },
    getWidth(ref) {
      return ref.offsetWidth;
    },
    filterBarrage(barrages, remainder) {
      if (barrages) {
        return barrages.filter((barrage, index) => {
          if (index % 4 === remainder) {
            return barrage;
          }
        });
      }
    },
  },
};
</script>

<style lang="less">
.wedding-barrage {
  width: 100%;
  height: 1.8rem;
  position: relative;
  p {
    position: absolute;
    padding: 0.05rem 0;
    white-space: nowrap;
    transition: all 0.6s linear;
    -webkit-transition: all 0.6s linear;
    span {
      padding: 0 0.15rem;
      &.mine {
        color: #e6db74;
        padding: 0.04rem 0.06rem;
        border: 1px solid #e6db74;
      }
    }
  }
}
</style>