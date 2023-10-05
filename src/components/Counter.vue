<template>
  <div class="counter">{{ currentNumber }}</div>
</template>

<script>
export default {
  data() {
    return {
      currentNumber: 0,
    };
  },
  methods: {
    start() {
      this.currentNumber = 3;
      let a = setInterval(() => {
        let next = this.currentNumber - 1;
        if (next < 1) {
          clearInterval(a);
          this.$emit("ended");
        } else {
          this.currentNumber = next;
        }
      }, 1000);
    },
  },
};
</script>

<style>
.counter {
  display: flex;
  width: 100%;
  height: 100%;
  font-size: 250px;
  align-items: center;
  justify-content: center;
}
</style>
