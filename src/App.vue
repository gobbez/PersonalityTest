<script setup>
import { ref } from 'vue'
import FormPersonality from './components/FormPersonality.vue'

const lingua = ref('en') // default language

function cambiaLingua(nuovaLingua) {
  lingua.value = nuovaLingua
}
</script>

<template>
  <div>
    <!-- Languages Bar -->
    <div class="flex justify-end p-4">
      <button @click="cambiaLingua('it')" type="button" class="p-2 rounded hover:bg-gray-200">
        <img
          src="/images/logoita.png"
          alt="Italiano"
          class="h-6 w-6 object-contain"
          loading="lazy"
        />
      </button>
      <button
        @click="cambiaLingua('en')"
        type="button"
        class="p-2 rounded hover:bg-gray-200"
        style="margin-left: 12px"
      >
        <img
          src="/images/logoeng.png"
          alt="English"
          class="h-6 w-6 object-contain"
          loading="lazy"
        />
      </button>
    </div>

    <!-- Content -->
    <div class="center">
      <FormPersonality :lingua="lingua" />
    </div>
  </div>
</template>

<style scoped></style>
