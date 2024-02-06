<script setup>

import TitleBlock from "../components/TitleBlock.vue";
import TableBlock from "../components/TableBlock.vue";
import PanelBtn from "../components/PanelBtn.vue";
import {onMounted, ref} from "vue";
import axios from "axios";

const
    infoPostIndexes = ref([]),
    address = ref(''),
    infoMessage = ref(''),
    errorMessage = ref(''),
    totalPages = ref(1),
    currentPage = ref(1);

/**
 * Retrieves data from API based on the provided search address.
 * If no search address is provided, retrieves all data by pages.
 *
 * @param {string} searchAddress - The address to search for.
 *
 * @return {void}
 */
function getData(searchAddress = '') {
  errorMessage.value = '';
  const params = {
    ...(currentPage.value > 1 && {
      page: currentPage.value
    }),
    ...(searchAddress && {
      address: searchAddress
    }),
  };
  address.value = searchAddress;
  axios.get(`${import.meta.env.VITE_URL_BACK}/api/postindex`, {params})
      .then((response) => {
        infoPostIndexes.value.splice(0, infoPostIndexes.value.length);
        if (response.data) {
          if (response.data.error) {
            errorMessage.value = response.data.error;
          } else {
            totalPages.value = response.data.totalPages;
            currentPage.value = response.data.currentPage;
            response.data.data.forEach((infoPostIndex) => {
              infoPostIndexes.value.push({
                postIndex: infoPostIndex.post_office_id,
                address: infoPostIndex.address,
                postOffice: infoPostIndex.post_office_ukr
              })
            })
          }
        }
      })
      .catch(e => {
        errorMessage.value = e.message
      });
}

/**
 * Fetches data from API by post code.
 *
 * @param {string} postCode - The post code to retrieve data for.
 * @return {void}
 */
function getDataByPostCode(postCode) {
  errorMessage.value = '';
  axios.get(`${import.meta.env.VITE_URL_BACK}/api/postindex/${postCode}`)
      .then((response) => {
        infoPostIndexes.value.splice(0, infoPostIndexes.value.length);
        if (response.data) {
          if (response.data.error) {
            errorMessage.value = response.data.error;
          } else {
            infoPostIndexes.value.push({
              postIndex: response.data.post_office_id,
              address: response.data.address,
              postOffice: response.data.post_office_ukr
            });
            currentPage.value = 1;
            totalPages.value = 1;
          }
        }
      })
      .catch(e => {
        errorMessage.value = e.message
      });

}

/**
 * Deletes a post code.
 *
 * @param {string} postCode - The post code to be deleted.
 * @return {void}
 */
function deletePostCode(postCode) {
  errorMessage.value = '';
  axios.delete(`${import.meta.env.VITE_URL_BACK}/api/postindex/${postCode}`)
      .then((response) => {
        if (response.data) {
          if (response.data.error) {
            errorMessage.value = response.data.error;
          }
          if (response.data === 'success') {
            infoMessage.value = 'Post code deleted successfully';
            //after some time we remove the message
            setTimeout(() => {
              infoMessage.value = '';
            }, 3000);
            //delete the post code from the list
            let index = infoPostIndexes.value.findIndex((infoPostIndex) => infoPostIndex.postIndex === postCode);
            infoPostIndexes.value.splice(index, 1);
          }
        }
      })
      .catch(e => {
        errorMessage.value = e.message
      });

}

/**
 * Add records to the database.
 *
 * @param {Array} newRecords - Array of records to be added.
 */
function addRecords(newRecords) {
  errorMessage.value = '';
  newRecords.forEach((params) => {
    params.postal_code = params.post_office_id;
    axios.post(`${import.meta.env.VITE_URL_BACK}/api/postindex/add`, params )
        .then((response) => {
          if (response.data) {
            console.log(response.data);
            if (response.data.error) {
              errorMessage.value = response.data.error;
            } else {
                infoMessage.value = infoMessage.value + `Post code "${response.data}" added successfully  `;
            }
          }

        })
        .catch(e => {
          errorMessage.value = e.message
        });
  })
}

function changePage(pageNumber) {
  currentPage.value = pageNumber;
  getData(address.value);
}

onMounted(() => {
  getData();
})

</script>

<template>
  <title-block></title-block>
  <panel-btn
      @event-find-by-post-code="getDataByPostCode"
      @event-find-by-address="getData"
      @add-records="addRecords"
  ></panel-btn>
  <div class="w3-panel w3-red" v-if="errorMessage && errorMessage.length">
    <h3>Error!</h3>
    <p>{{ errorMessage }}</p>
  </div>
  <div class="w3-panel w3-green" v-if="infoMessage && infoMessage.length">
    <h3>Information!</h3>
    <p>{{ infoMessage }}</p>
  </div>

  <table-block
      :infoPostIndexes="infoPostIndexes"
      :current-page="currentPage"
      :total-pages="totalPages"
      @change-page="changePage"
      @event-delete="deletePostCode"
  ></table-block>

</template>

<style scoped>

</style>
