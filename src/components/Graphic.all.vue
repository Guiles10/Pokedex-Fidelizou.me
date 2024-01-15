<template>
    <section class="sec-modal" >
        <section class="modal">
          <div class="div-header">
            <p>Estatistica Geral </p>
          <small class="small-close" @click="closeGraphic">X</small>
        </div>
        <div class="div-graphic">
          <canvas id="myChart" style="height: 9900px;"></canvas>
        </div>
        </section>
    </section>
      
  </template>
  
  <script lang="ts">
  import axios from "axios";
  import { Component, Vue } from 'vue-property-decorator';
  import Chart from 'chart.js/auto';

  interface iPokemon {
    name: string;
    url: string
  }

@Component
  export default class Life extends Vue {
    pokemon!: iPokemon[];
    pokemonStatus!: number[] | undefined;
    pokemonVida!: number[] | undefined;
    pokemonForça!: number[] | undefined;
    pokemonDefesa: number[] | undefined;
    
    closeGraphic() {
      this.$emit('close-graphic');
    }
    mounted() {
      this.all_pokemon();
    }

    all_pokemon() {
      axios.get("https://pokeapi.co/api/v2/pokemon?limit=150")
        .then((response) => {
          this.pokemon = response.data.results;

          const pokemonIds = this.pokemon.map((pokemon: iPokemon) => {
            return Number(pokemon.url.split('/')[6]);
          });

          const requests = pokemonIds.map((id: number) => {
            return axios.get(`https://pokeapi.co/api/v2/pokemon/${id}`);
          });

          Promise.all(requests)
            .then((responses) => {
              const infoPokemons = responses.map((response) => response.data);
              this.pokemonVida = infoPokemons.map((response) => response.stats[0].base_stat);
              this.pokemonForça = infoPokemons.map((response) => response.stats[1].base_stat);
              this.pokemonDefesa = infoPokemons.map((response) => response.stats[2].base_stat);
              this.pokemonStatus = infoPokemons.map((response) => response.base_experience);

                const ctx = document.getElementById('myChart') as HTMLCanvasElement;
                    Chart.defaults.backgroundColor = '#9BD0F5';
                    Chart.defaults.borderColor = 'rgba(255, 255, 255)';
                    Chart.defaults.color = 'rgba(0, 0, 0)';
                const chart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: this.pokemon.map((p: iPokemon) => p.name.charAt(0).toUpperCase() + p.name.slice(1)), 
                    datasets: [
                      {
                          label: 'Status do Pokémon',
                          data: this.pokemonStatus,
                          backgroundColor: '#9BD0F5',
                          borderWidth: 2,
                          barThickness: 12,
                      },
                      {
                          label: 'Vida do Pokémon',
                          data: this.pokemonVida,
                          borderWidth: 2,
                          backgroundColor: 'green',
                          barThickness: 12,
                      },
                      {
                          label: 'Força do Pokémon',
                          data: this.pokemonForça,
                          backgroundColor: 'grey',
                          borderWidth: 2,
                          barThickness: 12,
                      },
                      {
                          label: 'Defesa do Pokémon',
                          data: this.pokemonDefesa,
                          backgroundColor: 'blue',
                          borderWidth: 2,
                          barThickness: 12,
                      },
                  ]
                },
                options: {
                    indexAxis: 'y',
                    scales: {
                        y: {
                            beginAtZero: false
                        },
                        x: {
                            beginAtZero: false,
                            ticks: {
                                callback: function(value) {
                                    return value.toString();
                                }
                            }
                        }
                    },
                }
                });

            chart.update();
            })
            .catch((error) => {
              console.error(error);
            });
        })
        .catch((error) => {
          console.error(error);
        });
    } 
}

</script>
  

<style>
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
    background-color: rgb(216, 209, 209);
    margin: 0 auto;
    color: rgba(0, 0, 0);
    width:  95%;
    height: 800px;
    border: 3px solid rgba(0, 0, 0, 0.5)  ;
    border-radius: 8px;
    border-radius: 8px;
    overflow: auto;
    max-height: 90%
  }
  #myChart{
    padding: 10px;
    width: 100%;
    height: 2500px;
;
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
</style>