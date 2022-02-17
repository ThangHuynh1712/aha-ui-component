<template>
   <b-button 
   v-bind="$attrs"
   v-on="$listeners">
    <slot v-for="(_, name) in $slots" :name="name"/>
   </b-button>
</template>

<script>
export default {
    name: 'AhaButton',
    props: {
    
  },
    mounted(){
      console.log(this.$attrs,this.$slots)
  }
}
</script>

<style>

</style>