<template>
   <b-button-group  
   v-bind="$attrs">
        <slot v-for="(_, name) in $slots" :name="name"/>
   </b-button-group>
</template>

<script>
export default {

}
</script>

<style>

</style>