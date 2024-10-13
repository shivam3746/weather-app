<script setup>
import { ref } from 'vue';
import axios from 'axios';

const searchQuery = ref("");
const queryTimeout = ref(null);
const API_Key = "API_Key";
const searchResults = ref(null);
const searchError = ref(null);

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () =>{
    if(searchQuery.value != ""){
      try{
        const result = await axios.get(
        `https://api.tomtom.com/search/2/geocode/${searchQuery.value}.json?key=${API_Key}&type=Geography`
      )
      searchResults.value = result.data.results;
      } catch{
        searchError.value = true;
      }
      return;
    }
    searchResults.value = null;
  },300)
}

</script>

<template>
  <main class="container text-white">
      <div class="pt-4 mb-8 relative">
         <input
          v-model="searchQuery"
          @input="getSearchResults" 
          type="text" 
          placeholder="Search for a City or State"
          class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
          />
          <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
              v-if="searchResults">
            <p class="py-2" v-if="searchError">
              Sorry, something went wrong, please try again.
            </p>
            <p class="py-2" v-if="!searchError && searchResults.length === 0">
              No results match your query, try a different term.
            </p>  
            <li v-for="searchResult in searchResults"
            :key="searchResult.id"
            class="py-2 cursor-pointer">
              {{ searchResult.address.freeformAddress }} {{ searchResult.address.country }}
            </li>
          </ul>
      </div>
  </main>
</template>
