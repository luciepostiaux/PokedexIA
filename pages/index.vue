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
  let result = pokemons.value.filter((pokemon) =>
    pokemon.nom.toLowerCase().includes(searchTerm.value.toLowerCase())
  );

  if (selectedSort.value === "numeroPokedex") {
    result.sort((a, b) => a.id - b.id);
  } else if (
    selectedSort.value === "alphabetical" &&
    selectedType.value !== "all"
  ) {
    result.sort((a, b) => a.nom.localeCompare(b.nom));
    result = result.filter((p) =>
      p.typesPokemon.some((t) => t.nom === selectedType.value)
    );
  } else if (selectedType.value !== "all") {
    result = result.filter((p) =>
      p.typesPokemon.some((t) => t.nom === selectedType.value)
    );
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
          class="rounded-lg shadow-lg"
        />

        <select v-model="selectedSort" class="rounded-lg shadow-lg">
          <option value="id">Numéro Pokedex</option>
          <option value="alphabetical">Alphabétique</option>
        </select>

        <select v-model="selectedType" class="rounded-lg shadow-lg">
          <option value="all">All</option>
          <option v-for="type in types" :value="type.nom">
            {{ type.nom }}
          </option>
        </select>
      </div>
    </div>

    <ul
      v-if="filteredAndSortedPokemons"
      class="grid gap-4 grid-cols-2 sm:grid-cols-3 lg:grid-cols-5 2xl:grid-cols-7"
    >
      <li
        v-for="pokemon in filteredAndSortedPokemons"
        :key="pokemon.id"
        class="bg-[#F2F2F2] rounded-lg shadow-lg p-4 flex items-center justify-center size-"
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
</template>
