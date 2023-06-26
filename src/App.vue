<template>
  <div id="app">
    <div class="app">
      <img class='img' alt="Vue logo" src="./assets/logo.png">
      
      <input class='input' type="text" placeholder="Nome do Pokemon" v-model="search" />
      
      <div class="div-filter">
        <p>Compare as Estatiscas dos Pokemon</p>
        <div class="div-btn">
          <button @click="toggleGraphic('all')">Todas as Estatisticas</button>
          <button @click="toggleGraphic('xp')">Experiencia</button>
          <button @click="toggleGraphic('str')">Força</button>
          <button @click="toggleGraphic('vida')">Vida</button>
          <button @click="toggleGraphic('def')">Defesa</button>
        </div>
      </div>
      
      <template v-if="filtered_pokemons && filtered_pokemons.length > 0">
        <Cards :pokemon="filtered_pokemons" @card-clicked="openModal" />
      </template>
      
      <GraphicAll v-if="activeGraph === 'all'" @close-graphic="closeGraphic('all')" />
      <GraphicStr v-if="activeGraph === 'str'" @close-graphic="closeGraphic('str')" />
      <GraphicLife v-if="activeGraph === 'vida'" @close-graphic="closeGraphic('vida')" />
      <GraphicDef v-if="activeGraph === 'def'" @close-graphic="closeGraphic('def')" />
      <GraphicEx v-if="activeGraph === 'xp'" @close-graphic="closeGraphic('xp')" />
      
    </div>
  </div>
</template>

<script lang="ts">
import axios from "axios";
import { Component, Vue } from 'vue-property-decorator';
import Cards from './components/Cards.vue';
import GraphicEx from './components/Graphic.ex.vue';
import GraphicLife from './components/Graphic.life.vue';
import GraphicStr from './components/Graphic.str.vue';
import GraphicDef from './components/Graphic.def.vue';
import GraphicAll from './components/Graphic.all.vue';

interface iPokemon {
  name: string;
  url: string
}

@Component({
  components: {
    Cards,
    GraphicEx,
    GraphicLife,
    GraphicStr,
    GraphicDef,
    GraphicAll,
  },
})

export default class App extends Vue {
  pokemon: iPokemon[] = [];
  search: string | null = "";
  show_modal = false;
  selected_pokemon = {};
  activeGraph: null | string = null

  toggleGraphic(graphic: string) {
    if (this.activeGraph === graphic) {
      this.activeGraph = null;
    } else {
      this.activeGraph = graphic;
    }
  }

  closeGraphic(graphic: string) {
    if (this.activeGraph === graphic) {
      this.activeGraph = null;
    }
  }

  get filtered_pokemons() {
    if (this.search) {
      const searchLowerCase = this.search.toLowerCase();

      return this.pokemon.filter((item: iPokemon) => {
        const itemNameLowerCase = item.name.toLowerCase();
        return itemNameLowerCase.includes(searchLowerCase);
      });
    } else {
      return this.pokemon;
    }
  }

  all_pokemon() {
    axios.get("https://pokeapi.co/api/v2/pokemon?limit=151")
      .then((response) => {
        this.pokemon = response.data.results;
      });
  }

  mounted() {
    this.all_pokemon();
  }

  openModal(pokemon: iPokemon) {
    this.selected_pokemon = pokemon;
    this.show_modal = true;
  }

  closeModal() {
    this.show_modal = false;
  }
}
</script>


<style>
#app {
  background-image: url('./assets/download.jpg');
  height: 100%;
  width: 100%;
}
.app {
  background-size: center;
  width: 100%;
  padding: 5px;
  max-width: 1200px;
  margin: 0 auto;
  box-sizing: border-box;
}

.img {
  width: 100%;
}

.input {
  width: 50%;
  min-width: 200px;
  height: 40px;
  align-items: center;
  display: flex;
  justify-content: center;
  margin: 0 auto;
  border: none;
  padding: 15px;
  border-bottom: 2px solid transparent;
  transition: border-color 0.3s;
  border-radius: 8px;
  margin-top: 50px;
  font-size: 18px;
  font-weight: 700;
  background-color: rgba(255, 255, 255, 0.8);
}

.input:focus {
  border-color: blue;
  border: none;
}
.div-filter{
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
}
.div-filter > p {
  color: red;
  font-size: 35px;
  margin: 25px 0;
  color:  rgba(255, 255, 255);
  font-weight: 500;
  text-align: center;
}
.div-btn{
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}
.div-btn > button {
  border-radius: 8px;
  padding: 10px;
  color: rgba(1, 1, 1);
  font-size: 20px;
  background-color:  rgba(255, 255, 255);
  margin: 10px;
  cursor: pointer;
  font-weight: 700;
}
.div-btn > button:hover {
  background-color: rgba(1, 1, 1, 0.5);
  color:  rgba(255, 255, 255);
}

</style>