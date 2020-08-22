<template>
  <v-app dark>
    <v-app-bar class="app-bar" :clipped-left="clipped" fixed app>
      <img src="img/logo-simplified-chinese-01.svg" />

      <v-flex text-center>太阳能发电站监控系统</v-flex>
      <span>地点名称</span>
    </v-app-bar>
    <v-main>
      <nuxt />
    </v-main>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: "mdi-apps",
          title: "Welcome",
          to: "/",
        },
        {
          icon: "mdi-chart-bubble",
          title: "Inspire",
          to: "/inspire",
        },
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: "Vuetify.js",
    };
  },
};
</script>
