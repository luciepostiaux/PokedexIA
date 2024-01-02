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
      typesPokemon {
        couleur {
          hex
        }
        nom
        image {
          url(
            transformation: {
              image: { resize: {} }
              document: { output: { format: webp } }
            }
          )
        }
      }
      attaques {
        nom
        description
        degat

        typeAttaque {
          nom
          image {
            url(transformation: { document: { output: { format: webp } } })
          }
        }
        typePokemon {
          nom
          couleur {
            hex
          }
          image {
            url(transformation: { document: { output: { format: webp } } })
          }
        }
      }

      zoneMap {
        nom
        image {
          url(transformation: { document: { output: { format: webp } } })
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
        url(transformation: { document: { output: { format: webp } } })
      }
      sprite {
        url(transformation: { document: { output: { format: webp } } })
      }
    }
  }
`;

import { ref, onMounted } from "vue";
import { useRoute } from "vue-router";

const pokemon = ref();
const activeTab = ref("attaques");

const route = useRoute();
const { data } = await useAsyncQuery(query, {
  slug: route.params.slug,
});

pokemon.value = data.value.pokemon;

function setActiveTab(tabName) {
  activeTab.value = tabName;
}
</script>

<template>
  <div class="bg-white rounded-xl p-10 shadow-lg max-w-4xl mx-auto">
    <div v-if="pokemon" class="grid grid-cols-1 md:grid-cols-3 md:gap-4">
      <div class="flex justify-center md:block hidden self-center">
        <NuxtImg
          :src="pokemon.image.url"
          :alt="pokemon.nom"
          class="rounded-lg border-4 h-64 w-64 object-cover"
          :style="{ borderColor: pokemon.couleur.hex }"
        />
      </div>

      <div class="md:col-span-2 space-y-4">
        <div
          class="flex flex-col md:flex-row justify-between items-start md:items-center"
        >
          <div class="flex items-center space-x-2">
            <h2 class="font-bold text-3xl md:text-4xl">{{ pokemon.nom }}</h2>
            <p class="text-gray-800">nº {{ pokemon.numeroPokedex }}</p>

            <div class="flex space-x-2">
              <img
                v-for="(type, index) in pokemon.typesPokemon"
                :key="index"
                :src="type.image.url"
                :alt="type.nom"
                class="h-8 w-8 md:h-10 md:w-10 object-cover"
              />
            </div>
          </div>
          <div class="flex justify-center py-2 block md:hidden">
            <NuxtImg
              :src="pokemon.image.url"
              :alt="pokemon.nom"
              class="rounded-lg border-4 max-w-full h-auto object-cover"
              :style="{ borderColor: pokemon.couleur.hex }"
            />
          </div>
          <div class="flex space-x-5 mt-2 md:mt-0">
            <div class="flex items-center space-x-1">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 24 24"
                class="h-4 md:h-5 fill-pink-500"
              >
                <path
                  d="M11 15.9339C7.33064 15.445 4.5 12.3031 4.5 8.5C4.5 4.35786 7.85786 1 12 1C16.1421 1 19.5 4.35786 19.5 8.5C19.5 12.3031 16.6694 15.445 13 15.9339V18H18V20H13V24H11V20H6V18H11V15.9339ZM12 14C15.0376 14 17.5 11.5376 17.5 8.5C17.5 5.46243 15.0376 3 12 3C8.96243 3 6.5 5.46243 6.5 8.5C6.5 11.5376 8.96243 14 12 14Z"
                ></path>
              </svg>
              <h2 class="text-sm md:text-md">{{ pokemon.femelle }}</h2>
            </div>
            <div class="flex items-center space-x-1">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 24 24"
                class="h-4 md:h-5 fill-blue-500"
              >
                <path
                  d="M15.0491 8.53666L18.5858 5H14V3H22V11H20V6.41421L16.4633 9.95088C17.4274 11.2127 18 12.7895 18 14.5C18 18.6421 14.6421 22 10.5 22C6.35786 22 3 18.6421 3 14.5C3 10.3579 6.35786 7 10.5 7C12.2105 7 13.7873 7.57264 15.0491 8.53666ZM10.5 20C13.5376 20 16 17.5376 16 14.5C16 11.4624 13.5376 9 10.5 9C7.46243 9 5 11.4624 5 14.5C5 17.5376 7.46243 20 10.5 20Z"
                ></path>
              </svg>
              <h2 class="text-sm md:text-md">{{ pokemon.male }}</h2>
            </div>
          </div>
        </div>

        <div>
          <h3 class="font-semibold">Taille : {{ pokemon.taille }}m</h3>
          <h3 class="font-semibold">Poids : {{ pokemon.poids }}kg</h3>
        </div>
        <p>{{ pokemon.description }}</p>
      </div>

      <div class="grid col-start-1 col-end-7 pt-8">
        <div
          class="flex flex-wrap justify-center md:justify-start space-x-2 md:space-x-4"
        >
          <button
            @click="setActiveTab('attaques')"
            class="text-base md:text-xl font-semibold px-1 md:px-2 py-1 border-2 rounded-lg"
            :class="{ 'bg-gray-200': activeTab === 'attaques' }"
            :style="{ borderColor: pokemon.couleur.hex }"
          >
            Attaques
          </button>
          <button
            @click="setActiveTab('localisation')"
            class="text-base md:text-xl font-semibold px-1 md:px-2 py-1 border-2 rounded-lg"
            :class="{ 'bg-gray-200': activeTab === 'localisation' }"
            :style="{ borderColor: pokemon.couleur.hex }"
          >
            Localisation
          </button>
          <button
            @click="setActiveTab('formes')"
            class="text-base md:text-xl font-semibold px-1 md:px-2 py-1 border-2 rounded-lg"
            :class="{ 'bg-gray-200': activeTab === 'formes' }"
            :style="{ borderColor: pokemon.couleur.hex }"
          >
            Formes
          </button>
        </div>

        <div v-if="activeTab === 'attaques'" class="animate-fade-in-down">
          <div>
            <ul class="pt-4">
              <li v-for="attaque in pokemon.attaques" :key="attaque.nom">
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
                        <span
                          class="uppercase text-xs"
                          :style="{ color: attaque.typePokemon.couleur.hex }"
                          >{{ attaque.typePokemon.nom }}</span
                        >
                      </p>
                      <div>
                        <span
                          v-if="attaque.typeAttaque"
                          class="text-xs font-medium ml-2"
                        >
                          {{ attaque.typeAttaque.nom }}
                        </span>
                        <p class="font-medium text-sm">
                          Dégât: {{ attaque.degat }}
                        </p>
                      </div>
                    </div>
                    <div class="flex justify-between items-center">
                      <p>
                        {{ attaque.description }}
                      </p>
                      <!-- <img
                        v-if="attaque.typeAttaque && attaque.typeAttaque.image"
                        :src="attaque.typeAttaque.image.url"
                        :alt="attaque.typeAttaque.nom"
                        class="h-8 w-8 object-cover ml-2"
                      /> -->
                    </div>
                  </div>
                </div>
              </li>
            </ul>
          </div>
        </div>
        <div v-if="activeTab === 'localisation'" class="animate-fade-in-down">
          <div
            class="flex flex-col md:flex-row items-center md:items-start space-y-4 md:space-y-0 md:space-x-4 pt-8 justify-around"
          >
            <p class="font-semibold text-lg self-center">
              {{ pokemon.zoneMap.nom }}
            </p>
            <NuxtImg
              :src="pokemon.zoneMap.image.url"
              :alt="pokemon.zoneMap.nom"
              class="rounded-lg"
            />
          </div>
        </div>
        <div
          v-if="activeTab === 'formes'"
          class="flex space-x-4 animate-fade-in-down pt-8 justify-around"
        >
          <div class="">
            <p class="font-semibold text-lg">Basique</p>
            <NuxtImg
              :src="pokemon.sprite.url"
              :alt="pokemon.nom"
              class="w-64 h-auto"
            />
          </div>
          <div class="">
            <p class="font-semibold text-lg">Shiny</p>
            <NuxtImg
              :src="pokemon.shiny.url"
              :alt="pokemon.nom"
              class="h-auto w-64"
            />
          </div>
        </div>
      </div>
    </div>
    <div v-else>
      <p>Loading...</p>
    </div>
  </div>
</template>
<style>
@keyframes fade-in-down {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
.animate-fade-in-down {
  animation: fade-in-down 0.5s ease-out;
}
</style>
