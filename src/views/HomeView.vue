<template lang="">
  <main
    class="rounded mt-5 container h-screen text-white bg-[url('C:\Users\ÄGARE\vue-project\src\assets\img\tom-barrett-hgGplX3PFBg-unsplash.jpg')] bg-cover"
  >
    <div class="pt-4 mb-8 mt-20 relative bg-black/50 p-4 rounded">
      <input
        type="text"
        @keyup.enter="getSearchResults"
        v-model="searchInput"
        placeholder="search for a city"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-Secondary focus:outline-none"
      />
      <button
        class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded mt-4"
      >
        Click or press enter
      </button>
      <ul
        class="absolute bg-weather-primary text-white w-full shadow-md py-2 top-[66px] p-2 border rounded"
        v-if="searchResult"
      >
        <p v-if="searchResult.length === 0">No results matches your search</p>
        <template v-else>
          <li
            v-for="results in searchResult"
            :key="results.id"
            class="py-2 cursor-pointer"
          >
            <router-link
              :to="{
                name: 'cityView',
                params: {
                  place_name: results.place_name,
                },
                query: {
                  lat: results.geometry.coordinates[1],
                  lng: results.geometry.coordinates[0],
                },
              }"
            >
              {{ results.place_name }}</router-link
            >
          </li>
        </template>
      </ul>
    </div>
  </main>
</template>
<script>
import axios from "axios";
export default {
  data() {
    return {
      searchInput: "",
      searchResult: null,
      searchError: null,
      mapBoxApiKey:
        "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q",
    };
  },
  watch: {
    searchInput() {
      if (this.searchInput <= 0) {
        this.searchResult = "";
      }
    },
  },

  computed: {},
  methods: {
    getSearchResults() {
      setTimeout(async () => {
        clearTimeout(this.getSearchResults);
        if (this.searchInput !== "") {
          try {
            const result = await axios.get(
              `https://api.mapbox.com/geocoding/v5/mapbox.places/${this.searchInput}.json?access_token=${this.mapBoxApiKey}&types=place`
            );
            this.searchResult = result.data.features;
          } catch {
            this.searchError = true;
          }
        } else {
          this.searchResult = null;
        }
      }, 300);
    },
  },
};
</script>
<style lang=""></style>
