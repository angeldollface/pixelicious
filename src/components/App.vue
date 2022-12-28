<!--
PIXELICIOUS by Alexander Abraham, a.k.a. "Angel Dollface".
Licensed under the MIT license.
-->

<template>
<ModelCog/>
</template>
<script lang="ts">
import { defineComponent } from 'vue';
import ModelCog from './ModelCog.vue';
export default defineComponent(
  {
    name: 'App',
    components: {
      ModelCog
    }
  }
);
</script>
<style lang="scss">
@import '../sass/global.scss';
</style>
