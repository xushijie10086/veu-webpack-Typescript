<template>
  <div id="app">
    <hello1/>
  </div>
</template>
 
<script  lang="ts">
import Vue from 'vue'
import hello from './components/HelloVue.vue'
import Component from 'vue-class-component'
@Component({
    components: {
        'hello1': hello
    }
})
export default class App extends Vue{
}
</script>