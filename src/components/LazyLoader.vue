<template>
  <div class="loader-box"></div>
</template>

<script>
export default {};
</script>

<style lang="scss">
.loader-box {
  margin: 10px 0;
  height: 30px;
  width: 90%;
  position: relative;
  overflow: hidden;
  background: #e2e2e2;

  &::before {
    content: "";
    display: block;
    position: absolute;
    left: -150px;
    top: 0;
    height: 100%;
    width: 150px;
    background: linear-gradient(
      to right,
      transparent 0%,
      #dadada 50%,
      transparent 100%
    );
    animation: load 1s cubic-bezier(0.4, 0, 0.2, 1) infinite;
  }

  @media screen and (max-width: 576px) {
    margin: 10px auto;
  }
}

@keyframes load {
  from {
    left: -150px;
  }
  to {
    left: 100%;
  }
}
</style>