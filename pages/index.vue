<template>
  <div class="flex menu grow_1">
    <nuxt-link class="flex_column menu_elem" to="/bot_menu">
      <img class="menu_elem_img" src="../assets/images/bot.png" alt="">
      <span class="menu_elem_text">Telegram Bot</span>
    </nuxt-link>
  </div>
</template>

<style scoped>
  @import url('~/assets/css/menu.css');
</style>


<script>
export default {
  name: 'IndexPage',
  layout: 'menu_layout'
}
</script>
