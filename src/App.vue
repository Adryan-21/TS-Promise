<template>
  <table>
    <caption>
      Pokemons
    </caption>
    <thead>
      <tr>
        <th>Name</th>
        <th>Types</th>
        <th>URL</th>
        <th>Ability (name)</th>
        <th>Ability (desc)</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(pokemon, index) in state.ListPokemons" :key="index">
        <td>
          {{ pokemon.name }}
        </td>
        <td>
          {{ pokemon.types }}
        </td>
        <td>
          {{ pokemon.url }}
        </td>
        <td>
          {{ pokemon.ability.name }}
        </td>
        <td>
          {{ pokemon.ability.desc }}
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script lang="ts">
import { defineComponent, reactive } from "vue";

interface Pokemon {
  name: string;
  url: string;
  types: string;
  ability: {
    name: string;
    desc: string;
  };
}

export default defineComponent({
  name: "App",
  components: {},
  setup() {
    const state = reactive({
      ListPokemons: [] as Pokemon[],
    });

    function getData() {
      fetch("https://pokeapi.co/api/v2/pokemon")
        .then((res) => res.json())
        .then((data) => {
          data.results.forEach((data: Pokemon) => {
            const pokemon: Pokemon = {} as Pokemon;
            pokemon.name = data.name;
            pokemon.url = data.url;
            fetch(pokemon.url)
              .then((res) => res.json())
              .then((data) => {
                pokemon.types = data.types[0].type.name;
                pokemon.ability = { name: data.abilities[0].ability.name, desc: "" };
                fetch(data.abilities[0].ability.url)
                  .then((res) => res.json())
                  .then((data) => {
                    pokemon.ability.desc = data.effect_entries[0].effect;
                    state.ListPokemons.push(pokemon);
                  });
              });
          });
        });
    }

    getData();

    return { state };
  },
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}

table,
td {
  border: 2px solid black;
}
td {
  padding: 10px;
}
</style>
