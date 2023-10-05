<template>
  <div class="result" :class="{ success: success, fail: !success }">
    <div class="inner_result">{{ message }}</div>
  </div>
</template>

<script>
export default {
  props: ["message", "success"],
};
</script>

<style>
.result {
  position: absolute;
  display: flex;
  width: 100%;
  height: 100%;
  font-size: 50px;
  align-items: center;
  justify-content: center;
}
.success {
  background-color: rgba(0, 255, 0, 0.7);
}
.fail {
  background-color: rgba(255, 0, 0, 0.7);
}

.inner_result {
  display: inline;
  border-radius: 5px;
  background-color: rgba(218, 218, 218, 0.7);
  padding: 50px;
}
</style>
