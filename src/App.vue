<template>
  <b-container fluid id="app">
    <b-row class="app-header">
      <b-col>
        <b-img src="./assets/truora_logo-color.png" alt="Truora logo" fluid></b-img>
        <p class="lead">
          Interfaz usando Vue y BootstrapVue creada para resolver technical exercise propuesto por Truora
          <b-link href="https://www.truora.com/" target="_blank">https://www.truora.com/</b-link>
        </p>
      </b-col>
    </b-row>
    <!-- use router-link component for navigation. -->
    <!-- specify the link by passing the `to` prop. -->
    <!-- <router-link> will be rendered as an `<a>` tag by default -->
    <!-- <nav>
      <router-link to="/serverinfo">ServerInfo</router-link>
    </nav>-->
    <!-- route outlet -->
    <!-- component matched by the route will render here -->
    <router-view></router-view>
  </b-container>
</template>

<script>
export default {
  name: "app"
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
.app-header {
  text-align: center;
}
img {
  width: 50%;
  margin-bottom: 60px;
}
.lead {
  margin-bottom: 60px;
}
</style>
