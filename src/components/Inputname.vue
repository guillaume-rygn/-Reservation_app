<template>
  <input
    type="text"
    placeholder="votre nom"
    v-model="title"
    v-on:input="updateName()"
  />
</template>

<script>
  export default{
    name:'Inputname',
    data() {
      return {
        title: null,
      };
    },
    methods:{
      updateName(){
        this.$emit('nameChoice', this.title)
      }
    }
  
  }
</script>

<style>
  
</style>