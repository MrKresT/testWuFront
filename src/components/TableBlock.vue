<script setup>

import TableRow from "./TableRow.vue";
import {ref} from "vue";

const $props = defineProps({
      infoPostIndexes: {
        type: Array,
        default: () => [],
      },
      totalPages: {
        type: Number,
        default: 1,

      },
      currentPage: {
        type: Number,
        default: 1
      }
    }),
    $emit = defineEmits([
      'eventDelete',
      'changePage'
    ]);

function changePage(negative = false) {
  $emit('changePage', $props.currentPage + (negative ? -1 : 1));
}

function eventDelete(postCode) {
  $emit('eventDelete', postCode);
}
</script>

<template>
  <table class="w3-table-all w3-hoverable">
    <thead>
    <tr class="w3-light-grey">
      <th>Post index</th>
      <th>Address</th>
      <th>Post Office</th>
      <th>Actions</th>
    </tr>
    <template v-for="(infoPostIndex, index) in $props.infoPostIndexes">
      <table-row
          :infoPostIndex="infoPostIndex"
          @event-delete="eventDelete"
      ></table-row>
    </template>
    <tr>
      <td colspan="3" class="w3-light-grey" v-show="infoPostIndexes.length > 1">
        <div class="w3-button w3-right w3-aqua">
          Total pages:{{ $props.totalPages }}
        </div>
        <button
            v-if="totalPages > $props.currentPage"
            class="w3-button w3-right w3-gray"
            @click="changePage(false)">
          Next
        </button>
        <div class="w3-button w3-right w3-aqua">
          Current page: {{ $props.currentPage }}
        </div>
        <button
            v-if="$props.currentPage > 1"
            class="w3-button w3-right w3-gray"
            @click="changePage(true)">
          Previous
        </button>
      </td>
      <td></td>
    </tr>
    </thead>
  </table>

</template>

<style scoped>

</style>
