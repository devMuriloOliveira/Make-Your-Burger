<template>
  <div class="main-container">
      <banner/>
      <h1>formulario</h1>
      <BurgerForm/>
  </div>
</template>

<script>
import Banner from '@/components/Banner.vue'
import BurgerForm from '@/components/BurgerForm.vue'


export default {
  name: 'HomeView',
  components: {
    Banner,
    BurgerForm
  }
}
</script>
    BurgerForm
