<script setup>
import {ref} from "vue";

const $emit = defineEmits([
      'eventFindByPostCode',
      'eventFindByAddress',
      'addRecords',
    ]),
    postCode = ref(''),
    address = ref(''),
    errorMessage = ref(''),
    newRecords = ref([]);

function eventFindByPostCode() {
  errorMessage.value = '';
  if (!postCode.value.length) {
    eventFindByAddress();
  } else if (postCode.value.length === 5) {
    address.value = '';
    $emit('eventFindByPostCode', postCode.value)
  } else {
    errorMessage.value = 'Incorrect post code. Length must be 5';
  }
}

function eventFindByAddress() {
  errorMessage.value = '';
  postCode.value = '';
  $emit('eventFindByAddress', address.value)
}

function addRecord() {
  newRecords.value.push({
    post_office_id: '',
    region_ukr: '',
    district_new_ukr: '',
    settlement_ukr: '',
    post_office_ukr: '',
    postal_code: '',
  });
}

function removeRecord(index){
  newRecords.value.splice(index,1)
}

function eventAddRecords(){
  //this need some validation of records
  //for this time delete all records with empty postIndex
  newRecords.value = newRecords.value.filter(item => item.post_office_id);
  $emit('addRecords', JSON.parse(JSON.stringify(newRecords.value)));
  newRecords.value = [];
}

</script>

<template>
  <div class="w3-bar">
    <div class="w3-panel w3-red" v-if="errorMessage && errorMessage.length">
      <h3>Error!</h3>
      <p>{{ errorMessage }}</p>
    </div>
    <div class="w3-bar-item">
      <label>Post Code</label>
      <input v-model="postCode" class="w3-input" type="text" @keyup.enter="eventFindByPostCode">
    </div>
    <div class="w3-bar-item">
      <label>Address</label>
      <input v-model="address" class="w3-input" type="text" @keyup.enter="eventFindByAddress">
    </div>
    <div class="w3-bar-item">
      <div class="w3-btn w3-green" @click="addRecord">
        Add new post
      </div>
    </div>
  </div>

  <div class="w3-container" v-if="newRecords.length">
    <div class="w3-bar w3-border" v-for="(record, index) in newRecords">
      <div class="w3-bar-item">
        <label>Post index</label>
        <input v-model="record.post_office_id" class="w3-input" type="text" maxlength="5">
      </div>
      <div class="w3-bar-item">
        <label>Region</label>
        <input v-model="record.region_ukr" class="w3-input" type="text">
      </div>
      <div class="w3-bar-item">
        <label>District</label>
        <input v-model="record.district_new_ukr" class="w3-input" type="text">
      </div>
      <div class="w3-bar-item">
        <label>Settlement</label>
        <input v-model="record.settlement_ukr" class="w3-input" type="text">
      </div>
      <div class="w3-bar-item">
        <label>Name of office</label>
        <input v-model="record.post_office_ukr" class="w3-input" type="text">
      </div>
      <button class="w3-button w3-right w3-amber" @click="removeRecord(index)">Remove</button>
    </div>
    <div class="w3-bar">
      <button class="w3-button w3-right w3-green" @click="eventAddRecords">Add</button>
    </div>
  </div>
</template>

<style scoped>

</style>
