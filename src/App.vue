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

    async function getData() {
      const pokemonsResp = await fetch("https://pokeapi.co/api/v2/pokemon");
      const pokemonsList = await pokemonsResp.json();

      pokemonsList.results.forEach(async (element: any) => {
        const pokemon: Pokemon = {} as Pokemon;
        pokemon.name = element.name;
        pokemon.url = element.url;

        const abilityResp = await fetch(element.url);
        const abilityData = await abilityResp.json();
        pokemon.types = abilityData.types[0].type.name;
        pokemon.ability = { name: abilityData.abilities[0].ability.name, desc: "" };

        const descResp = await fetch(abilityData.abilities[0].ability.url);
        const descData = await descResp.json();
        pokemon.ability.desc = descData.effect_entries[0].effect;

        state.ListPokemons.push(pokemon);
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
