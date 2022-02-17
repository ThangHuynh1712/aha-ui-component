<template>
   <b-dropdown-item 
    v-bind="$attrs"
     v-on="$listeners">
        <template v-slot:[slot]="scope" v-for="(_, slot) of $scopedSlots">
            <slot :name="slot" v-bind="scope" />
        </template>
    </b-dropdown-item>
</template>

<script>
export default {

}
</script>

<style>

</style>