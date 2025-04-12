<template>
  <section>
    <!-- 头部菜单 -->
    <title-bar />
    <page-main />
    <Footer-code v-if="!$route.path.includes('homepage')" />
  </section>
</template>
<script>
import TitleBar from './website-layout/TitleBar.vue'
import PageMain from './website-layout/PageMain.vue'
import FooterCode from './website-layout/Footer.vue'
export default {
  components: { TitleBar, PageMain, FooterCode },
  data() {
    return {
    }
  },
  created() {
  },
  methods: {
  }
}
</script>

<style lang="scss" scoped>
// @import "@/assets/css/mixin.scss";
// @import "@/assets/css/variables.scss";

</style>
