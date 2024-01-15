<template>
  <section>
    <ul class="ul-crard">
      <li
        v-for="OnePokemon in pokemon"
        :key="OnePokemon.name"
        class="pokemon-card"
        @click="handleCardClick(OnePokemon)"
      >
        <img
          class="pokemon-img"
          :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${get_all_id(OnePokemon)}.png`"
          :alt="OnePokemon.name"
        />
        <p class="pokemon-name">{{ OnePokemon.name }}</p>
      </li>
    </ul>

    <section v-if="selectedPokemon" class="sec-modal">
      <div class="modal" >
        <div class="div-header">
          <p>{{ selectedPokemon.name }}</p>
          <small class="small-close" @click="closeModal">X</small>
        </div>
        <div class="div-img">
          <div>
            <img
            :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${get_all_id(selectedPokemon)}.png`"
            alt="selectedPokemon.name" >
            <p>Normal</p>
          </div>
          <div>
            <img
            :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/shiny/${get_all_id(selectedPokemon)}.png`"
            alt="selectedPokemon.name" >
            <p>Shiny</p>
          </div>
        </div>

        <section class="sec-info">
          <div class="div-type">
            <p>Tipo:</p>
            <small>{{ info_pokemon.types[0].type.name }} </small>
            <small v-if="info_pokemon.types[1]" > {{ info_pokemon.types[1].type.name }}</small>
          </div>

          <div class="div-info">
            <p>Tamanho:</p>
            <small>Autura: {{ (info_pokemon.height * 0.1).toFixed(2) }} m</small>
            <small>Peso: {{ (info_pokemon.weight * 0.1).toFixed(2) }} Kg</small>
          </div>

          <div class="div-type">
            <p>Expreriência:</p>
            <small>{{ info_pokemon.base_experience }} </small>
          </div>

          <div class="div-info">
            <p>Status:</p>
            <small>vida: {{ info_pokemon.stats[0].base_stat }} </small>
            <small>Atack: {{ info_pokemon.stats[1].base_stat }} </small>
            <small>defesa: {{ info_pokemon.stats[2].base_stat }} </small>
          </div>

          <div class="div-info">
            <p>Skills:</p>
            <small>bsica: {{ info_pokemon.abilities[0].ability.name }}</small>
            <small>especial: {{ info_pokemon.abilities[1].ability.name }}</small>
          </div>

        </section>

      </div>

    </section>
  </section>
</template>

<script lang="ts">
import axios from "axios";
import { Component, Vue, Prop } from 'vue-property-decorator';

interface iPokemon {
  name: string;
  url: string
}
@Component
export default class Cards extends Vue {
  @Prop({ required: true })

  pokemon: iPokemon[] = [];

  selectedPokemon: iPokemon | string | any= '';

  info_pokemon: any = ''

  handleCardClick(pokemon: iPokemon) {
    this.selectedPokemon = pokemon;
    this.get_id(this.selectedPokemon);
  }

  closeModal() {
    this.selectedPokemon = '';
  }

  get_all_id(pokemon: iPokemon) {
    let number = Number(pokemon.url.split('/')[6]);
    return number;
  }

  get_id(pokemon: iPokemon) {
    let number = Number(pokemon.url.split('/')[6]);
    this.show_pokemon(number)
  }

   show_pokemon(id: number) {
    axios.get(`https://pokeapi.co/api/v2/pokemon/${id}`)
    .then((response: any) => {
      this.info_pokemon = response.data;
    })
    .catch((error: any) => {
      console.error(error);
    });
  }

}
</script>
  
<style scoped>
.ul-crard {
    display: flex;
    align-items: center;
    flex-wrap: wrap;
    justify-content: space-around;
    flex-direction: row;
    margin-top: 50px;
    padding: 0;
}
 .pokemon-card {
    display: flex;
    background: linear-gradient(
    to top left,
    rgba(1, 1, 1, 0.5),
    rgba(255, 255, 255)
  );
    width: 200px;
    padding: 10px;
    text-align: center;
    cursor: pointer;
    transition: box-shadow 0.3s ease;
    align-items: center;
    flex-direction: column;
    margin: 10px ;
    border-radius: 8px;
}
.pokemon-card:hover {
  box-shadow: 0 0 20px 0 #ccc;
}

.pokemon-img {
  width: 135px;
  height: 135px;
  margin: 0 auto;
  transition: transform 0.3s ease;
}

.pokemon-card:hover .pokemon-img {
  transform: scale(1.6);
}
.pokemon-name {
    font-size: 25px;
    font-weight: bold;
    text-transform: capitalize;
}
.poke-img-modal{
    width: 50px;
    height: 50px;
}
.sec-modal{
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 999;
}
.modal {
  background-color: rgba(255, 255, 255, 0.8);
  margin: 0 auto;
  color: white;
  width:  400px;
  height: 715px;
  max-width: 90%;
  border: 3px solid rgb(216, 209, 209);
  border-radius: 8px;
}
.div-header{
  background-color:rgba(0, 0, 0, 0.8);
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px;
  border-radius: 8px 8px 0 0;
  margin-bottom: 5px;
}
.div-header > p {
  color: white;
  font-size: 40px;
  margin: 0;
  text-transform: capitalize;
}
.small-close{
    color: rgb(216, 209, 209);
    font-size: 30px;
    cursor: pointer;
}
.small-close:hover{
    font-size: 35px;
}
.div-img{
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: space-around;
  color: black;
  font-size: 20px;
  margin: 0;
}
.div-img > div > p{
  margin: 0;
}
.div-img > div{
  width: 45%;
  border: 2px solid rgba(0, 0, 0);
  border-radius: 8px;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.div-img > div > img{
  width: 80%;
}
.div-type {
  background-color: rgb(0, 0, 0, 0.8);
  display: flex;
  align-items: center;
  padding: 8px;
  border-radius: 15px;
  margin-top: 10px;
}
.div-type > p {
  font-size: 20px;
  margin: 0;
  color: rgba(0, 243, 41, 0.8);
}
.div-type > small {
  font-size: 18px;
  margin-left: 15px;
  text-transform: capitalize;
}
.div-info{
  background-color: rgb(0, 0, 0, 0.8);
  display: flex;
  align-items: flex-start;
  padding: 8px;
  border-radius: 15px;
  margin-top: 10px;
  flex-direction: column;
}
.div-info > p {
  font-size: 20px;
  margin: 0;
  margin-bottom: 10px;
  color: rgba(0, 243, 41, 0.8);
}
.div-info > small {
  font-size: 18px;
  text-transform: capitalize;
  margin-bottom: 5px;
}
</style>
  