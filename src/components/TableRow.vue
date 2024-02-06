<script setup>

const $emit = defineEmits(['eventDelete']),
     $props = defineProps({
       infoPostIndex:{
         type: Object,
         default: () => {},
       }
     });
</script>

<template>
<tr>
  <td>{{ $props.infoPostIndex.postIndex }}</td>
  <td>{{ $props.infoPostIndex.address }}</td>
  <td>{{ $props.infoPostIndex.postOffice }}</td>
  <td><button class="w3-button w3-red" @click="$emit('eventDelete', $props.infoPostIndex.postIndex)">Delete</button></td>
</tr>
</template>

<style scoped>

</style>
