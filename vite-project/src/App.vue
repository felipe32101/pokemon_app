<template>
  <div class="principal">
    <div v-for="(pokemon, index) in pokemons" :key="index" class="card">
      <div class="card-content">
        <div class="card-number">#{{ pokemon.numero }}</div>
        <div class="card-name">{{ pokemon.nombre }}</div>
        <div>Altura: {{ pokemon.estadistica }}</div>
        <div>Peso: {{ pokemon.peso }}kg</div>
        <img :src="pokemon.img" alt="Imagen de {{ pokemon.nombre }}" class="pokemon-image" />
        <button @click="mostrarDetalle(index)">VER MAS</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { ref, onMounted } from "vue";

const pokemons = ref([]);

async function obtenerurlpokemon() {
  for (let i = 1; i <= 50; i++) {
    const response = await axios.get(`https://pokeapi.co/api/v2/pokemon/${i}`);
    const r = response.data;

    pokemons.value.push({
      numero: r.id,
      nombre: r.name.toUpperCase(),
      estadistica: r.height,
      peso: r.weight,
      hp: Math.min(Math.max(r.stats[0].base_stat, 0), 100),
      ataque: r.stats[1].base_stat,
      defensa: r.stats[2].base_stat,
      as: r.stats[2].base_stat,
      sd: r.stats[4].base_stat,
      s: r.stats[5].base_stat,
      img: r.sprites.other['official-artwork'].front_default,
      mostrarDetalle: false // Agregar un campo mostrarDetalle
    });
  }
}

function mostrarDetalle(index) {
  // Cambiar el valor de mostrarDetalle para el Pokémon en el índice dado
  pokemons.value[index].mostrarDetalle = true;
}

onMounted(() => {
  obtenerurlpokemon();
});
</script>

<style scoped>
.principal {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
  gap: 20px;
}

.card {
  width: 180px;
  background-color: #f0f0f0;
  border-radius: 10px;
  padding: 8px;
  text-align: center;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.3);
}

.card-content {
  margin-bottom: 8px;
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

button {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 5px 10px;
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
</style>