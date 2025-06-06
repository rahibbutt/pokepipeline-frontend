<template>
  <div class="min-h-screen bg-gray-100 p-6">
    <h1 class="text-3xl font-bold mb-6 text-center text-indigo-600">Pokemon List</h1>

    <!-- Container -->
    <div class="w-full flex justify-center mt-6 ml-48">
      <div class="flex flex-col sm:flex-row items-center gap-3 max-w-4xl w-full px-4">

        <!-- Search Input -->
        <input
          v-model="searchTerm"
          type="text"
          placeholder="Search Pokémon by name..."
          class="flex-1 min-w-[200px] max-w-sm px-4 py-2 border border-gray-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-indigo-500 transition duration-150"
        />

        <!-- Trigger Button -->
        <button
          @click="handleClick"
          :disabled="isLoading"
          class="flex-shrink-0 px-6 py-2 flex items-center justify-center gap-2 font-semibold rounded-lg shadow-md transition duration-200 focus:outline-none focus:ring-2 focus:ring-offset-2 text-white disabled:cursor-not-allowed whitespace-nowrap"
          :class="isLoading ? 'bg-gray-400' : 'bg-blue-600 hover:bg-blue-700 focus:ring-blue-500'"
        >
          <svg
            v-if="isLoading"
            class="animate-spin h-5 w-5 text-white"
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
          >
            <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4" />
            <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8v4a4 4 0 00-4 4H4z" />
          </svg>
          <span>{{ isLoading ? 'Triggering...' : 'Trigger Pipeline' }}</span>
        </button>

        <!-- Status Message -->
        <div class="min-w-[160px] h-5 text-left flex-shrink-0">
          <p
            class="text-sm font-medium transition-opacity duration-300"
            :class="[
          statusMessage ? 'opacity-100 visible' : 'opacity-0 invisible',
          statusSuccess ? 'text-green-600' : 'text-red-600'
        ]"
          >
            {{ statusMessage || '‎' }} <!-- Invisible fallback character -->
          </p>
        </div>
      </div>
    </div>









    <div
      class="px-4 sm:px-6 lg:px-8 grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 xl:grid-cols-5 gap-6 mt-6"
    >
      <div
        v-for="pokemon in filteredPokemons"
        :key="pokemon.id"
        class="bg-yellow-100 rounded-2xl shadow-xl p-4 w-full max-w-xs border-4 border-yellow-400 relative hover:shadow-2xl transition"
      >
        <!-- Header -->
        <div
          class="flex justify-between items-center bg-yellow-300 px-3 py-1 rounded-t-lg border-b border-yellow-500"
        >
          <h2 class="text-lg font-bold capitalize text-gray-800">{{ pokemon.name }}</h2>
          <span class="text-sm font-semibold text-red-600"> HP {{ getHP(pokemon) }} </span>
        </div>

        <!-- Image -->
        <div
          class="flex justify-center items-center bg-white rounded-lg p-3 mt-2 mb-4 shadow-inner"
        >
          <img :src="pokemon.sprite_url" :alt="pokemon.name" class="w-28 h-28" />
        </div>

        <!-- Types -->
        <div class="mb-2">
          <span class="block text-xs font-semibold text-gray-600 uppercase">Types</span>
          <div class="flex flex-wrap gap-1 mt-1">
            <span
              v-for="(type, index) in pokemon.types"
              :key="index"
              class="bg-green-200 text-green-800 text-xs px-2 py-1 rounded-full"
            >
              {{ type.name }}
            </span>
          </div>
        </div>

        <!-- Abilities -->
        <div class="mb-2">
          <span class="block text-xs font-semibold text-gray-600 uppercase">Abilities</span>
          <div class="flex flex-wrap gap-1 mt-1">
            <span
              v-for="(ability, index) in pokemon.abilities"
              :key="index"
              class="bg-purple-200 text-purple-800 text-xs px-2 py-1 rounded-full"
            >
              {{ ability.name }}
            </span>
          </div>
        </div>

        <!-- Stats -->
        <div class="mb-2">
          <span class="block text-xs font-semibold text-gray-600 uppercase">Stats</span>
          <ul class="text-sm text-gray-700 space-y-1 mt-1">
            <li
              v-for="(stat, index) in pokemon.stats.filter((s) => s.name !== 'hp')"
              :key="index"
              class="flex justify-between"
            >
              <span class="capitalize">{{ stat.name }}:</span>
              <strong>{{ stat.base_stat }}</strong>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref, computed } from 'vue'
import axios from 'axios'
import { API_BASE_URL } from '@/globals.ts'
const isLoading = ref(false)
const statusMessage = ref('')
const statusSuccess = ref(true) // for coloring the message
const searchTerm = ref('')

interface Pokemon {
  id: number
  name: string
  sprite_url: string
  types: { name: string; slot: number }[]
  abilities: { name: string; is_hidden: boolean; slot: number }[]
  stats: { name: string; base_stat: number }[]
}

const pokemons = ref<Pokemon[]>([])

const filteredPokemons = computed(() => {
  const term = searchTerm.value.toLowerCase()
  return pokemons.value.filter((pokemon) => pokemon.name?.toLowerCase().includes(term))
})

const getHP = (pokemon: Pokemon): number | string => {
  const hpStat = pokemon.stats.find((stat) => stat.name === 'hp')
  return hpStat?.base_stat ?? '?'
}

const fetchPokemons = async () => {
  try {
    const res = await axios.get(`${API_BASE_URL}/api/pokemon`)
    pokemons.value = res.data
    console.log('Fetched Pokémons:', res.data)
  } catch (err) {
    console.error('Error fetching Pokémon:', err)
    statusMessage.value = 'Failed to fetch Pokémons.'
    statusSuccess.value = false
  }
}

const handleClick = async () => {
  try {
    isLoading.value = true
    statusMessage.value = ''

    const res = await axios.get(`${API_BASE_URL}/api/trigger-pipeline`)
    pokemons.value = res.data

    if (res?.data?.status === "OK") {
      await fetchPokemons()
      statusMessage.value = 'Pipeline triggered successfully!'
      statusSuccess.value = true
    } else {
      statusMessage.value = 'Pipeline trigger failed.'
      statusSuccess.value = false
    }

    // ⏱️ Auto-hide the message after 3 seconds
    setTimeout(() => {
      statusMessage.value = ''
    }, 3000)

  } catch (err) {
    console.error('Error Triggering pipeline:', err)
    statusMessage.value = 'An error occurred while triggering.'
    statusSuccess.value = false

    // ⏱️ Auto-hide the message after 3 seconds
    setTimeout(() => {
      statusMessage.value = ''
    }, 3000)

  } finally {
    isLoading.value = false
  }
}




onMounted(async () => {
  await fetchPokemons()
})
</script>
