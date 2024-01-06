<script setup>
const query = gql`
  query Pokemons {
    pokemons(orderBy: id_ASC, first: 100) {
      createdAt
      description
      id
      nom
      publishedAt
      slug
      updatedAt
      numeroPokedex
      typesPokemon {
        nom
      }
      image {
        url(
          transformation: {
            document: { output: { format: webp } }
            image: { resize: { fit: crop, height: 256, width: 256 } }
          }
        )
      }
      sprite {
        url(
          transformation: {
            document: { output: { format: webp } }
            image: { resize: { height: 256, width: 256 } }
          }
        )
      }
    }
    typesPokemon(first: 100) {
      nom
    }
  }
`;

const pokemons = ref();
const types = ref();

const { data } = await useAsyncQuery(query);
console.log(data.value);
pokemons.value = data.value.pokemons;
types.value = data.value.typesPokemon;

const searchTerm = ref("");
if (data.value) {
  pokemons.value = data.value.pokemons;
}
const filteredAndSortedPokemons = computed(() => {
  let result = pokemons.value.filter(
    (pokemon) =>
      selectedType.value === "all" ||
      pokemon.typesPokemon.some((t) => t.nom === selectedType.value)
  );

  result = result.filter((pokemon) =>
    pokemon.nom.toLowerCase().includes(searchTerm.value.toLowerCase())
  );

  if (selectedSort.value === "numeroPokedex") {
    result.sort((a, b) => a.numeroPokedex - b.numeroPokedex);
  } else if (selectedSort.value === "alphabetical") {
    result.sort((a, b) => a.nom.localeCompare(b.nom));
  }

  return result;
});

const selectedType = ref("all");
const selectedSort = ref("id");
</script>

<template>
  <div class="">
    <div class="flex justify-center items-center">
      <div class="mb-4 space-x-4">
        <input
          type="text"
          v-model="searchTerm"
          id="searchBar"
          placeholder="Rechercher..."
          class="rounded-lg shadow-lg px-2"
        />

        <select v-model="selectedSort" class="rounded-lg shadow-lg px-2">
          <option value="id">Numéro Pokedex</option>
          <option value="alphabetical">Alphabétique</option>
        </select>

        <select v-model="selectedType" class="rounded-lg shadow-lg px-2">
          <option value="all">All</option>
          <option v-for="type in types" :value="type.nom">
            {{ type.nom }}
          </option>
        </select>
      </div>
    </div>

    <div class="pr-4">
      <ul
        v-if="filteredAndSortedPokemons"
        class="grid gap-4 grid-cols-2 sm:grid-cols-4 lg:grid-cols-6 2xl:grid-cols-8"
      >
        <li
          v-for="pokemon in filteredAndSortedPokemons"
          :key="pokemon.id"
          class="bg-gray-200 p-4 rounded-lg shadow-lg hover:shadow-sm hover:bg-gray-300 transform hover:scale-95 transition duration-150 ease-in-out"
        >
          <NuxtLink
            :to="`/pokemon/${pokemon.slug}`"
            class="flex flex-col items-center"
          >
            <div class="size-24 flex items-center justify-center">
              <NuxtImg
                :src="pokemon.sprite.url"
                :alt="pokemon.nom"
                class="max-w-full max-h-full object-contain"
              />
            </div>
            <h2 class="text-xl font-bold mt-2">{{ pokemon.nom }}</h2>
            <p class="text-gray-700">{{ pokemon.numeroPokedex }}</p>
          </NuxtLink>
        </li>
      </ul>
      <ul v-else>
        <li>Loading...</li>
      </ul>
    </div>
  </div>
</template>
