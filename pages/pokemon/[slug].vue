<script setup>
const query = gql`
  query Pokemon($slug: String!) {
    pokemon(where: { slug: $slug }) {
      id
      nom
      slug
      description
      taille
      poids
      pv
      male
      femelle
      numeroPokedex

      couleur {
        hex
      }
      attaques {
        nom
        description
        degat

        typeAttaque {
          nom
          image {
            url
          }
        }
        typePokemon {
          nom
          couleur {
            hex
          }
          image {
            url
          }
        }
      }

      zoneMap {
        nom
        image {
          url
        }
      }

      createdAt
      publishedAt
      updatedAt
      stage
      image {
        url(transformation: { document: { output: { format: webp } } })
      }
      shiny {
        url
      }
      sprite {
        url
      }
    }
  }
`;

const pokemon = ref();

const route = useRoute();
const { data } = await useAsyncQuery(query, {
  slug: route.params.slug,
});

console.log(data.value);
pokemon.value = data.value.pokemon;

onMounted(() => {
  function hideAllDivs() {
    document.getElementById("div1").classList.add("hidden");
    document.getElementById("div2").classList.add("hidden");
    document.getElementById("div3").classList.add("hidden");
  }

  document.getElementById("btn1").addEventListener("click", () => {
    hideAllDivs();
    document.getElementById("div1").classList.remove("hidden");
  });

  document.getElementById("btn2").addEventListener("click", () => {
    hideAllDivs();
    document.getElementById("div2").classList.remove("hidden");
  });

  document.getElementById("btn3").addEventListener("click", () => {
    hideAllDivs();
    document.getElementById("div3").classList.remove("hidden");
  });
});
</script>

<template>
  <div class="bg-slate-200 rounded-xl">
    <div
      class="grid grid-cols-1 sm:grid-cols-1 lg:grid-cols-1 2xl:grid-cols-2 justify-items-center p-10"
    >
      <div v-if="pokemon" class="space-y-4 max-w-xl">
        <div class="flex justify-between col-start-2">
          <div class="flex items-center space-x-3">
            <h2 class="font-bold text-40xl pb-2">{{ pokemon.nom }}</h2>
            <h3 class="font-semibold">Numéro Pokedex</h3>

            <p>{{ pokemon.numeroPokedex }}</p>
          </div>
          <div class="flex space-x-5">
            <div class="flex items-center space-x-2">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 24 24"
                class="h-8 fill-pink-500"
              >
                <path
                  d="M11 15.9339C7.33064 15.445 4.5 12.3031 4.5 8.5C4.5 4.35786 7.85786 1 12 1C16.1421 1 19.5 4.35786 19.5 8.5C19.5 12.3031 16.6694 15.445 13 15.9339V18H18V20H13V24H11V20H6V18H11V15.9339ZM12 14C15.0376 14 17.5 11.5376 17.5 8.5C17.5 5.46243 15.0376 3 12 3C8.96243 3 6.5 5.46243 6.5 8.5C6.5 11.5376 8.96243 14 12 14Z"
                ></path>
              </svg>
              <h2 class="text-xl">{{ pokemon.femelle }}</h2>
            </div>
            <div class="flex items-center space-x-2">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 24 24"
                class="h-8 fill-blue-500"
              >
                <path
                  d="M15.0491 8.53666L18.5858 5H14V3H22V11H20V6.41421L16.4633 9.95088C17.4274 11.2127 18 12.7895 18 14.5C18 18.6421 14.6421 22 10.5 22C6.35786 22 3 18.6421 3 14.5C3 10.3579 6.35786 7 10.5 7C12.2105 7 13.7873 7.57264 15.0491 8.53666ZM10.5 20C13.5376 20 16 17.5376 16 14.5C16 11.4624 13.5376 9 10.5 9C7.46243 9 5 11.4624 5 14.5C5 17.5376 7.46243 20 10.5 20Z"
                ></path>
              </svg>

              <h2 class="text-xl">{{ pokemon.male }}</h2>
            </div>
          </div>
        </div>
        <div class="grid justify-items-center h-auto">
          <NuxtImg
            :src="pokemon.image.url"
            :alt="pokemon.nom"
            class="rounded-lg border-4"
            :style="{ borderColor: pokemon.couleur.hex }"
          />
        </div>
        <div class="flex justify-center text-center space-x-4">
          <h3 class="font-semibold">Taille :</h3>
          <p>{{ pokemon.taille }}m</p>
          <h3 class="font-semibold">Poids :</h3>

          <p>{{ pokemon.poids }}kg</p>
        </div>

        <p class="text-center">{{ pokemon.description }}</p>
      </div>
      <div v-else>
        <li>Loading...</li>
      </div>
      <div class="row">
        <div class="text-center space-x-4 py-8">
          <button
            id="btn1"
            class="text-xl font-semibold px-2 border-2 border-black rounded-lg"
          >
            Attaques
          </button>
          <button
            id="btn2"
            class="text-xl font-semibold px-2 border-2 border-black rounded-lg"
          >
            Localisation
          </button>
          <button
            id="btn3"
            class="text-xl font-semibold px-2 border-2 border-black rounded-lg"
          >
            Formes
          </button>
        </div>

        <div id="div1">
          <div v-if="pokemon.attaques" class="p-4 xl:pr-24">
            <h3 class="font-semibold text-2xl pb-4">Attaques :</h3>
            <div>
              <ul class="">
                <li
                  v-for="attaque in pokemon.attaques"
                  :key="attaque.nom"
                  class=""
                >
                  <div class="py-4">
                    <div
                      class="border-l-2 pl-4"
                      :style="{ borderColor: pokemon.couleur.hex }"
                    >
                      <div
                        class="flex space-x-4 items-center justify-between pr-4"
                      >
                        <p class="font-semibold text-lg">
                          {{ attaque.nom }}
                        </p>
                        <p class="font-medium text-sm">
                          Dégât
                          {{ attaque.degat }}
                        </p>
                        <!-- <div
                          class="border-2 h-12 w-24"
                          :style="{
                            borderColor: attaque.typePokemon.couleur.hex,
                          }"
                        ></div> -->
                        <!-- <div v-if="attaque.typeAttaque" class="space-x-2">
                          <NuxtImg
                            :src="attaque.typePokemon.image.url"
                            :alt="attaque.typePokemon.nom"
                            class="h-[3rem]"
                          />
                        </div>
                        <div v-else>
                          <p>Pas d'image disponible</p>
                        </div> -->
                      </div>
                      <p>
                        {{ attaque.description }}
                      </p>
                    </div>
                  </div>
                </li>
              </ul>
            </div>
          </div>
        </div>
        <div id="div2" class="hidden">
          <div class="flex">
            <div class="" v-if="pokemon.zoneMap">
              <p class="font-semibold text-xl py-4">
                {{ pokemon.zoneMap.nom }}
              </p>
              <NuxtImg
                :src="pokemon.zoneMap.image.url"
                :alt="pokemon.zoneMap.nom"
                class="rounded-lg"
              />
            </div>
          </div>
        </div>
        <div id="div3" class="hidden">
          <div class="flex" v-if="pokemon">
            <div class="">
              <NuxtImg
                :src="pokemon.sprite.url"
                :alt="pokemon.nom"
                class="h-auto"
              />
              <p class="font-semibold">Basique</p>
            </div>
            <div class="">
              <NuxtImg
                :src="pokemon.shiny.url"
                :alt="pokemon.nom"
                class="h-auto"
              />
              <p class="font-semibold">Shiny</p>
            </div>
          </div>
        </div>

        <!-- <div
          class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-2 2xl:grid-cols-2 pt-8"
        >
          <div class="flex" v-if="pokemon">
            <div class="">
              <NuxtImg
                :src="pokemon.sprite.url"
                :alt="pokemon.nom"
                class="h-auto"
              />
              <p class="font-semibold">Basique</p>
            </div>
            <div class="">
              <NuxtImg
                :src="pokemon.shiny.url"
                :alt="pokemon.nom"
                class="h-auto"
              />
              <p class="font-semibold">Shiny</p>
            </div>
          </div>
        </div> -->
      </div>
    </div>

    <!-- <div class="flex justify-center py-4">
      <div
        class="rounded-lg inline-block1"
        :style="{
          width: '60vh',
          height: '1rem',
          backgroundColor: pokemon.couleur.hex,
        }"
      ></div>
    </div> -->
  </div>
</template>
