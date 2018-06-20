<template>
  <div class="full-width center-content">
    <hello-component name="World" />
  </div>
</template>
<script>

import HelloComponent from './components/hello/HelloComponent.vue'
export default {
  components: {
    HelloComponent
  }
}
</script>

<style>
.full-width{
  width: 100%;
};
.center-content{
  display: flex;
  justify-content: center;
  align-items: center;
};
</style>