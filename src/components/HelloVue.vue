<template>
    <p>{{message}}</p>
</template>
 
 
<script lang="ts">
import Vue from "vue";
import Component from "vue-class-component";

@Component
export default class App extends Vue {
  message = "hello word!";
}
</script>
 