<template>
  <div>
    <div id="arriba">
      <input type="text" name="" id="buscar" placeholder="Buscar pokemon" />
      <input type="select" name="" id="" />
    </div>
    <div id="busqueda"></div>
    <div class="principal">
      <div class="principal2">
        <button
          @click="abrirDetalle(index)"
          v-for="(pokemon, index) in pokemons"
          :key="index"
          class="card"
        >
          <div
            class="card-content"
            style="font-family: 'Franklin Gothic Medium'; padding: 15px"
          >
            <div id="arribaCard">
              <div id="izquierda">
                <div class="card-number" style="font-size: 50px">
                  #{{ pokemon.numero }}
                </div>
                <div class="card-name" style="font-size: 25px">
                  {{ pokemon.nombre }}
                </div>
              </div>
              <div
                id="derecha"
                style="gap: 5px; display: flex; flex-direction: column"
              >
                <div
                  id="tipo"
                  v-for="(tipo, i) in pokemon.tipos"
                  :key="i"
                  :style="'background-color: ' + colorestipo[tipo]"
                  class="pokemon-type"
                >
                  <i :class="tipoIconos[tipo]"></i>
                  <p style="margin: 0">{{ tipo }}</p>
                </div>
              </div>
            </div>
            <img
              :src="pokemon.img"
              alt="Imagen de {{ pokemon.nombre }}"
              class="pokemon-image"
            />
          </div>
        </button>
        <div class="modal" v-if="mostrarModal">
          <div class="modal-content">
            <div style="display: flex; justify-content: space-between">
              <h1 style="margin: 0">#{{ detallePokemon.numero }}</h1>
              <button @click="cerrarDetalle()">❌</button>
            </div>
            <h2 style="margin: 0">{{ detallePokemon.nombre }}</h2>
            <div style="display: flex; flex-direction: row">
              <div>
                <img
                  :src="detallePokemon.img"
                  :alt="'Imagen de ' + detallePokemon.nombre"
                  class="modal-image"
                />
              </div>
              <div
                style="
                  width: 500px;
                  display: flex;
                  align-items: center;
                  justify-content: center;
                "
              >
                <table>
                  <tr>
                    <td>HP</td>
                    <td class="progreso">
                      <progress :value="detallePokemon.hp" max="300"></progress
                      >{{ detallePokemon.hp }}
                    </td>
                  </tr>
                  <tr>
                    <td>Ataque</td>
                    <td class="progreso">
                      <progress
                        :value="detallePokemon.ataque"
                        max="300"
                      ></progress
                      >{{ detallePokemon.ataque }}
                    </td>
                  </tr>
                  <tr>
                    <td>Defensa</td>
                    <td class="progreso">
                      <progress
                        :value="detallePokemon.defensa"
                        max="300"
                      ></progress
                      >{{ detallePokemon.defensa }}
                    </td>
                  </tr>
                  <tr>
                    <td>Ataque Especial</td>
                    <td class="progreso">
                      <progress :value="detallePokemon.as" max="300"></progress
                      >{{ detallePokemon.as }}
                    </td>
                  </tr>
                  <tr>
                    <td>Defensa Especial</td>
                    <td class="progreso">
                      <progress :value="detallePokemon.sd" max="300"></progress
                      >{{ detallePokemon.sd }}
                    </td>
                  </tr>
                  <tr>
                    <td>Velocidad</td>
                    <td class="progreso">
                      <progress :value="detallePokemon.s" max="300"></progress
                      >{{ detallePokemon.s }}
                    </td>
                  </tr>
                </table>
              </div>
            </div>
            <div>Altura: {{ detallePokemon.estadistica }}M</div>
            <div>Peso: {{ detallePokemon.peso }}kg</div>
          </div>
        </div>
      </div>
      <div class="Footer">
        <button
          id="cargMas"
          @click="cargarMasPokemons"
          v-if="paginaActual * 50 <= 500"
        >
          Cargar Más
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { ref, onMounted } from "vue";

const pokemons = ref([]);
const paginaActual = ref(1);
const mostrarModal = ref(false);
const detallePokemon = ref({});

async function obtenerurlpokemon() {
  for (let i = 1; i <= 50; i++) {
    const response = await axios.get(`https://pokeapi.co/api/v2/pokemon/${i}`);
    const r = response.data;
    pokemons.value.push({
      numero: r.id,
      nombre: r.name.toUpperCase(),
      estadistica: r.height / 10,
      peso: r.weight / 10,
      hp: Math.min(Math.max(r.stats[0].base_stat, 0), 100),
      ataque: r.stats[1].base_stat,
      defensa: r.stats[2].base_stat,
      as: r.stats[2].base_stat,
      sd: r.stats[4].base_stat,
      s: r.stats[5].base_stat,
      img: r.sprites.other["official-artwork"].front_default,
      mostrarDetalle: false,
      tipos: r.types.map((e) => e.type.name),
    });
  }
}

async function cargarMasPokemons() {
  const paginaSiguiente = paginaActual.value + 1;
  const limite = paginaSiguiente * 50;

  for (let i = (paginaSiguiente - 1) * 50 + 1; i <= limite; i++) {
    const response = await axios.get(`https://pokeapi.co/api/v2/pokemon/${i}`);
    const r = response.data;
    pokemons.value.push({
      numero: r.id,
      nombre: r.name.toUpperCase(),
      estadistica: r.height / 10,
      peso: r.weight / 10,
      hp: Math.min(Math.max(r.stats[0].base_stat, 0), 100),
      ataque: r.stats[1].base_stat,
      defensa: r.stats[2].base_stat,
      as: r.stats[2].base_stat,
      sd: r.stats[4].base_stat,
      s: r.stats[5].base_stat,
      img: r.sprites.other["official-artwork"].front_default,
      mostrarDetalle: false,
      tipos: r.types.map((e) => e.type.name),
    });
  }
  paginaActual.value = paginaSiguiente;
}

const indiceSeleccionado = ref(-1);

function abrirDetalle(index) {
  detallePokemon.value = pokemons.value[index];
  indiceSeleccionado.value = index;
  mostrarModal.value = true;

  document.querySelectorAll(".stat-fill").forEach((bar) => {
    bar.classList.add("stat-fill");
  });

  document.querySelectorAll(".stat-fill").forEach((bar) => {
    bar.classList.remove("exceeded");
  });
}

function cerrarDetalle() {
  mostrarModal.value = false;
  indiceSeleccionado.value = -1;
}

const colorestipo = {
  grass: "#4CAF50",
  poison: "#dc02dc",
  fire: "#FF0000",
  flying: "#00BFFF",
  water: "#2196F3",
  bug: "#6B8E23",
  normal: "#808080",
  electric: "#FFFF00",
  ground: "#d5722c",
  fairy: "#FF69B4",
  fighting: "#d84343",
  psychic: "#fe891c",
  rock: "#c8c8c8",
  ice: "#9bdfff",
  ghost: "#9e029e",
  steel: "#d1d1d1",
  dragon: "#a96b00",
  dark: "#746e64",
};

const tipoIconos = {
  grass: "fa fa-leaf",
  poison: "fa fa-flask",
  fire: "fa fa-fire",
  flying: "fa fa-dove",
  water: "fa fa-tint",
  bug: "fa fa-bug",
  normal: "fa fa-circle",
  electric: "fa fa-bolt",
  ground: "fa fa-globe",
  fairy: "fa fa-star",
  fighting: "fa-solid fa-hand-fist",
  psychic: "fa-solid fa-brain",
  rock: "fa-regular fa-gem",
  ice: "fa-regular fa-snowflake",
  ghost: "fa-solid fa-ghost",
  steel: "fa-solid fa-weight-hanging",
  dragon: "fa-solid fa-dragon",
  dark: "fa-regular fa-moon",
};

onMounted(() => {
  obtenerurlpokemon();
});
</script>

<style scoped>
#arriba {
  display: flex;
  justify-content: space-around;
  align-items: center;
  text-align: center;
  font-size: 125%;
  height: 80px;
  background-color: rgba(0, 204, 255, 0.63);
  color: white;
  position: fixed;
  width: 100%;
  z-index: 50;
  top: 0%;
}

#buscar {
  width: 400px;
  border-radius: 50px;
  border: 0;
}

#busqueda {
  margin-top: 80px;
  height: 400px;
  background-image: url("https://us.v-cdn.net/6034460/uploads/5IJZUB88U07Q/banner-general-1920x415.png");
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
}

.principal {
  gap: 30px;
  margin: 0; 
  padding: 0;
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
  gap: 20px;
  background: linear-gradient(rgba(0, 204, 255, 0.63), white);
  background-position: center;
  background-repeat: no-repeat;
 width: 100%;
  border: none; 
}

.principal2{
  display: flex;
  flex-wrap: wrap;
  gap: 35px;
  justify-content: center;
  margin: 0; 
  padding: 0;
}

.card {
  margin-top: 45px;
  width: 320px;
  background-color: #f0f0f0;
  border-radius: 10px;
  padding: 8px;
  text-align: center;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.3);
}

.card-content {
  margin-bottom: 8px;
}

#arribaCard {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.card-number {
  font-size: 20px;
  color: #007bff;
}

.card-name {
  font-size: 16px;
  margin-top: 5px;
  margin-bottom: 8px;
}

#tipo {
  width: 100px;
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 15px;
}

button {
  border: none;
  cursor: pointer;
}

.pokemon-data {
  font-size: 14px;
}
.pokemon-image {
  max-width: 100%;
  display: block;
  margin: 0 auto;
  margin-top: 10px;
}

.pokemon-type {
  border-radius: 5px;
  width: 30%;
  height: 30px;
  font-size: 12px;
  text-align: center;
  color: black;
  font-weight: bold;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.7);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999;
  font-family: "Franklin Gothic Medium", "Arial Narrow", Arial, sans-serif;
}

.modal-content {
  background-color: white;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
  text-align: center;
}

.modal button {
  background-color: transparent;
  color: white;
  border: none;
  /* padding: 5px 10px; */
  cursor: pointer;
  align-content: right;
}

.modal-image {
  max-width: 100%;
  height: auto;
  margin-top: 10px;
}

progress {
  height: 50px;
  width: 300px;
}

.progreso {
  display: flex;
  align-items: center;
  gap: 15px;
}

#cargMas {
  width: 150px;
  height: 50px;
  border-radius: 100px;
}
.Footer {
  background-color: rgba(0, 204, 255, 0.63);
  width: 100%;
  display: flex;
  justify-content: center;
  padding: 10px 0px;
}
</style>