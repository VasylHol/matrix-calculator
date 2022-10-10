<template>
  <div class="wrapper" >
    <matrix-calculator />
  </div>
</template>

<script>
import MatrixCalculator from '@/components/MatrixCalculator.vue'

export default {
  name: 'App',
  components: {
    MatrixCalculator
  }
}
</script>

<style>
 .wrapper{
  display: flex;
  justify-content: center;
  align-items: center;
  height: 70vh;
 }
</style>
