<template>
  <v-app>
    <v-container>
      <v-container>
        <v-row>
          <v-container class="text-center">
            <img alt="Vue logo"
              src="https://upload.wikimedia.org/wikipedia/commons/9/98/International_Pok%C3%A9mon_logo.svg" />
            <v-container class="d-flex align-center justify-center">
              <div class="title">
                <v-img :src="require('../src/assets/pokedex.png')" class="my-3" contain height="100" />
                <h1 class="text-center white--text" style="font-size: 5rem">
                  Pokedex
                </h1>
              </div>

            </v-container>
          </v-container>
        </v-row>

        <v-text-field v-model="search" label="Pesquisar" placeholder="Charmander" solo></v-text-field>

        <v-row>
          <v-col cols="6" md="2" v-for="pokemon in filtered_pokemons" :key="pokemon.name">
            <PokemonCard :pokemon="pokemon" @clicked="show_pokemon" />
          </v-col>
        </v-row>
      </v-container>
    </v-container>

    <PokemonInfoDialog :show.sync="show_dialog" :selected_pokemon="selected_pokemon" />
  </v-app>
</template>

<script>
import axios from "axios";

import PokemonCard from "./components/PokemonCard.vue";
import PokemonInfoDialog from "./components/PokemonInfoDialog.vue";

export default {
  name: "App",

  components: {
    PokemonCard,
    PokemonInfoDialog,
  },

  data() {
    return {
      pokemons: [],
      search: "",
      show_dialog: false,
      selected_pokemon: null,
    };
  },

  mounted() {
    axios
      .get("https://pokeapi.co/api/v2/pokemon?limit=493")
      .then((response) => {
        this.pokemons = response.data.results;
      });
  },
  methods: {
    show_pokemon(id) {
      axios.get(`https://pokeapi.co/api/v2/pokemon/${id}`).then((response) => {
        this.selected_pokemon = response.data;
        this.show_dialog = !this.show_dialog;
      });
    },
    get_move_level(move) {
      for (let version of move.version_group_details) {
        if (
          version.version_group.name == "sword-shield" &&
          version.move_learn_method.name == "level-up"
        ) {
          return version.level_learned_at;
        }
      }
      return 0;
    },
  },
  computed: {
    filtered_pokemons() {
      return this.pokemons.filter((item) => {
        return item.name.includes(this.search);
      });
    },
  },
};
</script>

<style>
#app {
  background: radial-gradient(#ffbf0b, #e20000);
  -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
  background-size: cover !important;
  background-position: center;
  min-height: 100vh;
}

.title {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 150px;
  height: 150px;
}
</style>
