<template>
  <div class="principal">
    <div class="foto" :style="c == true ? 'display:flex' : 'display:none'">
      <div>
        <div
          style="font-size: 180px; margin-top: 2%;font-family: Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif; color: rgb(31, 150, 186);">
          #{{ numero }}</div>
        <div
          style="font-size: 60px; font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif; margin-top: 6%; width: 100%; text-align: end;color: white;">
          {{ nombre }}</div>
        <div style="display: flex;gap: 70%; justify-content: center; color: white;">
          <div>
            <div> Altura</div>
            <div>{{ estadistica }}</div>
          </div>
          <div>
            <div>Peso</div>
            <div>{{ peso }}kg</div>

          </div>
        </div>

      </div>
      <div style="margin-top: 5%;"> <img :src="img" alt=""></div>

    </div>
    <div style="color: white; text-align: center;font-size: 20px;">
      <div class="fotos" :style="c == true ? 'display:flex' : 'display:none'">Estadistica
        <div class="estadisticas" style="color: white;">
        <div style="margin:;">HP <progress :value= "hp" max="100"></progress>{{ hp }}</div>
      </div>
      </div> 
    </div>
      
    <button @click="obtenerurlpokemon()" style="display: flex; ">Peticion</button>
  </div>
</template>

<script setup>
import HelloWorld from './components/HelloWorld.vue'
import axios from "axios"
import { ref } from "vue"

const numero = ref(" ")
const img = ref("")
const nombre = ref("")
const c = ref(false)
const estadistica = ref("")
const peso = ref("")
const hp= ref("")
const ataque= ref("")
const defensa= ref("")
const as= ref("")
const sd= ref("")
const s= ref("")

async function obtenerurlpokemon() {
  // let e = await axios.get("https://pokeapi.co/api/v2/pokemon?limit=50&offset=0")
  // console.log(r)
  c.value = true
  let r = await axios.get("https://pokeapi.co/api/v2/pokemon/3")
  numero.value = r.data.id
  nombre.value = r.data.name.toUpperCase()
  estadistica.value = r.data.height
  peso.value = r.data.weight
  hp.value = Math.min(Math.max(r.data.stats[0].base_stat, 0), 100);
  ataque.value =r.data.stats[1].base_stat
  defensa.value =r.data.stats[2].base_stat
  as.value =r.data.stats[2].base_stat
  sd.value =r.data.stats[4].base_stat
  s.value =r.data.stats[5].base_stat



  img.value = r.data.sprites.other['official-artwork'].front_default
}
</script>
<style scoped>
.principal {
  height: 100vh;
}

.foto {
  gap: 8%;
  justify-content: center;
  flex-direction: row;

}
.fotos{
  gap: 8%;
  justify-content: center;
  flex-direction: row;
  display: flex;
  flex-direction: column;
}</style>
