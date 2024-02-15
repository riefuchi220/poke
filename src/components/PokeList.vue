<script setup lang="ts">
import { ref, onMounted } from "vue";
import axios from "axios";

interface Pokemon {
  id: number;
  name: string;
  image: string;
}

const pokemonIds = Array.from({ length: 1025 }, (_, i) => i + 1);
const pokemons = ref<Pokemon[]>([]);

async function fetchPokemons() {
  const requests = pokemonIds.map((id) =>
    axios
      .get(`https://pokeapi.co/api/v2/pokemon/${id}`)
      .then((response) => ({
        id: response.data.id,
        name: response.data.name,
        image: response.data.sprites.front_default,
      }))
      .catch((error) =>
        console.error(`Failed to fetch pokemon with id ${id}:`, error)
      )
  );

  const pokemonData = (await Promise.all(requests)).filter(
    (pokemon): pokemon is Pokemon => pokemon !== undefined
  );
  pokemons.value = pokemonData;
}

onMounted(fetchPokemons);
</script>

<template>
  <section class="poke-list">
    <li v-for="pokemon in pokemons" :key="pokemon.id">
      <p>No:{{ pokemon.id }}</p>
      <p>{{ pokemon.name }}</p>
      <img
        :src="pokemon.image"
        :alt="pokemon.name"
        style="width: 100px; height: 100px"
      />
    </li>
  </section>
</template>

<style scoped>
.poke-list {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 24px;
  padding: 0;
  margin: 0 auto;
  list-style: none;
}

li {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 12px;
  border-radius: 20px;
  background-color: aliceblue;
  border: solid 1px rgb(219, 233, 246);
  width: 200px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

img {
  width: 100px;
  height: 100px;
}

p {
  margin: 4px 0;
}
</style>
