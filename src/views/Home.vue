<template>
<Banner />
  <div class="main_container">
    <h1>Build your Burger</h1>
    <BurgerForm />
  </div>
</template>

<script>
import Banner from '../components/Banner.vue';
import BurgerForm from '../components/BurgerForm.vue';

export default {
  name: "Home",
  components: {
    Banner,
    BurgerForm
  }
}
</script>
