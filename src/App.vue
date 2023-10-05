<template>
  <div class="top">
    <hr />
    <div style="float: left">CLICK THE NUMBERS IN ORDER</div>
    Reflex Timeout:
    <input type="number" v-model="reflexTimeout" />
    Box Count:
    <input type="number" v-model="boxCount" />
    <button @click="start" :disabled="status < 3">Start New Game</button>
    <hr />
  </div>

  <div class="bottom">
    <div class="game_area" ref="gameArea">
      <Counter v-show="status == 1" ref="counter" @ended="counterEnded" />
      <div v-if="status > 1">
        <NumberBox
          v-for="(boxInfo, index) in boxInfos"
          :key="index"
          :value="boxInfo.value"
          :length="boxInfo.length"
          :top="boxInfo.top"
          :left="boxInfo.left"
          :ref="'nb' + boxInfo.value"
          @clicked="clicked"
        />
      </div>
      <Result
        :message="resultInfo.message"
        :success="resultInfo.success"
        v-show="status > 3"
      />
    </div>
  </div>
</template>

<script>
import NumberBox from "./components/NumberBox.vue";
import Counter from "./components/Counter.vue";
import Result from "./components/Result.vue";

export default {
  name: "App",
  components: { NumberBox, Counter, Result },
  data() {
    return {
      //array of info of box value, size and position
      //every info will contain: value, length, top, left
      boxInfos: [],
      //the message after the game ends
      resultInfo: {
        message: null,
        success: false,
      },
      //current status
      //0 preparing, 1: counting 2: number showing 3: numbers not showing, 4: result
      status: 3,
      //next number the user must click
      nextNumberToClick: 1,
      //recorded time when game starts
      startTimeMs: 0,
      //v-model of boxCount input
      boxCount: 5,
      //v-model of reflex timeout input
      reflexTimeout: 500,
    };
  },
  methods: {
    start() {
      this.status = 0; //watch will catch this
    },
    counterEnded() {
      this.status = 2; //watch will catch this
    },
    createBoxInfos() {
      const maxBoxLength = 200;
      const boxMargin = 10;

      let currentWidth = this.$refs.gameArea.offsetWidth;
      let currentHeight = this.$refs.gameArea.offsetHeight;

      //Find an initial side length
      let sideLength = (currentWidth * currentHeight) / this.boxCount;
      sideLength = Math.floor(Math.sqrt(sideLength));

      //Now, find the maximum possible side length < maxBoxLength
      let verticalBoxCount;
      let horizontalBoxCount;
      while (true) {
        verticalBoxCount = Math.floor(currentWidth / sideLength);
        horizontalBoxCount = Math.floor(currentHeight / sideLength);
        if (verticalBoxCount * horizontalBoxCount > this.boxCount) {
          break;
        }
        sideLength--;
      }

      if (sideLength > maxBoxLength) {
        sideLength = maxBoxLength;
      }

      verticalBoxCount = Math.floor(currentWidth / sideLength);
      horizontalBoxCount = Math.floor(currentHeight / sideLength);
      let totalBoxCount = verticalBoxCount * horizontalBoxCount;

      //Find random (count of this.boxCount) boxes from totalBoxCount
      let randomlySelectedBoxes = [];
      while (true) {
        let a = Math.floor(Math.random() * totalBoxCount);
        if (!randomlySelectedBoxes.includes(a)) {
          randomlySelectedBoxes.push(a);
        }

        if (randomlySelectedBoxes.length == this.boxCount) {
          break;
        }
      }

      //Find real values for every box
      this.boxInfos = [];
      let verticalPadding = (currentWidth - verticalBoxCount * sideLength) / 2;
      let horizontalPadding =
        (currentHeight - horizontalBoxCount * sideLength) / 2;

      for (let i = 0; i < this.boxCount; i++) {
        let index = randomlySelectedBoxes[i];
        this.boxInfos.push({
          value: i + 1,
          length: sideLength - boxMargin * 2,
          left:
            (index % verticalBoxCount) * sideLength +
            verticalPadding +
            boxMargin,
          top:
            Math.floor(index / verticalBoxCount) * sideLength +
            horizontalPadding +
            boxMargin,
        });
      }
      this.status = 1;
    },
    clicked(box) {
      if (this.status < 3) {
        return;
      }

      if (box.value == this.nextNumberToClick) {
        box.showNumber(true);
        this.nextNumberToClick++;
      } else if (box.value > this.nextNumberToClick) {
        this.status = 4;
      }

      if (this.nextNumberToClick > this.boxCount) {
        this.status = 5;
      }
    },
  },
  watch: {
    status: {
      handler: function (val) {
        if (val == 0) {
          this.createBoxInfos();
        } else if (val == 1) {
          this.$refs.counter.start();
        } else if (val == 2) {
          setTimeout(() => {
            this.boxInfos.forEach((boxInfo) => {
              this.$refs["nb" + boxInfo.value][0].showNumber(false);
            });
            this.status = 3;
          }, this.reflexTimeout);
        } else if (val == 3) {
          this.nextNumberToClick = 1;
          this.startTimeMs = Date.now();
        } else {
          this.resultInfo.success = val == 5;
          this.resultInfo.message =
            val == 5
              ? (Date.now() - this.startTimeMs) / 1000 + " ms"
              : "Wrong Box";
          this.boxInfos.forEach((boxInfo) => {
            this.$refs["nb" + boxInfo.value][0].showNumber(true);
          });
        }
      },
      //deep: true,
    },
  },
};
</script>

<style>
.top {
  margin: 0;
  padding: 0;
}
.bottom {
  flex: 1;
  background-color: #dadada;
}
.game_area {
  width: 100%;
  height: 100%;
  position: relative;
  margin: 0;
  padding: 0;
}
</style>
