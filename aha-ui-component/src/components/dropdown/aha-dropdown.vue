<template>
   <b-dropdown 
    v-bind="$attrs">
    <template v-slot:[slot]="scope" v-for="(_, slot) of $scopedSlots">
      <slot :name="slot" v-bind="scope" />
    </template>
    </b-dropdown>
</template>

<script>
export default {

}
</script>

<style>

</style>