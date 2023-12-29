<script>
import PokemonImage from './components/PokemonImage.vue'
import PokemonData from './components/PokemonData.vue'
import PokemonStats from './components/PokemonStats.vue'

import imagePlaceholder from './assets/placeholder.png'

export const apiUrl = 'https://pokeapi.co/api/v2/pokemon/';

export default {
  components: {
    PokemonImage,
    PokemonData,
    PokemonStats
  },
  data() {
    return {
      searchTerm: '',
      pokemonId: '',
      pokemonName: '---',
      pokemonInfo: '---',
      pokemonImage: imagePlaceholder,
      pokemonTypes: [],
      pokemonStats: {},
      searchDone: true,
      chartDone: true
    }
  },
  methods: {
    async search() {
      this.searchDone = false;
      this.chartDone = false;
      console.log('searching for: ' + this.searchTerm);

      const searchTerm = this.searchTerm.toLowerCase();

      try {
          const response = await fetch(apiUrl + searchTerm);
          if (response.status != 200) {
              this.pokemonName = 'NOT FOUND';
              this.pokemonImage = imagePlaceholder;
              this.pokemonInfo = '---';
              this.pokemonTypes = [];
              this.pokemonStats = {};
              console.log('Looks like there was a problem. Status Code: ' + response.status);
              this.searchDone = true;
              this.chartDone = true;
              return;
          }

          const data = await response.json();
          const speciesResponse = await fetch(data.species.url);
          this.pokemonData = data;
          this.pokemonName = data.name.charAt(0).toUpperCase() + data.name.slice(1);
          this.pokemonImage = data.sprites.other.home.front_default;
          this.pokemonTypes = data.types;
          this.pokemonStats = data.stats;

          const speciesData = await speciesResponse.json();
          this.pokemonInfo = speciesData.flavor_text_entries[0].flavor_text;

      } catch (error) {
          console.log('Error:', error);
      }
      this.searchDone = true;

  },
    

  handleChartDone() {
      this.chartDone = true
    }
  },
  watch: {
    pokemonImage() {
      if (this.pokemonImage == null) {
        this.pokemonImage = imagePlaceholder
      }
    }
  },
  computed: {
    enableSearch() {
      return this.searchDone && this.chartDone
    }
  },
  mounted() {
    
  }

}




</script>

<template>
  <div class="w-full max-w-screen-md m-auto flex align-center md:flex-row flex-col text-white">
    <div class="left-side border border-solid border-slate-300 inline-block md:w-1/2 w-full bg-red-600 p-5">
      <!-- Pokemon Image container -->
      <div class="pokemon-img-container bg-white p-3">
        <div class="pokemon-img  bg-slate-300 w-full text-center m-auto p-3">
          <!-- Pokemon Image -->

            <PokemonImage :pokemonImage="pokemonImage"/>

        </div>
        <div class="lights flex align-center justify-center">
          <div class="light w-3.5 h-3.5 rounded-full m-1 bg-red-700"></div>
          <div class="light w-3.5 h-3.5 rounded-full m-1 bg-red-700"></div>
        </div>
      </div>
      <!-- Pokemon Communications Speaker and lights -->
      <div class="pokedex-coms">
        <div class="pokedex-coms py-3 flex align-start items-center">
          <div class="speaker bg-gray-800 w-10 h-10  text-center rounded-full m-1"></div>
          <div class="light bg-yellow-300 w-10 h-2.5  text-center rounded-full m-1"></div>
          <div class="light bg-gray-800 w-10 h-2.5  text-center rounded-full m-1"></div>
        </div>
      </div>
      <!-- Pokemon input search, information and controls -->
      <div class="pokedex-controls flex">
        <div class="w-full">
          <!-- Pokemon search input -->
          <div>Introduzca el ID o nombre del Pokemon</div>
          <input @keyup.enter="search" type="text" class="w-full mb-3 text-black" placeholder="Search Pokemon" v-model="searchTerm" :disabled="!enableSearch"/>

          <div class="pokemon-info p-2 border border-solid border-black  bg-green-700">
            <strong>{{pokemonName}}</strong>
              <div class="pokemon-types flex gap-2">
                <div class="pokemon-type" v-for="pokemonType in pokemonTypes" :key="pokemonType.type.name">
                  <span :class="{ [pokemonType.type.name]: pokemonType.type.name, 'unknown': !pokemonType.type.name , 'p-1 inline-block rounded': true}">
                    {{ pokemonType.type.name }}
                  </span>
                </div>
              </div>
            <div class="pokemon-info">
              {{pokemonInfo}}
            </div>
          </div>
        </div>

      </div>
    </div>
    <div class="right-side border border-solid border-slate-300 inline-block md:w-1/2 w-full bg-red-600 p-3">
      <!-- Status Chart -->

      <div class="bg-white">
        <PokemonStats :pokemonStats="pokemonStats" @chartDone="handleChartDone"/>

      </div>

    </div>
  </div>
</template>

<style scoped>

</style>
