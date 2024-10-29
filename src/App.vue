<template>
  <div id="form">
    <FormComponent />
  </div>
</template>

<script lang="ts">
import { Options, Vue } from "vue-class-component";
import FormComponent from "./components/FormComponent.vue";

@Options({
  components: {
    FormComponent,
  },
})
export default class App extends Vue {}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

html {
  background-color: rgb(221, 221, 221);
}

#form {
  margin: 0 auto;
  background-color: rgb(216, 216, 216);
  width: 40%;
  padding-top: 30px;
  padding-bottom: 30px;
  border-radius: 20px;
  box-shadow: 0px 5px 10px 8px rgba(0, 0, 0, 0.411);
  margin-bottom: 50px;
  transition: all 0.3s ease-in-out;
}

@media only screen and (max-width: 1000px) {
  #form {
    width: 80%;
  }
}
</style>
