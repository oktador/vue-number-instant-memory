<template>
  <div class="numberbox" ref="box" @click="clicked">
    <div v-show="show" class="nbin">{{ value }}</div>
  </div>
</template>

<script>
export default {
  props: ["value", "length", "top", "left"],
  data() {
    return {
      show: true,
    };
  },
  mounted() {
    let length = 50;
    let left = 0;
    let top = 0;
    if (this.length) {
      length = this.length;
    }
    if (this.top) {
      top = this.top;
    }
    if (this.left) {
      left = this.left;
    }

    this.$refs.box.style.width = length + "px";
    this.$refs.box.style.height = length + "px";
    this.$refs.box.style.top = top + "px";
    this.$refs.box.style.left = left + "px";
  },
  methods: {
    showNumber(showNumber) {
      this.show = showNumber;
    },
    clicked() {
      this.$emit("clicked", this);
    },
  },
};
</script>

<style>
.numberbox {
  border-radius: 4px;
  background-color: darkseagreen;
  color: white;
  font-style: italic;
  font-size: 40px;
  position: absolute;
}
.nbin {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
</style>
