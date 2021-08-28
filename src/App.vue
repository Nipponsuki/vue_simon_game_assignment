<template>
  <Game />
</template>

<script>
import Game from "./components/Game.vue";
export default {
  components: { Game },
};
</script>

<style lang="scss">
*,
*::after,
*::before {
  margin: 0;
  padding: 0;
  box-sizing: inherit;
}
body {
  background: ghostwhite;
}
</style>
