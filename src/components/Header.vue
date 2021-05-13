<template>
  <div class="header">
    <a href="#">
      <img src="../assets/logo.png" alt />
    </a>
    <p>Ваш логин: {{l}}</p>
  </div>
</template>

<script>
export default {
  props: ["l"],
};
</script>

<style lang="scss">
.header {
  display: flex;
  justify-content: space-between;
  padding: 10px;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  background: #ffffff;
}
</style>