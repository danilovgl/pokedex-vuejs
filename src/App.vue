<template>
  <header>
    <div className="conatainer-title">
      <h2>Pokédex</h2>
    </div>
    <form>
      <div className="container-itens">
        <input v-model="search" type="text" placeholder="Busque seu Pokémon" />
      </div>
    </form>
  </header>
  <nav>
    <div @click="showPokemon(get_id(pokemon))" class='card' v-for="pokemon in filtered_pokemons " :key="pokemon.name">
      <div class="topCard">

        <h2>{{get_name(pokemon)}}</h2>
      </div>
      <img class='pokemons'
        :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${get_id(pokemon)}.png`"
        :alt="pokemon.name" />
    </div>
  </nav>
  <transition name="fade" appear>
    <div class="modal-overlay" v-if="showModal " @click="showModal = false"></div>
  </transition>
  <transition name="slide" appear>
    <div  class="modal" v-if="showModal | selectPokemon">
      <h1>{{get_name(selectPokemon)}}</h1>
      <div class="box-popup">
      <div v-for="type in selectPokemon.types" :key="type.slot"  class="box-popup-type">
        {{type.type.name}}
      </div>
        <img class='pokemons-popup'
        :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${selectPokemon.id}.png`"
        :alt="selectPokemon.name" />
    
      <div class="box-popup-2">
        <p>Altura: {{(selectPokemon.height * 2.4).toFixed(0)}} cm</p>
      </div>
      <div class="box-popup-2">
        <p>Peso: {{(selectPokemon.weight * 0.453592).toFixed(0)}} kg</p>
      </div>
      <div class="container-state" v-for="(stat) in selectPokemon.stats" :key="stat.stat.name">
       <div class="div-state">
        <p>{{ transform_name(stat.stat.name) }} - {{ stat.base_stat }}</p></div>
        </div> 
    </div>
      <button class="button" @click="showModal = false">
        Fechar
      </button>
    </div>
  </transition>
</template>
<script>

import axios from "axios"
import { ref } from "vue"

export default {
  name: 'App',
  components: {

  },

  data() {
    return {
      pokemons: [],
      search: "",
      showModal: false,
      selectPokemon: null
    }
  },
  computed: {
    filtered_pokemons: function () {
      return this.pokemons.filter((item) => {
        return item.name.includes(this.search)
      })
    },
  },

  mounted() {
    axios.get("https://pokeapi.co/api/v2/pokemon?limit=151").then((response) => {
      this.pokemons = response.data.results
      console.log(response)
    })
  },
  methods: {
    get_id(pokemon) {
      return Number(pokemon.url.split("/")[6])
    },
    get_name(pokemon) {
      return pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1)
    },
    showPokemon(id) {
      axios.get(`https://pokeapi.co/api/v2/pokemon/${id}`).then((response) => {
        this.selectPokemon = response.data
        this.showModal = !this.showModal
      })
      
    },
    transform_name(name) {
      if (name.includes("-")) {
        let parts = name.split("-");
        return `${parts[0].charAt(0).toUpperCase()} ${parts[1]
          .charAt(0)
          .toUpperCase()}${parts[1].slice(1)}`;
      }
      return name.charAt(0).toUpperCase() + name.slice(1);
    },
  },


}
</script>